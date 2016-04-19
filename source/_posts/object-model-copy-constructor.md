---
title: object_model_copy_constructor
date: 2016-04-18 15:03:27
tags: C++拷贝构造函数
---

拷贝构造函数（copy constructor）
=======
<font color=red><strong>构造函数的应用场合，3种</strong></font>
------------
 
### 1.对一个object（对象）做显示的初始化操作
> 例如
```cpp
class X {....};
X x;
//显示地以一个object的内容作为另一个class object的初值
X xx=x;
```

### 2.当object被当做参数交给某个函数时
> 例如
> ```cpp
  extern void foo(X x);
  void bar()
  {
   X xx;
   // 以xx 作为foo()第一个参数的初值（隐式的初始化操作）
   foo(XX);
   //...
  }
 ```

### 3.当函数传回一个类对象（class object）
> 例如：
``` cpp
X foo_bar()
{
    X xx;
    //...
    return xx;
}
```

<font color=red><strong>编译器只有在必要的时候合成拷贝构造函数，通常有以下情况是必要的</strong></font>
--------
一个 class object 可用两种方式复制得到
+ 被初始化(<font color=blue>这里重点要讲</font>）
+ 被指定

<font color="green">就像默认构造函数一样，如果class没有声明一个复制构造函数，编译器class是否展现“Bitwise copy semantics” (位逐次拷贝) 来合成拷贝构造函数</font>

### Bitwise Copy Semantics (位逐次拷贝)
> 例子
```cpp
#include "Word.h"
Word noun("book");
void foo()
{
 Word verb=noun;
 //...
}
```
> 如果Word类定义如下：
> ```cpp
// 以下声明展现了bitwise copy semantics
class Word
{
  public:
  Word(const char *);
  ~Word(delete [] str;)
  //...
  private:
  int cnt;
  char *str;
}
```
<font color=red>这种情况并不需要合成出一个默认构造函数</font>
如果<code>class Word</code>定义如下:
```cpp
class Word{
public:
Word(const String &);
~Word();
//...
private:
int cnt;
String str;
};
```
><font color=red>这种情况下，编译器合成出一个copy constructor,以便调用member class String object的拷贝构造函数</font>
>```cpp
//一个被合成出来的复制构造函数
//C++ 伪代码
inline Word::Word(const Word &wd)
{
     str.String::String(wd.str);
     cnt=wd.cnt;
}
>```

<font color=red>注意的是：在被合成出来copy 构造函数中，如整数、指针、数组等等非成员也都被会复制</font>

### 不要Bitwise Copy Semantic!
+ 当一个类内含一个对象成员而后者的class声明有一个拷贝构造函数（不论是显示声明还是编译器合成的）
+ 当一个类继承一个base class同时后者存在一个copy 构造函数（不论是显示声明还是编译器合成的）
+ 当一个类声明一个或获得virtual function时。
+ 当class派生自一个继承链，其中有一个或多个的virtual base class时。

<font color=red>前面两种情况，编译器必须将member或base class的“copy constructor”调用操作安插到合成的copy constructor</font>


