# Linux内核模块

## 本章要求

1. 了解Linux内核模块的概念和用法。
2. 掌握编写一个简单内核模块的方法。
3. 了解内核模块参数的传递方式。
4. 学会利用内核模块创建一个虚拟字符设备。
5. 了解如何通过数组参数传递给内核模块。

## 主要内容

- [编写一个简单的内核模块](./chapter-5_1.md)
- [内核模块传参](./chapter-5_2.md)
- [创建字符设备](./chapter-5_3.md)
- [实现计时器](./chapter-5_4.md)
- [实现简单文件操作](./chapter-5_5.md)
- [Linux系统编程基础](./chapter-5_6.md)

## 注意

本章节为习题前置知识，而非正式课程内容，但建议先学习本章节内容再进行相关习题。


## 前提条件
要跟随本教程，您需要具备以下前提条件：
- 熟练掌握C编程
- 基本的Linux命令行知识
- 已设置好Linux开发环境（例如，运行Linux发行版并安装了`make`和`gcc`）

## 本章简介

本教程为编写 Linux 内核模块提供了一个起点。它涵盖了模块参数传递、创建虚拟字符设备、启动定时器以及使用简单的文件操作。对于更高级的主题，请参考《Linux 内核模块编程指南》和其他内核开发资源。


## 参考文献

- [Linux Kernel Module Programming Guide](https://tldp.org/LDP/lkmpg/2.6/html/)
- [Linux Device Drivers, Third Edition](https://lwn.net/Kernel/LDD3/)
- [Kernel API Documentation](https://www.kernel.org/doc/html/latest/)

