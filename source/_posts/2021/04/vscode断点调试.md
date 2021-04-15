---
title: vscode断点调试
date: 2021-04-15 17:35:24
description: 记录一下vscode的debug配置
tags:    
    - tool
---

记录一下vscode的debug配置，方便copy

## 调试C++
在目录`.vscode`下创建`launch.json`
```json
{
    // 使用 IntelliSense 了解相关属性。 
    // 悬停以查看现有属性的描述。
    // 欲了解更多信息，请访问: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "xxx调试",
            "type": "cppdbg",
            "request": "launch",
            // 启动程序
            "program": "${workspaceFolder}/build/ToyWebServer",
            // 运行参数
            "args": ["65535"],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb",
            "setupCommands": [
                {
                    "description": "为 gdb 启用整齐打印",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ],
            "miDebuggerPath": "/usr/bin/gdb",
            // "preLaunchTask": "make"
        }
    ]
}
```
在目录`.vscode`下创建`tasks.json`
```json
{
    // See https://go.microsoft.com/fwlink/?LinkId=733558
    // for the documentation about the tasks.json format
    "version": "2.0.0",
    "tasks": [
        {
            "label": "Run server",
            "type": "shell",
            // 运行命令
            "command": "./build/ToyWebServer 65535",
            // 参数
            // "args": ["-j8"],
            "problemMatcher": [],
            "group": {
                "kind": "build",
                "isDefault": true
            }
        }
    ]
}
```

## 调试python

todo
