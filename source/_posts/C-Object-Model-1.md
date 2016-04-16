---
title: C++_Object_Model_1
date: 2016-04-16 11:23:55
tags: C++对象面向观
---

### C++的额外成本
------------
> C++ 较C的最大区别：在于面向对象，类较C的<em>struct</em>不仅包含数据，同时还包含了数据的操作，在语言层面上C++还带来了面向对象的新特性:
>+ 类
>+ 继承
>+ 多态
>新特性使得C++更强大，但是同时伴随着空间布局和存取时间的额外成本。这些额外成本主要是由virtual引起的
>+ virtual function 机制，用来支持“执行期的绑定”
>+ virtual base class ---虚基类机制，以共享虚基类的subject
___________________________________

##### 三种对象模型
--------------------------------
>C++类包含两种数据成员：静态数据成员和非静态数据成员；同时包含成员函数，静态函数 和虚函数三种成员函数，这些机制在C++对象是如何被表现的？下面有三种模型可以用以表 现它们——简单对象模型、表格驱动对象模型以及C++对象模型。也许你没兴趣去了解有几种 方式可以实现C++的对象模型，只想了解C++对象模型。然则，C++对象模型是在前两种对象 模型上发展而来的，甚至于局部上直接用到前两种对象模型。

>假定有一个Point类，我们将用三种对象模型来表现它。Point类如下:
_______________________________________

```cpp

class Point
{
 public:
  Point(float xval);
  virtual ~Point();
  float x() const;
  static int PointCount();
protected:
  virtual ostream& print(ostream &os)const;
  float _x;
  static int _point_count;
 
}

```

#### 简单对象模型

------------------------------------------

> 简单对象模型：一个C++对象存储了多有指向成员的指针，而成员本身不在存储对象之中.也就说不论数据成员还是成员函数，也不轮是普通成员函数还是虚函数，他们都是存储在对象之外，同时对象保存指向他们的指针而已
![Alt text](./1.png)


><strong>简单模型的缺点：</strong>对于编译器说虽然简单，但是同时付出的代价是空间和执行期的效率，显尔易见的是对于每一成员额外都要搭上一个指针大小的空间以及对于每个成员的操作都增加了一个间接层。
><em>因此C+++并没有使用这样一个对象的模型，但是被用到C++中“指向成员的指针”的概念当中。</em> 
——————————————————————————————————————————————————————————————————————

#### 表格驱动对象模型
----------------------------------
>表格驱动模型：它将对象中的所有成都抽离出来在外建表，对象本身仅仅只是存储指向这个表的指针。下图可以明显看出，他将所有的数据成员抽离出来建成数据成员表，将所有函数抽取出来建成一张函数成员表，而对象本身只保持一个指向数据成员表的指针
![Alt text](./2.jpg)
> <em>当然C+++也没有采用这一种对象模型，但是C++却一这个模型作为支持虚函数的方案</em>
——————————————————————————————————————————————————————————————————————

#### C++ 对象模型
_________
> 所有的的非静态数据成员函数存储在对象本身中，所有的静态数据成员，成员函数（包括静态和非静态）都置于对象之外，另外，用一张虚函数表（virtual table）存储所有指向虚函数的指针，并在表头附加上一个该类的type_info for Point对象<strong>(用来支持runtime type identification RTTI)</strong>，在对象中则保存一个指向虚函数表的指针，如下图：
![Alt text](./3.png)

