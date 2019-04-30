title: C++的输入与输出
categories: C++
data: 2018-04-02 12:20:00
tags:
- C++格式输出
- 输入输出警告
------------
# C++的输入与输出

## 流和缓冲区

### 流
&ensp;&ensp;C++程序把输入和输出看作字节流。输入时，程序从输入流中抽取字节；输出时，程序将字节插入到输出流中。   
输入流可能来自键盘，也可能来自于硬盘。同样输出流也可以流向其他地方，如屏幕，打印机等。C++程序只检查字节流，而   
不关心其去向。因此，输入流需要两个连接：   
*  将流和输入去向的程序连接起来。   
*  将流和文件连接起来。   
   同样，对输出的管理也包括两步，将流连接到程序或者输出目标。   
### 缓冲区
&ensp;&ensp;为了高效的处理输入和输出，使用缓冲区。缓冲区是用作中介的内存块，将数据从程序传到设备或反过来的临时存储工具。   
程序每次从硬盘读取一个数据块，将其放入缓冲区，然后，从缓冲区每次读取一个字节。当读取完毕，程序读取另一个内存块。输出时，程序   
先填满缓冲区，然后将数据块传输到硬盘，并清空缓冲区，待下一次使用.   
**C++定义了一些类来管理流和缓冲区**   
![](https://camo.githubusercontent.com/ea5d0fd9cc2f137f6112b084fbed32d2009aaec6/687474703a2f2f3778726c75392e636f6d312e7a302e676c622e636c6f7564646e2e636f6d2f432b2b5f496e7075744f75747075742e676966)
使用这些工具，必须使用适当的对象。创建对象将打开一个流，并自动创建缓冲区，与流连接。   

-----------------------------
程序包含`iostream`文件，自动创建8个流对象，常用的输入和输出对象   
`cin` 用于处理标准输入流.   
 `cout` 用于处理标准输出流.   

 ## 使用`cout`进行输出

**通常使用`cout`&`<<`结合**   
 运算符`<<`被重载，使它能够识别所有的基本类型。此操作符被重载的原型为   
  `ostream & opreate<<(type)`   
  `<<`运算符重载的返回类型都是`ostream &`，一个对象的引用，也就是调用该运算符的引用，所有，   
  可以使用其进行拼接输出   
  `cout<<5<<"cd"<<endl;`   
### 其他ostream方法
1 `cout.put(5).put('T');`   

  `put` 方法，将所有合法类型转换为字符类型。而且，返回的依旧是指向该引用的对象.   

2 `cout.write(char *ptr,size);`

  `write`方法，输出字符串，第一个参数：字符串的地址，第二个参数：要显示的字符数。而且，返回的是`ostream &`.  

### 使用`cout`格式化输出   

#### 修改显示计数系统
**1  进制** 
```
hex(cout);//将格式设为16进制
dec(cout);//十进制
oct(cout);//八进制
```
**使用影响下面，请恢复回来**   
**2 字段宽度**
```
int width();//返回当前宽度
int width(size);//设置宽度

////注意：width只改变下一个项目.
```
**3 填充字符**   
`cout.fill('*')//以*号填充空白`
**将会一直有效**   
**4 设置浮点数精度**   
```
cout.precision(2)//设置总宽度为2
cout<<20.458<<endl;.//将只输出20.
//而且，将一直有效
```
**5 打印末尾0和小数点**   
```
cout.setf(std::ios_base::showpoint);//默认6位
cout.precision(3);
cout << 5.0 << endl;//输出5.00
结合precision(),使用，效果更佳.
```



## 使用`cin`进行输入

`cin`也重载了基本类型，可以直接进行输入,通常  结合`>>`运算符使用 
```C++
int a;char b;char *ptr;
istream & opreate(type &);
cin>>a;
cin>>b;
cin>>ptr;
```
`cin`返回`istream`对象，故也可进行拼接输入   
**`cin`检查输入流**   
```C++
char ptr[20];
int distance;
char ch;
cin>>ptr>>distance>>ch;
cout<<ptr<<endl<<distance<<endl<<ch<<endl;
```
不进行初始化的结果
![](http://upload.ouliu.net/i/20171210230308lpdjf.png)
初始化后预期结果正常
![](http://upload.ouliu.net/i/20171210230416qq0d6.png)
正常输入，结果正常
![](http://upload.ouliu.net/i/20171210230506lcnu8.png)

**再次意识到初始化的重要性,数组只要不越界，可以正常输入输出** 

**`cin`一直读取，知道遇到(空格，换行或制表符)或第一个与类型不符的为止**  

`cin`返回的是个对象，在循环中,当输入不匹配，将返回一个false;

```C++
int ele;
cin>>ele;
//输入 -123Z，输出-123
```
![](http://upload.ouliu.net/i/201712102314573wjzs.png)

## 其他`istream`方法   
**1 `get()`**   
单字符输入
```C++
char ch;
cin.get(ch);//或者ch=cin.get();
//与C语言getchar() 类似,只读取一个字符
//但cin.get(&)与cin.get(),不同的是，前一个返回一个对象，后一个返回一个整型的值
```
**2 `getline(),get()`**
字符串输入
```C++
iostream &get(char *,int ,char);//数组，长度，结尾标志
iostream &get(char *,int );
iostream &getline(char *,int ,char);
iostream &getline(char *,int );
```
```C++
char ptr[20];
cin.getline(ptr,20);
//getline在输入流中保存空格，直到遇到第一个回车或者第19个字符.结束输入.
//get()方法，与getline方法类似
```
* 其他的还有`read(),peek(),putback()`等方法   
```C++
//read() 通常和write() 函数结合使用，一般用于处理文件
//peek() 用于检查下一个输入的字符串
char ptr[20];
char ch;
int i=0;
while((ch=cin.peek())!='#')
  cin.get(ptr[i++]);
```
### 特别注意一点，防止数组越界的问题


