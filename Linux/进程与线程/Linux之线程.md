## 线程

一个进程中的多个线程共享以下资源：

- 可执行的指令
- <u>静态数据</u>
- 进程中打开的<u>文件描述符</u>
- 当前的<u>工作目录</u>
- 用户ID
- 用户组ID

每个线程中的私有资源包括：

- 线程ID（TID）
- PC（程序计数器）和相关寄存器
- <u>堆栈</u>
- 错误号（errno）
- 优先级
- 执行状态和属性

### 创建线程

![image-20230619190513451](https://github.com/Scholar618/Learning_Notes/blob/main/Linux/image_20230619190513451.png)

成功时返回0，失败时返回错误码，看的不太懂？直接上例子

```c
#include <stdio.h>
#include <pthread.h>
#include <unistd.h>

int *testThread(char *arg) {
    printf("This is the thread test!\n");
    return NULL;
}

int main() {
    pthread_t tid;
    int rel;
    rel = pthread_create(&tid, NULL, (void*)testThread, NULL);
    printf("This is the main thread!\n");
    sleep(1);
    return 0;
}

```

结果:

![image-20230619192353463](https://github.com/Scholar618/Learning_Notes/blob/main/Linux/image_20230619165043509.png)

注意：主进程的退出，创建的线程也会退出，线程创建需要事件，如果主进程马上退出，那线程不能得到执行

### 线程结束

```c
void pthread_exit(void *retval);
```

> 结束当前进程
> retval可被其他线程通过pthread_join获取
> 线程私有资源被释放

### 线程查看TID

第一种方法：

```
pthread_t pthread_self(void)
```

第二种方法：

通过pthread_create函数中的第一个参数。

### 线程的参数传递

例如上个代码：

```c
#include <stdio.h>
#include <pthread.h>
#include <unistd.h>

int *testThread(char *arg) {
    printf("This is the thread test!\n");
    return NULL;
}

int main() {
    pthread_t tid;
    int rel;
    rel = pthread_create(&tid, NULL, (void*)testThread, NULL);
    printf("This is the main thread!\n");
    sleep(1);
    return 0;
}

```

想将主函数的参数传递到testThread函数中，

```c
#include <stdio.h>
#include <pthread.h>
#include <unistd.h>

int *testThread(void *arg) {
    printf("This is the thread test!\n");
    // arg先强转为int*型，在前面加*解引用获取整数值
    printf("input arg = %d\n", *(int*)arg);
    return NULL;
}

int main() {
    pthread_t tid;
    int rel;
    int arg = 5; // 想传递arg整形
    // &arg变成int*arg类型，再强转变为void*arg
    rel = pthread_create(&tid, NULL, (void*)testThread, (void*)&arg);
    printf("This is the main thread!\n");
    sleep(1);
    return 0;
}

```

### 线程的回收

对于一个默认属性的线程来说，线程占用的资源并不会因为执行结束而得到释放，所以需要对线程进行回收

```c
int pthread_join(pthread_t thread, void **retval);
```

> - 成功时返回0，失败时返回错误码
> - thread要回收的线程对象
> - 调用线程阻塞直到thread结束
> - *retval接收线程thread的返回值

pthread_join是阻塞函数，如果回收的线程没有结束，则会一直等待

例如代码：

```c
#include <stdio.h>
#include <pthread.h>
#include <unistd.h>

void *func(void *arg) {
    printf("This is child thread!\n");
    sleep(1);
    pthread_exit("Thread return!");
}

int main() {
    pthread_t tid;
    void *retv;
    // 先创建，再回收
    pthread_create(&tid, NULL, func, NULL);

    pthread_join(tid, &retv);
    printf("Thread ret = %s\n",(char*)retv);

    sleep(1);
}
```

则会先执行线程，等待线程结束后才会执行pthread_join

### 线程分离

方法一：

```c
int pthread_detach(pthread_t thread);
```

> 成功时返回0，失败时返回错误号
>
> 指定该状态，线程主动与主控线程断开关系，线程结束后，不会产生僵尸进程

示例代码：

```c
#include <stdio.h>
#include <pthread.h>
#include <unistd.h>

void *func(void *arg) {
    pthread_detach(pthread_self()); // 另一种方式
    printf("This is child thread!\n");
    sleep(1);
    pthread_exit("Thread return!");
}

int main() {
    pthread_t tid[5];
    void *retv;
    // 先创建，再回收
    for(int i = 0; i < 5; i++) {
        pthread_create(&tid[i], NULL, func, NULL);
    //  pthread_detach(tid); // detach实现线程回收
    }
    while(1) {
        sleep(1);
    }
}
```

方法二：

创建线程时设置为分离属性

```c
pthread_attr_t attr;
pthread_attr_init(&attr);
pthread_attr_setdetachstate(&attr, PTHREAD_CREATE_DETACHED);
```

