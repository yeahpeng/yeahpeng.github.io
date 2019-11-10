title: Windows下vscode输出乱码
date: 2016-07-11 16:46:33
categories: skill
tags: 
- vscode
- Windows
- 乱码
---
　　在`Windows`下配置完`go`的开发环境。运行后，`OUTPUT`栏中是一堆乱码。<!-- more -->猜测，可能是`cmd`中的编码和`vscode`中不同引起，`vscode`的编码为：`UTF-8`，通过`CHCP`命令设置`cmd`的编码为`UTF-8`
```
CHCP 65001
```
　　设置之后，`OUTPUT`输出正常。