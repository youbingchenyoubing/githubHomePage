layout: C++ 虚函数工作原理
title: C++
---
C++ 虚函数工作原理	
=========

<em>编译器处理虚函数的方法是：给每个对象添加一个隐藏成员</em>

>隐藏成员保存了一个指向函数地址数组的指针，这个数组成为虚函数表，虚函数表中存储了为类对象进行申明的虚函数地址
>例如，基类对象包含一个指针，改指针指向基类的虚函数的地址表，派生类的对象也包含了一个指向独立地址表的指针，如果该派生类提供了虚函数的新定义，该虚函数表间保存了新函数的地址；如果没有重新定义，该虚函数表保存的是原始版本的地址
>
<em>注意：无论类中包含的虚函的个数是多少，都只需要在对象中添加1个地址成员，只是表的大小不同而已</em>
![](./img/virtual.jpg "虚拟示意图")






有关虚函数的注意事项
===============
+ 在基类方法声明中使用关键字virtual可使该方法以及所有的派生类中都是虚
+ 如果指向对象的引用或是指针或是引用来调用虚方法，程序将使用为对象型定义的方法，而不使用为引用或是指针类型定义的方法，这称为<strong>动态联编或是晚期联编</strong>
+ <strong>构造函数不能为虚函数</strong>，理由是：创建派生类的对象时，将调用派生类的构造函数，而不是基类的构造函数，然后，派生类的构造函数将使用基类的一个构造函数，这种顺序不同于继承机制。因此派生类不能继承基类的构造函数，所以将构造函声明为虚函数的没有什么意义。
+<strong>析构函数应该是虚函数，除非不用做基类</strong>

