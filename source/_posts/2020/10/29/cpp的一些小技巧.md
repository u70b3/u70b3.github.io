---
title: C++的一些小技巧
date: 2020-10-29 21:38:54
tags:
    - C++
    - demo
description: 记录C++开发中的小技巧和一些易错易混知识，以及C++的坑
toc: 1
---


## 一、do...while

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

## 二、string、char* 、char[]、char 之间的转换
- string -> char* 使用string类方法

- string -> char[]使用循环赋值，结尾加’\0’

- char*、char[] -> string 直接赋值

- 单个字符char -> string
### 

## 三、string、char*、int 之间的转换
### 

## 四、cin、cin.get、cin.getline、getline

