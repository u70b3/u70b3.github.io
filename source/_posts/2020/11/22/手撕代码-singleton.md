---
title: 手撕代码-singleton
date: 2020-11-22 16:19:43
tags: 
    - C++
    - 手撕代码
description: C++ 实现 singleton
cover: 
---

## c++11 local static

C++ 11 规定了 local static 在多线程条件下的初始化行为，要求编译器保证了内部静态变量的线程安全性。

```c++
class Singleton {
private:
	Singleton() { };
	~Singleton() { };
	Singleton(const Singleton&) = delete;
	Singleton& operator=(const Singleton&) = delete;
public:
	static Singleton& getInstance() // 返回引用
        {
		static Singleton instance; // 静态局部变量
		return instance;
	}
};
```
