---
title: 自己实现简单的Array类
categories: C++
date: 2018-04-09 21:21:29
tags:
- 操作符重载
- 复制构造函数
- 类模板
- 深复制和浅复制
---

自己学习设计一个类，在一个类中，综合各种操作，包括操作符重载，拷贝构造函数，默认构造函数，模板技术，还有深复制和浅复制的问题等，综合起来，感觉收获良多。

# 设计类框架

1. 一个数组应支持各种类型，即应用到模板技术

   ```C++
   template <class T>
   class MyArray{
     
   };
   ```

2. 一个数组什么操作都需要知道容量，当前元素数量，还有数组本身

   ```C++
   template <class T>
   class MyArray{
     public:
     	int m_size;//当前元素数量
     	int m_capacity;//数组容量
     	T* pAddr;//保存数组
   };
   ```

3. 对于一个类，基础的构造和析构。

   构造：给数组分配大小，置初值

   析构：收回分配出去的内存

   ```C++
   //构造函数
   MyArray(int capacity=20){
     this->m_capacity=n;
     this->m_size=0;
     this->pAddr=new T[this->m_capacity];
   }
   /*
   当定义一个MyArray对象时，使用如下语句
   MyArray<int>arr;
   将自动将数组大小设置为20，因为使用了默认参数20
   若使用
   MyArray<int>arr(40);
   将设置数组大小为40；
   */
   ```

   ```C++
   //析构函数
   ~MyArray(){
     if(this->pAddr!=NULL)
       delete [] pAddr;
   }
   /*
   当执行动态申请的内存释放时，要使用对应的格式
   {
     type * addr=new type[20];
     delete [] addr;
   }
   {
     type * addr=new type;
     delete addr;
   }
   */
   ```

4. 现在到数组的操作了

   数组操作包括：插入元素，修改元素，存取元素，删除元素等。

   ```C++
   template <class T>
   class MyArray{
   	MyArray(int n=20);
     	MyArray(const MyArray<T> & arr);//拷贝构造函数，用于另一个对象初始化新定义的对象
     	T& operator [] (int index);//重载[]运算符，对数组元素取值 
     	MyArray<T>& operator=(const MyArray<T> & arr);//重载=运算符，也用于直接用对象赋值。
     	void PushBack( T &data);//尾部插入元素，一个&，对变量取引用
     	void PushBack( T && data);//两个&&，直接对值取引用
     	T& PopBack();//弹出末尾元素
     	~MyArray();
   };
   ```

# 类方法的实现

1. 拷贝构造函数

   ```C++
   MyArray(const MyArray<T> & arr)
   {
   		this->m_size = arr.m_size;
   		this->m_capacity = arr.m_capacity;
   		this->pAddr = new T[this->m_capacity];
   		for (int i = 0;i < this->m_size;i++)
   		{
   			this->pAddr[i] = arr.pAddr[i];
   		}
   }
   ```

   也就是新开辟一个内存空间，将原有对象的所有东西，全部搬过来

2. 重载`[]`运算符

   ```C++
   T& operator [] (int index)
   {
   		return this->pAddr[index];
   }
   ```

3. 重载`=`运算符

   `=`运算符用来对类对象进行赋值，也可以对单个元素改变值，这里要做的是对整个对象赋值。

   因为`=`可以连着使用，比如`a=b=c`，所以要返回的是一个对象类型

   ```C++
   MyArray<T>& operator=(const MyArray<T> & arr)
   {
   		if (this->pAddr != NULL)
   		{
   			delete[] this->pAddr;
   		}
   		this->m_size = arr->m_size;
   		this->m_capacity = arr->m_capacity;
   		this->pAddr = new T[this->m_capacity];
   		for (int i = 0;i < this->m_size;i++)
   		{
   			this->pAddr[i] = arr->pAddr[i];
   		}
   		return *this;
   }
   ```

   每个类都有一个`this`指针，指向自己。

