---
title: 重学 C++ 笔记
date: 2020-11-25 12:09:20
tags:
    - C++
description: 系统学习 C++ 的笔记，个人查漏补缺用，主要参考侯捷五门课（OOP/C++11/STL/startup/内存）、《C++ primer 5th》、《Effective Modern C++》
cover: 
---

## 一、C++基础
### 面向对象
- 构造函数尽量使用列表初始化，可以少赋值一次（变量先初始化，再赋值）


- 函数若在 class body 内定义完成，则自动成为 inline 候选人（inline 是对编译器的建议）
- 相同 class 的各个 objects 互为 friends
- 参数传递、返回值传递首先考虑 pass-by-reference（to const），而不是 pass-by-value
  - local 变量不可以返回引用
- 不改值的成员函数后面随手加个`const`
- 引用本质是指针的语法糖
  - 引用定义时必须初始化
  - 引用传递不允许直接引用，可以间接引用
- return-by-reference：传输者无需知道接收者是以reference形式接收
  ```cpp
  inline complex&
  __doapl(complex* ths, const complex& r)
  {
    //
    return *ths;
  }
  ```

### 模板
todo

## 二、C++11

todo

## 三、STL
todo 

## 四、内存
todo

## 五、c++程序startup

todo
