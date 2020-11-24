---
title: C++ 的一些小知识
date: 2020-10-29 21:38:54
tags:
    - C++
    - demo
description: 记录 C++ 开发中的小技巧和一些易错易混知识，以及 C++ 的坑
cover:
---


## 一、`do...while(0)`

### 更安全的宏定义

假设有如下代码：
```c++
#define p {A;B}
if(COND){
	p;      //不符合编程习惯————通常语句后加分号
} else {
    ;
}

当然，如果上述语句不加分号，是不会出现问题的。用do…while(0)的好处是可以将加分号贯彻到底。
```

### 更优秀的错误处理
假设有以下代码：
```c++
if(error1)
{
	A;
	B;
}
if(error2)
{
	A;
	B;
}
if(error3)
{
	A;
	B;
}
```
代码冗余度很高。这时，可以祭出**臭名昭著**的`goto`，导向同一段errorhandle代码： 
```c++
if(error1)
{
	goto errorhandle;
}
if(error1)
{
	goto errorhandle;
}
if(error1)
{
	goto errorhandle;
}
...
errorhandle:
A;
B;
```

然而，众所周知，goto是万恶之源，怎么破？
答案是使用do…while(0)
```c++
do{
	if(error1)
		break;
	if(error2)
		break;
	if(error3)
		break;
}while(0);
A;
B;
```
因此，在需要错误处理时，do…while(0)可以减少代码冗余度和goto的使用。

## 二、string、const char* 、char[]、char转换
### string -> char* 使用string类方法
```c++
    string str = "hello";
    const char *p = str.data();     //尽量不用
	const char *q = str.c_str();
```

### string -> char[]使用循环赋值，结尾加’\0’
```c++
    string s = "hello";
    for (auto c : s)
    {
		;	//...
    }
```
### const char*、char[] -> string 直接赋值
```c++
    string s1, s2;
    const char *p = "hello";
    char q[100] = "world";
    s1 = p;
    s2 = q;
    cout << s1 << endl << s2 << endl;
```
### 单个字符char -> string
```c++
	//1、构造函数 string(size_t,char)
	char x = 'a';
	string s(1,x); //1表示构造函数需要的x的个数
	//2、push_back
	string s;
	char x = 'a';
	s.push_back(x);
```
## 三、string、char*、int 之间的转换

```c++
#include <iostream>
#include <sstream>
#include <climits>
using namespace std;
int main()
{
    int num = INT_MAX;
    string s1 = "-233a233";
    const char *s2 = "+233a233";
    cout << to_string(num) << endl; //to_string用于将int转换为string,超出int范围不报错，但只显示int值
    cout << stoi(s1) << endl;       //stoi在转换string类时，如果遇到字符，不再转换后续整型及字符
    cout << atoi(s2) << endl;       //atoi只可以用于const char*（char*可以自动转换为const char *）

    // 字符串与int的转换可以使用stringstream
    string s3;
    stringstream ss;
    ss << num;
    ss >> s3;
    cout << s3 << endl;
}
```
## 四、cin、cin.get、cin.getline、getline

- cin会从缓存区内的第一个非结束符字符开始，读取字符串，遇到结束符，将结束符留在缓存区
- `cin.get(char)` 可以读取字符、空格、TAB、回车，仅读取一个，剩余的依然会留在缓存区
- `cin.getline(ch，len)` getline是读取一行到一个数组，而get是单纯的读取数据
- `getline(cin,str);` 第二个参数是string，不存在溢出问题

## 五、
