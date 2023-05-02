[TOC]

# 深入理解C指针

## 1.动态内存分配函数

- malloc		在堆上分配内存

- realloc        在之前分配的内存块基础上，将内存重新分配为更大或更小的部分

- calloc         从堆上分配内存并清零

- free            将内存块返回堆 

  

### 1.1 使用malloc函数

函数原型：

```
void* malloc(size_t);
```

注意“size_t”表示无符号整数，即不能为非负数。

所以在传入参数为非负数时会引发问题。（可能返回NULL或直接报错）

#### malloc典型用法：

```
int *p1 = (int*)malloc(sizeof(int));
```

**注意**	

​	由函数原型可知，malloc返回值为void，所以分配内存时进行了强转（int*）。当然，其他类型也可以进行强转。

​	为数据类型分配指定字节数时尽量用"sizeof"操作符

这行代码执行了三个操作：

（1）从堆上分配内存 （无法分配返回NULL）

（2）内存不会被修改或清空（malloc函数特性）

（3）返回首字节地址

由（1）可知，当malloc函数分配内存时，会返回NULL，所以可以进行判断证明指针分配没有问题

```
int *p = (int*)malloc(sizeof(int));
if(p!=NULL) {
	printf("指针没有问题！");
} else {
	printf("无效的指针！！");
}
```

> 初始化静态或全局变量时不能调用函数，会引起编译错误。

例如：

```
static int *p1 = malloc(sizeof(int));
```

这条语句会产生一个错误的编译信息，全局变量也一样。

更正代码：

```
static int *p1;//先进行初始化
p1 = malloc(sizeof(int));
```



### 1.2 使用calloc函数

函数原型：

```
void *calloc(size_t numElements, size_t elementSize);
```

与malloc函数不同，calloc函数在分配的同时会清空内存。

> 清空内存指的是将其内容置为二进制0

#### calloc函数用法：

calloc函数是根据"numElements"和"elementSize"两个参数乘积来分配内存

例如：为p1分配了20个字节，全部包含0（全部清零是calloc函数特性）

```
int *p1 = calloc(5, sizeof(int));
```

使用malloc+memset有同样的效果

```;
int *p1 = malloc(5*sizeof(int))
memset(p1,0,5*sizeof(int));
```

memset函数第一个参数指向<u>要填充缓冲区的指针</u>，第二个参数是<u>填充缓冲区的值</u>，最后一个参数是<u>要填充的字节数</u>。



### 1.3 使用realloc函数

我们在使用指针时可能需要时不时增加或减少为指针分配的内存，在需要改变数组长度的时候会显得尤其有用。

函数原型:

```
void *realloc(void *ptr, size_t size);
```

函数两个参数分别是：指向原内存块的指针， 请求的大小。

请求的大小可以比当前分配的字节数小或大。

1. 如比当前分配字节数小，多余的内存还给堆，但不能保证多余的内存会被清空。

2. 如比当前分配字节数大，可能会在紧挨着的区域分配新的内存，否则在堆的其他区域分配并把旧内存复制到新区域。

   