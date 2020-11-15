---
title: 重学 C++ 笔记
date: 2020-11-07 12:09:20
tags:
    - C++
description: 系统学习 C++ 的笔记，主要参考侯捷五门课、《C++ primer 5th》、《Effective Modern C++》
cover: 
---

## OO

- 构造函数尽量使用列表初始化，可以少赋值一次（变量先初始化，再赋值）
- 函数若在 class body 内定义完成，则自动成为 inline 候选人（inline 是对编译器的建议）
- 相同 class 的各个 objects 互为 friends
- 参数传递、返回值传递首先考虑 pass by reference（to const），而不是 pass by value
  - local 变量不可以返回引用
- 不改值的成员函数后面随手加个`const`

## C++11



## STL

