[TOC]



# C++STL标准库

## 使用C++标准库

### 标准库的形式

1. C++标准库的头文件不带h	例如 #include<vector>
2. **新式**C的头文件也不带h，但在前面带个c    例如 #include<cstdio>
3. **旧式**C的头文件仍可以用    例如#include<stdio.h>

### 六大部件

### 容器 （Containers）

#### 序列式容器

每个元素都有固定的位置，取决于插入时间和地点，和元素值或类型都无关

例如：vector，deque，list， stack， queue。

##### vector容器

###### 简介：

- vector是将元素置于一个`动态数组`中加以管理的容器
- vector可以随机存取元素（支持索引值直接存取，用[]操作符或at()方法）
- vector尾部添加或移除元素非常快速，但在中部或头部插入元素或移除元素比较费时

###### 默认构造

采用模板类实现

```c++
vector<T> vecT;

vector<int> vecInt;			//一个存放int的vector容器
vector<float> vecFloat;		//一个存放float的vector容器
vector<string> vecString;	//一个存放string的vector容器

class CA{}；
vector<CA*> vecpCA;			//存放CA对象的指针的vector容器
vector<CA> vecCA;			//用于存放CA对象的vector容器
```

###### 带参数构造

- vector(begin, end);		//构造函数将（begin，end）区间的元素拷贝给本身
- vector(n, elem);             //构造函数将n个elem拷贝给本身
- vector(const vector &vec);//拷贝构造函数

###### 赋值

- vector.assign(begin, end);	//将(begin, end)区间的数据拷贝复制给本身(不能直接为数字)
- vector.assign(n, elem);        //将n个elem拷贝赋值给本身
- vector& operator = (const vector &vec); //重载等号操作符
- vector.swap(vec);                //将vec与本身的元素互换

###### 大小

- vector.size();						//返回容器中元素的个数
- vector.empty();                    //判断容器是否为空
- vector.resize(num);             //重新指定容器长度为num，若容器变长，则以默认值填充新位置；若容器变短，则末尾超出容器长度的元素会被删除
- vector.resize(num, elem);   //重新指定容器长度为num，若容器变长，则以elem填充新位置；若容器变短，则末尾超出容器长度的元素会被删除

###### 访问方式

1. vector[index]						//下标法，就像数组一样访问vector容器的值。（注意，如果下标越界，可能会造成程序异常终止，且没有提示）
2. vector.at(index);                   //返回索引index所指的数据，如果越界，会抛出异常（out of range）

###### 插入/移除

- vector.push_back(num); 	 //从末尾插入一个元素
- vector.pop_back();               //删除末尾的一个元素
- vector.insert(pos, elem);      //在pos位置插入一个elem元素（注意第一个参数不能为下标，应该为**指针**）例如：vector.insert(vector.begin() + 3, 100)
- vector.insert(pos,n,elem);    //在pos位置插入n个elem元素，无返回值
- vector.insert(pos,begin,end); //在pos位置插入[begin,end)区间的数据,无返回值

```c++
int a[] = {1,2,3,4,5};
int b[] = {10,20,30,40,50};
vector<int> v1;
v1.assign(a,a + 5);//v1为[1,2,3,4,5];
v1.insert(v1.begin()+2, b+1, b+4);
//此时v1为[1,2,20,30,40,3,4,5];
```

###### 迭代器的使用

- vector<int>::iterator iter		//变量名为iter
- vector容器的迭代器属于“随机访问迭代器”：迭代器一次可以移动多个位置







#### 关联式容器

元素位置取决于特定的排序准则，和插入时间地点无关

例如：set， multiset， map， multimap。



### 分配器 （Allocators）

### 算法 （Algorithms）

### 迭代器 （Iterators）

#### 概念

迭代器是一种检查容器内元素并且遍历容器内元素的**数据类型**

#### 作用

迭代器提供对一个容器中的对象的访问方法，并且定义了容器中对象的范围，这样就通过迭代器统一了对所有容器的访问方式，就不需要记住每个容器的访问方式了

#### 迭代器失效

1.插入元素失效









### 适配器 （Adapters）

### 仿函数 （Functors）









## 认识C++标准库











## 良好使用C++标准库













## 扩充C++标准库

