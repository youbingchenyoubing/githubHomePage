---
title: C++_Object_Model_2
date: 2016-04-17 19:00:38
tags: 深入C++构造函数
---
通常很多C++程序员存在两种误解：
=======================
+ 没有定义默认构造函数的类都会被编译器生成一个默认构造函数。
+ 编译器生成的默认构造函数会明确初始化类中每一个数据成员。

——————————————————————————————————————————————
**全局变量的内存 保证会在程序启动的时候被清为0，局部变量配置于程序堆栈中，heap objects配置在于自由空间，都不一定被清为0，他们的内容将是内存上次被使用后的遗迹**
<font color=red>C++标准规定：如果类的设计者并未为类定义任何构造函数，那么会有一个默认 构造函数被暗中生成，而这个暗中生成的默认构造函数通常是不做什么事的(无 用的)，下面四种情况除外。
换句话说，有以下四种情况编译器必须为未声明构造函数的类生成一个会做点事 的默认构造函数。我们会看到这些默认构造函数仅“忠于编译器”，而可能不会按 照程序员的意愿程效命。</font>

+ #### 1.包含有带有默认构造函数的对象成员的类
> 如果一个class没有任何contructor,但却包含一个对象成员，这个时候就有默认构造函数，那么这个默认构造函数就很重要.
```cpp
 class Foo{ public:Foo(),Foo(int)...};
 class Bar{public:Foo foo;char *str;};
 void foo_bar()
{
    Bar bar;  //Bar::foo 必须在此处初始化
    if(str) {...}
}
 ```
> <em>被合成的Bar default constructor</em>内含必要的代码，能够调用class Foo 的default constructor来处理对象成员 Bar::foo,但它并不产生任何代码来初始化Bar::str。 将Bar::foo初始化是编译器的责任，将Bar::str初始化是程序员的责任。被合成的默认构造函数大概可能是这样：
```cpp
 inline 
 Bar:Bar()
 {
  //C++ 伪代码
  foo.Foo::Foo();
}
```
> ！！！ <font color='red'>这种情况只满足编译器的需要，而不是程序的需要，为了让程序能够正确执行，字符指针必须str也必须被初始化</font>
> ```cpp
> //程序员定义的默认构造函数
> Bar::Bar(){str=0;}
> ```
> <font color='red'>现在程序需求已经获得满足，但是编译器还需要初始化 foo，由于默认构造函数已经被显式地定义出来，编译器没办法合成第二个</font>
> <font color='red'>编译采取的行动是：“如果class A”内含一个或多个的成员对象，那么Class A的每一个构造函数必须调用每一个成员类的默认构造函数，编译器会扩张已有存在的构造函数，在其中安插一些代码，使得 user code 被执行之前，先调用必要的默认构造函数</font>
> 
```cpp
 //扩张后的代码 C++ 伪代码
 Bar::Bar()
{
  foo.Foo::Foo(); // 附加上的compiler code
    str=0;     // explicit(显示) 用户代码
    }
```
> <font color='red'>如果都多个成员对象,<strong>按照成员声明的顺序来调用各个构造函数</strong></font>
>
>```cpp
 class Dopey{public:Doey()};
 class Sneezy{public:Sneezy(int);Sneezy();...};
 class Bashful{public:Bashful();...};
 //以及一个class Snow_White:
 class Snow_White{
   public:
   Doey doey; 
   Sneezy sneezy;
   Bashful bashful;
   //...
   private:
    int mumble;
 };
```
>如果没有Snow_White 没有定义默认构造函数，就会有一个 构造函数被合成出来，依次调用Dopey,Sneey,Bashful的默认构造函数
>```cpp
Snow_White::Snow_White():sneezy(1024)
{
 mumble=2048;
}
>```
>```cpp
//编译器扩张后的默认构造函数
// C++ 伪代码
Snow_White::Snow_White(): sneezy(1024)
{
  // 插入member class object
  //调用其的构造函数
  dopey.Dopey::Dopey();
  sneezy.Sneezy::Sneezy(1024);
  bashful.Bashful::Bashful();
 ...
 // explicit user code
 mumble= 2048;
}
>```
+ #### 2.继承带有默认构造函数的基类的类
> <font color='blue'>如果没有任何一个构造函数的类的父类带有默认构造函数的，那么这个派生类的默认构造函数很重要，并以此被合成出来，它将调用上一层基类的默认构造函数（根据他们的声明顺序）</font>
> <font color="orange"><strong>同样的,如果设计者提供多个构造函数，但其中没有默认构造函数，编译器会扩张每一个构造函数，将用以调用所有必要的默认构造函数的程序加进去。如果同时带有成员对象的话，那么那些成员对象的构造函数也会被调用---</strong></font><font color="red"><strong>当然是在基类的默认构造函数之后</strong></font>


