---
title: "Clion Run Linux Kernel"
date: 2020-12-27T10:44:47+08:00
lastmod: 2020-12-27T10:44:47+08:00
draft: false
keywords: []
description: ""
tags: []
categories: []
author: ""

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: false
toc: false
autoCollapseToc: false
# You can also define another contentCopyright. e.g. contentCopyright: "This is another copyright."
contentCopyright: false
reward: false
mathjax: false
---

<!--more-->

从2020.2版本开始，clion已经支持基于Makefile的工程了，不过还是比较麻烦，可以参考官方文档：[Dealing with Makefile Projects in CLion: Status Update](https://blog.jetbrains.com/clion/2020/02/dealing-with-makefiles/)

本文使用的是option2的方式：

1、 安装bear

bear是在编译过成功记录编译文件的依赖，生成compile_commands.json。
不要使用源码安装方式，因为编译的时候，会从github和墙外下载很多以来，非常慢；mster分支可能编译不通过；最快的方式 sudo apt install bear 

2、 安装kernel-grok

kernel-grok 基于compile_commands.json，生成CMakeLists.txt，方便clion解析。
```bash
sudo apt install ruby
git clone https://github.com/habemus-papadum/kernel-grok.git
cd clion-linux-kernel-3.16
```

3、编译方式：

 1. 进入内核目录，生成 .config, 可以使用默认的，也可以基于某个系统拷贝一份： cp /boot/config-.x.xxx .config
 2. 编译bear make
 3. ../kernel-grok/generate_cmake， 此时会生成CMakeList.txt,打开此文件，改为：
 ```
cmake_minimum_required(VERSION 2.8.8)
project(kernel)
SET(CMAKE_C_COMPILER "gcc")
include_directories(".")
include_directories("./include")                                         
add_library(kernel OBJECT

```
 4. 打开clion即可。