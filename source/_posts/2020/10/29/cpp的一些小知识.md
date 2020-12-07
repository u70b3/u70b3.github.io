---
title: C++ 的一些小知识
date: 2020-11-29 21:38:54
tags:
    - C++
    - demo
description: 记录 C++ 开发中的常用小技巧和一些易错易混知识，以及 C++ 的坑
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

---
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

---
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

---

## 四、cin、cin.get、cin.getline、getline

- cin会从缓存区内的第一个非结束符字符开始，读取字符串，遇到结束符，将结束符留在缓存区
- `cin.get(char)` 可以读取字符、空格、TAB、回车，仅读取一个，剩余的依然会留在缓存区
- `cin.getline(ch，len)` getline是读取一行到一个数组，而get是单纯的读取数据
- `getline(cin,str);` 第二个参数是string，不存在溢出问题


---
## 五、宏定义和条件编译
> 源码上的文本替换而已

- *#* 要开头，顶格写
- *#* 和指令之间可以有空格
- 单独的 *#* 是个空指令

    ```c++
    #                              // 预处理空行
    #if __linux__                  // 预处理检查宏是否存在
    #   define HAS_LINUX    1      // 宏定义，有缩进
    #endif                         // 预处理条件语句结束
    #                              // 预处理空行
    ```

- 可以用宏来美化源代码

    ```c++
    static uint32_t  calc_table[] = {  // 非常大的一个数组，有几十行
        0x00000000, 0x77073096, 0xee0e612c, 0x990951ba,
        0x076dc419, 0x706af48f, 0xe963a535, 0x9e6495a3,
        0x0edb8832, 0x79dcb8a4, 0xe0d5e91e, 0x97d2d988,
        0x09b64c2b, 0x7eb17cbd, 0xe7b82d07, 0x90bf1d91,
        ...                          
    };
    static uint32_t  calc_table[] = {
    #  include "calc_values.inc"        // 非常大的一个数组，细节被隐藏
    };
    ```

- 调试宏定义可以用 `g++ -E`
    ```bash
    g++ test03.cpp -E -o a.cxx    #输出预处理后的源码
    ```

- 宏是没有作用域概念的，永远是全局生效
    ```c++
    //   用完就取消
    #define CUBE(a) (a) * (a) * (a)  // 定义一个简单的求立方的宏

    cout << CUBE(10) << endl;        // 使用宏简化代码
    cout << CUBE(15) << endl;        // 使用宏简化代码

    #undef CUBE                      // 使用完毕后立即取消定义
    ```
    ```c++
    // 宏定义前先检查
    #ifdef AUTH_PWD                  // 检查是否已经有宏定义
    #  undef AUTH_PWD                // 取消宏定义
    #endif                           // 宏定义检查结束
    #define AUTH_PWD "xxx"           // 重新宏定义
    ```

- 条件编译：不同版本做不同实现
    ```c++
    #ifdef __cplusplus                      // 定义了这个宏就是在用C++编译
        extern "C" {                        // 函数按照C的方式去处理
    #endif
        void a_c_function(int a);
    #ifdef __cplusplus                      // 检查是否是C++编译
        }                                   // extern "C" 结束
    #endif

    #if __cplusplus >= 201402                // 检查C++标准的版本号
        cout << "c++14 or later" << endl;    // 201402就是C++14
    #elif __cplusplus >= 201103              // 检查C++标准的版本号
        cout << "c++11 or before" << endl;   // 201103是C++11
    #else   // __cplusplus < 201103          // 199711是C++98
    #   error "c++ is too old"               // 太低则预处理报错
    #endif  // __cplusplus >= 201402         // 预处理语句结束
    ```

- 条件编译：代替*/\*\*/*注释代码
    ```c++
    #if 0          // 0即禁用下面的代码，1则是启用
    ...          // 任意的代码
    #endif         // 预处理结束

    #if 1          // 1启用代码，用来强调下面代码的必要性
    ...          // 任意的代码
    #endif         // 预处理结束
    ```