4. 插入元素

   ```C++
   void PushBack( T &data)
   {
   		if (this->m_size >= this->m_capacity)
   			return;
   		this->pAddr[this->m_size] = data;
   		this->m_size++;
   }
   void PushBack( T && data) 
   {
   		if (this->m_size >= this->m_capacity)
   			return;
   		this->pAddr[this->m_size] = data;
   		this->m_size++;
   }
   ```

​	在这里只是简单的实现，当内存空间不够大不足以容下新插入的数据的时候，就提示错误，程序返回。而真正要实现，要采用动态数组的方法，动态申请内存，当原来内存不足时，申请一块更大的内存，将原来的数据拷贝过来，并释放以前的内存（STL vector正是这样做的）。

## __深复制与浅复制的问题__

当赋值的时候碰到了指针或者引用的时候，这时候影响就比较大了。

* 所谓的深拷贝就是每个对象的指针都指向各自的内存区域，当赋值的时候 ，给新指针申请一块独立的内存，将数据拷贝过来。这样释放对象指针内存执行析构的时候，一个不会影响一个，当然，这样做的效率是比较低的。取决于具体的问题
* 而浅拷贝就是只复制指针的地址，这样新指针和旧指针指向的是同一块内存区域，释放的时候，一个释放了另一个也就释放了。

这个问题在类中还是比较重要的：

当类中有指针时，当用一个对象调用复制构造函数的时候，如果不特殊处理，执行的就是浅复制，而当两个对象生命周期结束的时候，自动调用析构函数，无意间，就会对这个指针区域两次释放，这样的结果是不确定的，危险的。



# 代码

```C++
#include <iostream>
using namespace std;

template <class T>
class MyArray
{
public:
	//构造函数

	MyArray(int n=20) {
		this->m_capacity = n;
		this->m_size = 0;
		this->pAddr = new T[this->m_capacity];
	}
	MyArray(const MyArray<T> & arr)
	{
		this->m_size = arr.m_size;
		this->m_capacity = arr.m_capacity;
		this->pAddr = new T[this->m_capacity];
		for (int i = 0;i < this->m_size;i++)
		{
			this->pAddr[i] = arr.pAddr[i];
		}
	}
	T& operator [] (int index)
	{
		return this->pAddr[index];
	}
	MyArray<T>& operator=(const MyArray<T> & arr)
	{
		if (this->pAddr != NULL)
		{
			delete[] this->pAddr;
		}
		this->m_size = arr->m_size;
		this->m_capacity = arr->m_capacity;
		this->pAddr = new T[this->m_capacity];
		for (int i = 0;i < this->m_size;i++)
		{
			this->pAddr[i] = arr->pAddr[i];
		}
		return *this;
	}
	void PushBack( T &data)
	{
		if (this->m_size >= this->m_capacity)
			return;
		this->pAddr[this->m_size] = data;
		this->m_size++;
	}
	void PushBack( T && data) 
	{
		if (this->m_size >= this->m_capacity)
			return;
		this->pAddr[this->m_size] = data;
		this->m_size++;
	}
	T& PopBack()
	{
		if (this->m_size == 0)
		{
			cerr << "数组为空" << endl;
			exit(0);
		}
		this->m_size--;
		return this->pAddr[this->m_size+1];
	}
	~MyArray(){
		if(this->pAddr!=NULL)
		delete [] this->pAddr;
	}
public:
	int m_size;
	int m_capacity;
	T* pAddr;
};
void test()
{
	MyArray<int> arr(10);
	arr.PushBack(10);
	arr.PushBack(20);
	arr.PushBack(40);
	arr.PopBack();
	for (int i = 0;i < arr.m_size;i++)
	{
		cout << arr[i]<<" ";
	}
	cout << endl;
}
void test1()
{
	MyArray<char> arr;
	arr.PushBack(65);
	arr.PushBack(66);
	arr.PushBack(67);
	arr.PushBack(68);
	arr.PopBack();
	MyArray<char> arr2(arr);
	for (int i = 0;i < arr2.m_size;i++)
	{
		cout << arr2[i] << " ";
	}
	cout << endl;
}
int main()
{
	test();
	test1();
	system("pause");
	return 0;
}
```

