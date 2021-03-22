---
title: 代码实现-string
date: 2020-11-22 16:16:12
tags: 
    - C++
    - 代码实现
description: 实现 C++ 的 string
cover: 
sticky: 
---

## 源代码
文件：`StringTrivial.h`

```c++
#ifndef STRING_TRIVIAL_H
#define STRING_TRIVIAL_H

#include <utility>
#include <cassert>
#include <cstring>
#include <iostream>

namespace kid
{
    class String
    {
    public:
        String()
            : data_(new char[1])
        {
            // std::cout << "default-ctor" << std::endl;
            *data_ = '\0';
        }

        String(const char *str)
            : data_(new char[strlen(str) + 1])
        {
            // std::cout << "(const char*)-ctor" << std::endl;
            strcpy(data_, str);
        }

        String(const String &rhs)
            : data_(new char[rhs.size() + 1])
        {
            // std::cout << "copy-ctor" << std::endl;
            strcpy(data_, rhs.c_str());
        }
        /* Implement copy-ctor with delegating constructor in C++11
        String(const String& rhs)
            : String(rhs.data_)
        {
        }
        */

        ~String() noexcept
        {
            // std::cout << "dtor" << std::endl;
            delete[] data_;
        }

        /* Traditional:
        String& operator=(const String& rhs)
        {
            String(rhs).swap(*this); // copy-ctor--RAII | Non-throwing swap
            return *this;
        }// Old resources released when destructor of _ is called.
        */
        // In C++11, this is unifying assignment operator
        // the pass-by-value parameter serves as a temporary
        // http://en.wikibooks.org/wiki/More_C++_Idioms/Copy-and-swap
        String &operator=(String rhs) // yes, pass-by-value
        {
            // std::cout << "copy-assignment-operator" << std::endl;
            this->swap(rhs); // Non-throwing swap
            return *this;
        } // Old resources released when destructor of rhs is called.
        //

        // C++11 move-ctor
        String(String &&rhs) noexcept
            : data_(rhs.data_)
        {
            // std::cout << "move-ctor" << std::endl;
            rhs.data_ = nullptr;
        }

        /* Not needed if we have pass-by-value operator=() above, and it conflits.
        http://stackoverflow.com/questions/17961719/
        String& operator=(String&& rhs)
        {
            this->swap(rhs);
            return *this;
        }
        */

        // Accessors
        size_t size() const { return strlen(data_); }

        const char *c_str() const { return data_; }

        void swap(String &rhs) { std::swap(data_, rhs.data_); }

    private:
        char *data_;
    };
} // namespace kid
#endif
```

## 测试代码

文件：`StringTrivialTest.cpp`
命令：`g++ -O0 -g -Wall -o test StringTrivialTest.cpp && ./test`

```c++
#include "StringTrivial.h"
#include <iostream>
#include <utility>
#include <vector>

using namespace kid;

void foo(String x) {}

void bar(const String &x) {}

String baz()
{
    String res("world");
    return res;
}
String baz1(String &&s)
{
    //The std::move function converts the lvalue other to an rvalue.
    String res = std::move(s);
    return res;
}

int main()
{
    std::cout << "==start main==" << std::endl;
    String s0;          //default-ctor
    String s1("hello"); //(const char*)-ctor
    String s2(s0);      //copy-ctor
    String s3(s1);      //copy-ctor
    s2 = s1;            //copy-ctor|copy-assignment-operator|dtor
    s3 = s3;            //copy-ctor|copy-assignment-operator|dtor
    s1 = "awesome";     //(const char*)-ctor|copy-assignment-operator|dtor

    foo(s1);             //copy-ctor|dtor
    bar(s1);             //
    foo("temp");         //(const char*)-ctor|dtor
    bar("temp");         //(const char*)-ctor|dtor
    String s4 = baz();   //(const char*)-ctor
    s4 = baz();          //(const char*)-ctor|copy-assignment-operator|dtor
    baz1(String("kid")); //(const char*)-ctor|move-ctor|dtor

    std::cout << "=start vector test=" << std::endl;

    std::vector<String> svec;
    svec.push_back(s0);         //copy-ctor
    svec.push_back(s1);         //copy-ctor
    svec.push_back("good job"); //(const char*)-ctor|move-ctor|dtor

    std::cout << "=end vector test=" << std::endl;

    std::cout << "==end main==" << std::endl;
    return 0;
}
```
