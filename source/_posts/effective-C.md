---
title: effective_C++
date: 2016-04-20 17:57:07
tags: C++语言的四个次语言（effective C++ 第一条款）
---


条款 01：视为C++为一个语言的联邦
=========
+ （1） C -------C++仍然是以C为基础
+ （2） object-oriented C++
> classes(类) 包括构造函数和析构函数，encapsulation(封装)，inheritance（继承），polymorphism（多态），virtual functions(dynamic binding)（虚函数（动态绑定））等。
+  （3） Template C++
> 这是C++的generic programming（泛型编程）部分
+  （4）STL
> STL 是一个template library（模板库）

<font color=red>C++并不是一个带有一组守则的一体的语言，他是四个语言组成的联邦政府，每个语言都有自己的规约</font>

------------------------------------------------------------------------
条款 02： 尽量以const,enum,inline替换#define
======

该条款最好称为：<font color=blue><strong>"尽量用编译器而不用预处理"</strong></font>，因为#define不被视为语言的一部分
```cpp
#define ASPECT_RATO 1.653
```
编译器永远看不到 ASPECT_RATO这个符号名，因为在源码进入编译器之前，他会被预处理程序去掉，于是 ASPECT_RATO不会加入符号列表中。
解决的方法：不用预处理宏，定义一个常量
```cpp
const double AspectRatio=1.653; //大写名称通常用于宏
```
这样做的好处：
+ <font color=red>作为语言的常量，AspectRatio会被编译器看到，记入符号列表</font>
+ <font color=red>使用常量可能比使用#define导致较小量代码，因为预处理器盲目用1.653替换 ASPECT_RATO导致目标代码中存在多个1.653的拷贝，而AspectRadio就不会对于一个拷贝</font>

把常量限制在类中，首先要使它成为类的成员，为了保证常量最多只有一个拷贝，还要把它定义为静态成员：
```cpp
class GamePlayer
{
   private:
   static const int NUM_TURNS=5; //声明而非定义
   int score[NUM_TURNS];
   ...
};
```
说明：
+ <font color=red>语句NUM_TURNS的声明，而非定义</font>
+ <font color=red>C++要求对锁使用的任何东西提供一个定义式，如果他是Class的专属常量且为static整数类型（ints,chars,bools等），只要不取他们的地址可以只声明而无需定义</font>
+ <font color=red> NUM_TURNS的定义如下：</font>
> ```cpp
const int::GamePlayer::NUM_TURNS; //在实现文件而非头文件，由于class常量已在声明时获得初值，因此定义不可设定初值
```
+ <font color=red>没有办法使用#define来创建一个类属常量，<strong>因为#defines 不考虑作用域，一定被宏定义，它就在后编译过程有效（除非后面某处存在#unded）</strong></font>

#### 针对旧的编译器出现的问题：
旧一点的编译器认为类的静态成员在声明时定义初始值是非法的。可以在定义赋值：
```cpp
class EngineeringConstants                // header file
{ 
private:        
    static const double FUDGE_FACTOR;
    ...
};
// this goes in the class implementation file
const double EngineeringConstants::FUDGE_FACTOR = 1.35;
```
如果在编译器需要FUDGE_FACTOR的值例如，作为数组维数，是不行的。因为编译器必须在编译器间知道数组的大小。可以用enum解决
```cpp
class GamePlayer 
{
private:
    enum { NUM_TURNS = 5 };                    
    int scores[NUM_TURNS];
};
//注意：去一个enum地址是非法的，跟#define一样
```
同时一个普遍的#define的错误用法（是用来实现那些看起来像函数）而又不会导致函数调用的宏
```cpp
#define max(a,b) ((a)>(b) ?(a):(b))
```
注意：写宏的时候要对每个参数都要加上括号，否则会造成调用麻烦，但也会造成下面的错误：
```cpp
int a=5,b=0;
max(++a,b); //a的值增加了两次
max(++a,b+10)//a的值只增加了一次
//原因是：#define只是简单的替换
```
解决的方法：<strong><font color=blue>使用普通函数实现宏的效率，再加上可预计的行为和类型安全</font></strong>
```cpp
template<tydefname T>
inline const T &max(const T &a,const T &b)
{
       return a>b? a:b;
}
```
<strong>
请记住：

（1）对于单纯常量，最好以const或enums替换#defines。

（2）对于形似函数的宏，最好改用inline函数替换#defines。
</strong>S