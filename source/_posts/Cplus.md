layout: 开源project
title: C++开源项目 
date: 2016-04-01 10:49:18
tags:
---
# leveldb
>LevelDb是谷歌两位大神级别的工程师发起的开源项目，简而言之，LevelDb是能够处理十亿级别规模Key-Value型数据持久性存储的C++ 程序库。
>它是一个持久化存储的KV系统，和Redis这种内存型的KV系统不同，LevelDb不会像Redis一样狂吃内存，而是将大部分数据存储到磁盘上。
>其次，LevleDb在存储数据时，是根据记录的key值有序存储的，就是说相邻的key值在存储文件中是依次顺序存储的，而应用可以自定义key大小比较函数，LevleDb会按照用户定义的比较函数依序存储这些记录。
<p>主页:https://github.com/google/leveldb</p>

# SGI STL
>SGI STL是STL代码的经典实现版本，虽然很多编译器不直接使用这个版本，但是很多却在此基础之上进行改进的。比如GNU C++的标准库就是在此基础之上改进的。这份代码还有一个好处是有注释，代码书写非常规范，只要花些时间读懂它并非难事。


<p>主页：https://www.sgi.com/tech/stl/download.html</p>


# Muduo
>muduo 是一个基于 Reactor 模式的现代 C++ 网络库，它采用非阻塞 IO 模型，基于事件驱动和回调，原生支持多核多线程，适合编写 Linux 服务端多线程网络应用程序。


<p>主页:https://github.com/chenshuo/muduo</p>


