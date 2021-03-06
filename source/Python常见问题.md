---

title: Python常见问题
categories: Python
date: 2018-11-23 00:00:00
tags:

- 生成器
- 闭包
- 对象
---


## 1.函数传参机制

1. 所有的传参都是以引用的方式传参，即传过去的值为真正内存地址中的值，并非按值传参的将实参拷贝一份进行传递。

2. 在函数中对行参的修改，都反应真正内存中的改变。

   `前提是传入的可变对象`

## 2.staticmothod和classmethod

1. 方法总共分为三类，`1.静态方法,2.类方法,3.实例方法`。

2. 静态方法：(@staticmethod())

   可以直接通过类名来调用的方法，放在类内，仅为这个类服务。如果不需要访问实例的方法和属性，便可以定义为静态方法，省去创建对象的开销

3. 类方法：(@classmethod(clc,))

   可以为类做一些预处理的功能，最常用的就是作为构造函数。

4. 实例方法：(def test(self,))

   只有创建对象之后，才可以调用的方法。

### 类变量和实例变量

1. 类变量：

   定义在所有函数之外，通过`类名.变量名访问`，对所有对象可见。用处：暂不清楚。

2. 实例变量：

   在类内，通过`self.变量名`访问，类外，直接`对象名.变量名`访问。

## 3.列表生成式

1. 快速而简单的生成一个列表

   ```python
   list_str=[x*x for x in range(1,10)]
   # 输出1.4.9.16.25....81
   #可以循环生成列表
   list_for=[x+y for x in "ABC" for y in "DEF"]
   # 输出['AD', 'AE', 'AF', 'BD', 'BE', 'BF', 'CD', 'CE', 'CF']
   ```

## 4.子符串格式化

1. **%** 格式化

   * `"I am a %s"%"boy"`

   ​     则会输出 `I am a boy`

   * `"I am a %s"%(1,2,3),报错`
   * `"I am a %s"%("boy",)`

2. **format**格式化

   ```python
   "{} am a {}".format("I","boy")
   print("{who} am a {sex}".format(who="I",sex="boy"))
   ```

## 5.*args and **kwargs

1. 如果不确定函数中传多少个参数，便可以使用`*args`

2. 若想以列表的形式传参，则使用`kwargs`

   ![具体看图](/home/youmo/Pictures/2018-11-26 10-32-19屏幕截图.png)

## 6.`__new__`和`__init__`的区别

1. `__new__`主要用来创建一个实例。`__init__`主要用来初始化一个实例。
2. 在新式类中，创建一个对象的时候，如果重载了这两个方法，则会先调用new方法，然后，调用init方法。
3. 若不在new中返回对象或者返回一个已经存在的对象，则init不会被调用。

## 7.可变对象和不可变对象

1. 不可变对象

   `int,string,float,tuple`

   改变这些玩意的值，是将这些值拷贝一份，然后，将这个变量指向一块新的内存，并不是改变原来内存中的值。

2. 可变对象

   `dict list`

## 8.GIL线程全局锁

1. Python代码的执行由Python 虚拟机(也叫解释器主循环，CPython版本)来控制，Python 在设计之初就考虑到要在解释器的主循环中，同时只有一个线程在执行，即在任意时刻，只有一个线程在解释器中运行。对Python 虚拟机的访问由全局解释器锁（GIL）来控制，正是这个锁能保证同一时刻只有一个线程在运行。
   在多线程环境中，Python 虚拟机按以下方式执行：
   1. 设置GIL
   2. 切换到一个线程去运行
   3. 运行：
       a. 指定数量的字节码指令，或者
       b. 线程主动让出控制（可以调用time.sleep(0)）
   4. 把线程设置为睡眠状态
   5. 解锁GIL
   6. 再次重复以上所有步骤
2. 在多核CPU下，想实现真正的并发，就利用多进程。

## 8.垃圾回收

1. 引用计数

   每个对象都有一个计数器，当一个对象被引用时，计数器会增加，当计数器的值为0的时候，便将此对象从内存中删除。

   实例化的时候，计数器值为1,del时，将计数器清零。

   1. 缺点

      当两个对象互相引用的时候，计数器便永远不会清零。

      维护计数器消耗资源。

2. 标记－清除

   按需分配，当没有空闲内存时，从寄存器和程序栈引用出发，遍历对象为节点，将可以访问到的标记，然后，将没有标记的对象删除。

3. 分代技术

   标记清楚，要扫描一遍内存，消耗时间大。

   将内存快根据存活时间划分为不同的集合，扫描的概率、垃圾回收的概率随着这些代的存活时间增大而减小。