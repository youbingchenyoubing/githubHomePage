---
title: C++_Base
date: 2016-04-18 14:44:47
tags: C++ 基础
---
C++ 输出格式
========
相信大家在很多情况下：设置浮点数的显示精度


+ <code>cout.setprecision(2);</code> 和width()情况不同，但与fill()类似,新的精度设置将一直有效，直到被重新设置
+ > + <code>setprecision(n)</code>;设置输出的数字总位数，包括整数和小数部分。
   > + <code>setiosflags(ios::fixed)</code> 默认输出6位，必须和<code>setprecision(n)</code>配合使用，用来控制小数位数，不够补0
   > + <code>resetiosflags(ios::flags)</code> 取消精度的设置
```cpp
#include <iostream> 
#include <iomanip>  //这个是头文件，一定要。
using namespace std; 
int main () { 
    double f =3.14159; 
    cout << setprecision (5) << f << endl;                //3.1416        
    cout << setprecision (9) << f << endl;                 //3.14159 
    cout << fixed<<setprecision (5) << f << endl;    //3.14159 
    cout <<fixed<< setprecision (9) << f << endl;    //3.141590000 
    cout<<f<<endl;                                                  //3.141590000 
    cout<<resetiosflags(ios::fixed)<<setprecision(9)<<f;//3.14159 
    return 0; 
} 
```