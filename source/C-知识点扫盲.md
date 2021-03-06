---
title: C++知识点扫盲
categories: C++
date: 2018-03-30 21:12:44
tags:
- 面向对象编程
- C++数据类型
- 引用
- 函数
- 函数默认参数
- 函数重载
---

面向过程的编程，C语言典范已经够OK了，用C++的话，我想还是将**面向对象的思维**融入每一个思考过程中。

# 面向对象思想

我的理解： 

对象就像是自然界存在的某一个具体的东西，这个东西可以有形状、与重量、可以呼吸、可以跑、可以跳，而这些过程，我们称为对象的方法。而一系列这些有形状、会呼吸的这些实体聚集在一起，就形成了**类**。将具体的一类东西抽象称为一种数据类型，而将具体的东西抽象为属于这一类东西的实例，在编程的时候，运用这一思想，把具体问题抽象，把其特性、活动全部集合起来（数据的抽象和封装），构成一个类。

这种思维是锻炼出来的，要靠感觉去运用，这篇文章[面向对象思想](https://blog.csdn.net/longhuihu/article/details/5584423)

面向对象的特性

* 抽象
* 封装和数据隐藏
* 多态和继承
* 代码重用

# C++数据类型

## 内置类型

内置的基本类型：布尔、字符、整形、浮点、双精度浮点、无符号类型、宽字符类型。

![](https://i-msdn.sec.s-msft.com/dynimg/IC610193.jpeg)

## 自定义类型/复合类型

符合类型有：数组、结构体、字符串、结构体、共用体、指针、类。这些类型在使用的时候，需要手动分配内存。

关于内存的分配，见[C++内存分配](http://jacky-dai.iteye.com/blog/2320566)

# 引用

C++为了淡化指针的概念，引入了**引用**的概念，引用是给变量起了一个别名，也就是两个变量共用同一块内存，当对引用进行操作，就是直接对原变量进行操作。

**引用的作用**

引用的主要作用就是当作函数的参数进行传址传参，这样的话，与传值传参的区别是，不会产生这个原变量的副本，大大提高了效率。由于直接是对原变量进行操作，所以总会有意识没意识的修改了值，当不希望被修改时，可以用const修饰符。

# 函数

函数可以说是一个程序的灵魂，在C++中，我们更习惯称为方法，也就是完成特定功能的代码块，函数的命名，应该具有意义。

**内联函数**

用`inline`关键字修饰的函数，称作内联函数。内联函数的好处是，在程序执行的时候，直接将函数的代码搬过来展开，直接执行，省去了寻找函数的过程，故增加程序执行的速度，但随之的缺点就是，增加内存的消耗。又是典型的空间还换时间的例子。

```C++
inline int MAX(int a,int b){
 	return a>b?a:b; 
}
```



**默认参数函数**

默认参数指函数调用忽略了实参时而默认使用的一个值，是为了编程方便定义的。

```C++
double pow(float a,int b=2){
  double result=1;
  for(int i=0;i<b;i++){
  result*=a;
}
  return result;
}
```





注意：

* 添加默认值，从右向左，就是某个变量默认，它右边的变量都必须默认参数。
* 不能跳过某个实参给它右边的参数赋值。
* 只能在声明时，使用默认参数。

**函数重载**

函数重载允许同样的函数名做不同的事情，当函数的名称相同而参数类型和参数个数不同的时候，这样的函数就被称为重载函数。当在调用函数的函数，会根据你传进去的参数，匹配你到底想调用哪个函数。

```C++
void print(int a){
  std::cout<<a<<endl;
}
void print(double){
  std::cout<<endl;
}
void print(std::string a){
  std::cout<<a<<endl;
}
```



特点：

* 函数名相同
* 参数个数或类型不同
* 仅返回值不同的函数，无法区分重载
* 重载的函数，位于相同的作用域

