---
title: Server优雅启停
date: 2021-07-23 10:32:12
description: C++服务优雅启动停止
tags: 
    - C++
    - shell
---

## 背景

之前写 C++ 服务运行停止都比较粗暴，这里记录个小脚本

## 代码

**start.sh**

```shell

nohup ./test 2>&1 &
echo $! > pid

```

**stop.sh**

```shell
PIDFILE="$PWD/pid"
PID=$(cat $PIDFILE)
kill -9 $PID
```

**signal_handler.cpp**

```c++

#include <signal.h>

#include <iostream>

static volatile bool is_running = true;

// sigint sigkill sigterm
static void sigterm_handler(int sig)
{
    std::cout << "Receive Signal: " << sig;
    is_running = false;
}

// sigchild
void sigchld_handler(int sig)
{
    while (waitpid(-1, 0, WNOHANG) > 0)
        ;
    return;
}

int main()
{
    signal(SIGCHLD, sigchld_handler); // child_process exit
    signal(SIGINT, sigterm_handler);  // ctrl -c
    signal(SIGTERM, sigterm_handler); // kill
    signal(SIGKILL, sigterm_handler); // kill -9

    return 0;
}
```