[TOC]



## 范围for循环

### 前言

C++11引入的新特性，此for循环只适用于支持迭代器的容器，例如数组、vector、set、map等，而不能用于普通的指针或者基本数据类型。

### 普通for循环

语法规则

```c++
for (initialization; condition; increment) {
    // loop body
}
```

这个循环内容就不多讲了

### 范围for循环

语法规则

```c++
for (auto& element : mySet) {
    // loop body
}
```

- 第一个元素，auto& element是循环变量，表示每次循环赋值给element
- 第二个元素，mySet是定义的set容器，表示遍历此容器的元素

#### set容器实例

```c++
std::set<int> mySet = { 10, 20, 5, 30 };
for (auto& element: mySet) {
	std::cout << element << " ";
}
```

如果不用范围for循环，可以使用迭代器进行遍历

#### 对比

```c++
for (auto it = mySet.begin(); it != mySet.end(); ++it) {
    std::cout << *it << std::endl;
}
```

范围for循环以一个冒号作为分割

普通for循环以两个分号作为分割

#### 数组实例

一般遍历数组都是用普通for循环，但你知道使用范围for循环吗

范围for循环遍历数组

```c++
	int arr[4] = { 1,2,3,4 };

	for (auto& element: arr) {
		std::cout << element << " ";
	}
```



#### 数组优缺点

传统for循环

优点：可以更好地控制循环的细节，例如可以自定义循环变量的初始值、循环结束的条件等，还可以在循环体内使用break，continue，改变循环的执行流程。

范围for循环

优点：相比传统的for循环更加简洁明了，可以避免一些常见的陷阱，例如数组越界、循环变量类型错误等问题。也可以自动推导元素的类型。范围for循环通常使用在简单的遍历中，复杂的还是用传统的for循环吧。