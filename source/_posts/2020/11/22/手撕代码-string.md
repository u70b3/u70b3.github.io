---
title: 手撕代码-string
date: 2020-11-22 16:16:12
tags: 
    - C++
    - 手撕代码
description: 手撕 C++ 的 string
cover: 
---

## 源代码
文件：`StringTrivial.h`

```c++
#ifndef STRING_TRIVIAL_H
#define STRING_TRIVIAL_H

#include <utility>
#include <cassert>
#include <cstring>

namespace kid
{
    class String
    {
    public:
        String()
            : data_(new char[1])
        {
            *data_ = '\0';
        }

        String(const char *str)
            : data_(new char[strlen(str) + 1])
        {
            strcpy(data_, str);
        }

        String(const String &rhs)
            : data_(new char[rhs.size() + 1])
        {
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
            delete[] data_;
        }

        /* Traditional:
        String& operator=(const String& rhs)
        {
            String tmp(rhs);
            swap(tmp);
            return *this;
        }
        */
        // In C++11, this is unifying assignment operator
        String &operator=(String rhs) // yes, pass-by-value
        {
            // http://en.wikibooks.org/wiki/More_C++_Idioms/Copy-and-swap
            swap(rhs);
            return *this;
        }

        // C++11 move-ctor
        String(String &&rhs) noexcept
            : data_(rhs.data_)
        {
            rhs.data_ = nullptr;
        }

        /* Not needed if we have pass-by-value operator=() above, and it conflits.
        http://stackoverflow.com/questions/17961719/
        String& operator=(String&& rhs)
        {
            swap(rhs);
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
命令：`g++ -O1 -g -Wall -o test StringTrivialTest.cpp && ./test`
```c++
#include "StringTrivial.h"
#include <vector>

using namespace kid;

void foo(String x) {}

void bar(const String &x) {}

String baz()
{
    String ret("world");
    return ret;
}
int main()
{
    String s0;
    String s1("hello");
    String s2(s0);
    String s3(s1);
    s2 = s1;
    s3 = s3;
    s1 = "awesome";

    foo(s1);
    bar(s1);
    foo("temp");
    bar("temp");
    String s4 = baz();
    s4 = baz();

    std::vector<String> svec;
    svec.push_back(s0);
    svec.push_back(s1);
    svec.push_back("good job");
    return 0;
}

```
