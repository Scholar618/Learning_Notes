# Linux之进程

## 进程

### 进程概念

网上很多请自行了解

### 创建子进程

使用fork函数创建，如下例程序

```c
#include <stdio.h>
#include <unistd.h>

int main() {
	pid_t pid;
	printf("before fork pid = %d\n", (int)pid);
    // 创建子进程
	pid = fork();

	if(pid > 0) { // 父进程
		printf("This is father process\n");
	} else if(pid == 0) { // 子进程
		printf("This is child process\n");
	} else { // 出错了
		perror("fork");
		return 0;
	}
						
	printf("pid = %d\n", (int)pid);
	return 0;
}

```

### 父子进程

- 子进程会继承父进程的全部内容
- 子进程与父进程是两个不同的内存空间，互不影响
- 若父进程先结束了，子进程就会变成孤儿进程了
- 若子进程先结束，而父进程没有及时回收，子进程会变成僵尸进程

当子进程变成孤儿进程后，被**init进程**收养，子进程变成后台进程

### exec函数族

进程调用exec函数执行某个程序，进程当前内容会被指定的程序替换，实现让父子进程执行不同的程序

创建进程实现某种命令：

**execl / execlp**

```c
int execl(const char *path, const char *arg, NULL);
int execlp(const char *file, const char *arg, NULL);
```

> 成功时返回指定程序，失败时返回EOF
> path 执行的程序名称，包含路径
> arg 传递给执行的程序的参数列表
> file 执行的程序的名称，在PATH中查找

例如：执行ls命令，显示/etc目录下的所有文件的详细信息

```c
if(execl("/bin/ls", "ls", "-a", "-l", "/etc", NULL) < 0) {
	perror("execl");
}
if(execlp("ls", "ls", "-a", "-l", "/etc", NULL) < 0) {
	perror("execlp");
}
```

**execv / execvp**

```c
int execv(const char* path, char* const argv[]);
int execvp(const char*file, char* const argv[]);
```

> 成功时执行指定程序，失败时返回EOF
> arg封装成指针数组的形式

例如：执行ls命令，显示/etc目录下的所有文件的详细信息

```c
char *arg[] = {"ls", "-a", "-l", "/etc", NULL};

if(execv("/bin/ls", arg) < 0) {
	perror(“execv”);
}
if(execvp("ls", arg) < 0) {
	perror("execvp");
}
```

**system**

```c
int system(const char* command);
```

> 成功时返回命令command的返回值，失败时返回EOF
> 当前进程等待command执行结束后才继续执行

例如：执行ls命令，显示/etc目录下的所有文件的详细信息

```c
if(system("ls -a -l /etc") < 0) {
    perror("system");
}
```

注意：使用exec函数时，进程当前内容会被指定的程序替换

例如：如下代码显示当前目录下的文件

```c
#include <stdio.h>
#include <unistd.h>

int main() {

    printf("Before:\n");
    if(execlp("ls", "ls", "-a", "-l", "./", NULL) < 0) {
      perror("execlp");
    }
    printf("After:\n");
}

```

运行结果：

![Screenshot](/home/sjc/图片/Screenshot.png)

会发现没有After显示，这就是因为使用了exec函数，将后面的之前程序替换了，所以不会显示之后的运行结果。

### 守护进程

是Linux的后台进程，且生存周期较长，通常**独立于控制终端**并且周期性的执行某种任务或等待处理某些发生的事件

守护进程相关概念：

> 进程组（Process Group）：进程集合，每个进程组都有一个组长，其进程ID就是该进程组的ID
>
> 会话（Session）：进程组集合，每个会话有一个组长，其进程ID就是该会话组的ID
>
> 控制终端（Controlling Terminal）：每个会话可以有一个单独的控制终端，与控制终端相连接的Leader就是控制进程。

举例：http服务的守护进程叫httpd，mysql服务的守护进程叫mysqld

**守护进程的创建**

1.创建子进程、父进程退出

```c
if(fork() > 0) {
	exit(0);
}
```

> 子进程变成孤儿进程，被init进程收养
> 子进程在后台运行

2.子进程创建新会话

```c
if(setsid() < 0) {
	exit(-1);
}
```

> 子进程成为新的会话组长
> 子进程脱离原先的终端

3.更改当前工作目录（非必须）

```c
chdir("/");
chdir("/tmp");
```

> 守护进程在后台运行，其工作目录最好不被删除，就重新设定当前工作目录



4.重设文件权限掩码（非必须）

```c
if(umask(0) < 0) {
	exit(-1);
}
```

> 文件权限掩码设置为0
> 只影响当前进程

5.关闭打开的文件描述符

```
int i;
for(i = 0; i < 3; i++) {
	close(i);
}
```

> 关闭所有从父进程继承的打开文件
> 已脱离终端，stdin/stdout/stderr无法再使用了

示例程序：

```c
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <sys/stat.h>
int main() {
    // 创建一个子进程
    pid_t pid;
    pid = fork();
    if(pid < 0) {
        perror("fork");
        return 0;
    } else if(pid > 0){
        exit(0); // 父进程退出
    }

    printf("I am a deamon!\n");
    // 子进程创建新会话
    printf("sid = %d, pid = %d, pgid = %d\n", getsid(getpid()), getpid(), getpgid(getpid()));
    if(setsid() < 0) {
        perror("setsid");
        exit(0);
    }
    printf("after sid = %d, pid = %d, pgid = %d\n", getsid(getpid()), getpid(), getpgid(getpid()));

    // 改变工作目录
    chdir("/");

    // 设置文件权限掩码
    if(umask(0) < 0) {
        perror("unmask");
        exit(0);
    }

    // 关闭当前文件描述符
    close(0);
    close(1);
    close(2);

    printf("after close!!\n");

    sleep(100);
}
                 
```

![image-20230619165043509](/home/sjc/.config/Typora/typora-user-images/image-20230619165043509.png)





### 使用gdb调试多进程程序

```
// gdb调试多进程程序
set follow-fork-mode child    // 跟踪child进程
set follow-fork-mode parent   // 跟踪父进程
set detach-on-fork on/off     // 默认是on，off后可以跟踪俩进程
info inferiors				  // 查看你跟踪的进程
inferiors 进程序号 (1,2,3....) // 切换跟踪的进程
```