+ #### 3.带有一个virtual function的类
> 另有两种情况，也需要合成出默认构造函数
> + <strong>class 声明（或继承）一个 virtual function</strong>
> + <strong>class 派生自一个继承串链，其中有一个或更多的virtual base classes。</strong>
 ```cpp
 class Widget{
 public: 
 virtual void flip()=0;
 //...
 };
 void flip(const Widget & widget) {widget flip();}
 // 假设Bell和Whistle都派生自Widget
 void foo()
 {
  Bell b;
  Whistle w;
  //...
  flip(b);
  filp(w);
 }
 ```
> <strong><font color=red> 下面两个扩张行动会在编译期间发生：</font></strong>
> + 1.一个virtual function table（vtbl）会被编译器产生出来，内放类的 virtual functions地址
> + 2.在每一个class object中，一个额外的point member（也就是vptr）会被编译器合成出来，内含有关class vtbl的地址
> 
> <font color=red>为了虚函数机制发挥功效，编译器必须为每一个Widget或其派生类vptr设定初值，放置适当的virtual table的地址。 对于没有申明任何构造函数的类，编译器会为他们合成一个 默认构造函数 以便正确地初始化每一个class object 的vptr</font>
+ #### 4. 带有一个 virtual Base Class 的Class
>  ```cpp
 class X{public:int i};
 Class A:public virtual X {public:int j;};
 Class B:public virtual X{public: int l;};
 Class C:public A,public B{public: intk;};
 // 无法在编译的时候决定pa->X::i的位置
 void foo（const A *pa） {pa->i=1024;}
 main()
 {
   foo(new A);
   foo(new C);
 }
```
> 编译器无法固定住foo()之中“经由pa而存取的X::i的实际位置，因为pa的类型可以改变。编译器必须在改变“执行存取操作”的那些代码，使X::i可以延迟至执行期才决定下来。在派生类对象的每一个virtual base classes 中安插一个指针来完成所有经由引用和指针来存取一个virtual base classes 的操作都通过相关指针来完成。
>```cpp
//编译器的转变操作
void foo（const A *pa） {pa->_vbcX->i=1024;}
其中就是_vbcX表示编译器产生的指针，指向virtual base class X
```
><font color=red>_vbcX是在class object构造期间被完成的，对于class所定义的每一个构造函数，编译器会安插那些“允许每一个virtual base class 的执行期的代码”，如果没有申明，编译器会合成一个默认构造函数</font>

## 总结
<font color=red>需要说明的是，从概念来上来讲，每一个没有定义构造函数 的类都会由编译器来合成一个默认构造函数，以使得可以定义一个该类的对象， 但是默认构造函数是否真的会被合成，将视是否有需要而定。C++ standard 将 合成的默认构造函数分为 trivial 和 notrivial 两种，前文所述的四种情况对 应于notrivial默认构造函数，其它情况都属于trivial。对于一个trivial默认 构造函数，编译器的态度是，既然它全无用处，干脆就不合成它。在这儿要厘清 的是概念与实现的差别，概念上追求缜密完善，在实现上则追求效率，可以不要 的东西就不要</font>