##  class 和 struct
————————————————
#### 一个有意思的C技巧（但是别在C++中类使用）
```cpp
struct mumble {  
   /* stuff */  
   char pc[ 1 ];  
};  
// grab a string from file or standard input  
// allocate memory both for struct & string  
struct mumble *pmumb1 = ( struct mumble* )malloc(sizeof(struct mumble)+strlen(string)+1);  
strcpy( &mumble.pc, string );
```
<em>但是别在C++中使用。因为C++布局相对复杂，例如继承，有虚函数</em>
#### C++ 三种设计范式
+ 程序模型   （procedural model）
>  就像C一样，C++也支持它 字符串的处理就是一个例子，我们可以使用字符数组以及<code>str *</code>函数族群
>```cpp
>char boy[]="Danny";
>char *p_son;
>...
>p_son=new char[strlen(boy)+1];
>strcpy(p_son,boy);
>if(!strcmp(p_son,boy))
> take_to_disneyland(boy);
>```
+ ADT 模型 (抽象数据类型模型,典型的有操作符重载)
>例如下面<code>String class：</code>
>```cpp
>String girl="Anna";
>String daughter;
>//String::opertaor=();
>daughter=girl;
>...
>//String::operator()==();
>if(girl==daughter)
>  take_to_disneyland(girl);
>```
+ 面向对象模型（object-oriented model）。 在此模型中有一些彼此相关的类型，通过一个抽象的base class（用以提供共同接口）被封装起来。 
> ```cpp
> void check_in(Library_materials *pat)
> {
>   if(pat->late());
>      pat->fine();
>   pat->check_in();
>  if(Lender *plend=pat->reserved())
>    pat->notify(plend);
>}
> ```
______________________________________________
<strong>!!! 纯粹使用一种典范编程，有莫大的好处，如果混杂多种典范编程有可能带来意想不到的后果 ，例如将继承类的对象赋值给基类对象，而妄想实现多态，便是一种ADT模型和面向对象模型 混合编程带来严重后果的例子。</strong>


####重点 一个类的对象的内存大小包括：
+ 所有非静态数据成员的大小
+ 有内存对齐而填补的内存大小 （aligment 就是将数值调整到某数的倍数）
> 在32位计算机上，通常alignment为4bytes（32位）
+ 为了支持virtual有内部产生的额外负担
——————————————————————————————————————————————————————————————————————————————
###例子：
```cpp
class ZooAnimal {  
public:  
   ZooAnimal();  
   virtual ~ZooAnimal();  
   virtual void rotate();  
protected:  
   int loc;  
   String name;  
};
ZooAnimal za("Zoey");
ZooAnimal *pza=&za
```
![Alt text](./4.jpg)
### 加上多态以后：
```cpp
 class Bear:public ZooAnimal
 {
      public:
      Bear();
      ~Bear();
      protected:
       enum Dances {...};
       Dances dances_known;
       int cell_block;
 };
 Bear b("Yogi");
 Bear *pb=&b;
 Bear &rb=*pb;
 ```
 ![Alt text](./5.jpg)`
#### 注意：
```cpp
 Bear b;
 ZooAnimal *pz=&b;
 Bear *pb=&b;
```
他们每个都指向Bear object的第一个Byte。其间的差别是，pb所涵盖的地址是包括整个Bear object，而pz涵盖地址只包含Bear object 中的ZooAnimal subobject
除了 ZooAnimal subobject中出现的members，其余不能使用pz来直接处理Bear的任何members，唯一例外是通过virtual机制

————————————————————————————————————————————————————————————————————————————————————————————————
### 结构体（顺便提一下）
------------
##### 1、没有#pragma pack宏的对齐规则
+ 结构体变量中成员的偏移量必须是成员大小的整数倍（0被认为是任何数的整数倍）
+ 结构体大小必须是所有成员大小的整数倍，也即所有成员大小的公倍数。
+ 对于嵌套的结构体，需要将其展开。对结构体求sizeof时，上述两种原则变为：
  > + 展开后的结构体的第一个成员的偏移量应当是被展开的结构体中最大的成员的整数倍。
  > + 结构体大小必须是所有成员大小的整数倍，这里所有成员计算的是展开后的成员，而不是将嵌套的结构体当做一个整体
  > + 结构体包含共用体成员，则该共用体成员要从其原始共用体内部最大对齐模数的整数倍地址开始存储
  > + 结构体包含数组成员，比如char a[3],它的对齐方式和分别写3个char是一样的，也就是说它还是按一个字节对齐。如果写：typedef char Array[3],Array这种类型的对齐方式还是按一个字节对齐，而不是按它的长度3对齐
  
  
——————————————————————————————————————————————————————————————————————————————
##### 2、存在#pragma pack宏的对齐
 pragma pack (n) //编译器将按照n个字节对齐

 pragma pack () //取消自定义字节对齐方式
+ 结构，联合，或者类的数据成员，第一个放在偏移为0的地方，以后每个数据成员的对齐，按照#pragma pack指定的数值和自身对齐模数中较小的那个。



