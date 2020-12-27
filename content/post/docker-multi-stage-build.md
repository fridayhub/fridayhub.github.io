---
title: "Docker 多阶段构建尝试[转]"
date: 2019-06-13T16:54:20+08:00
lastmod: 2019-06-13T16:54:20+08:00
draft: false
keywords: ["docker","multi stage"]
description: ""
tags: ["docker", "container"]
categories: ["docker"]
author: "hulb"

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: false
toc: false
autoCollapseToc: false
# You can also define another contentCopyright. e.g. contentCopyright: "This is another copyright."
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false
mathjax: false
---
这两天抽空买了一个域名，打算将博客放上来。想着用docker来跑应用，便想将博客构建成docker镜像然后直接运行。由于博客采用hugo生成，直接都是静态文件，所以只需要使用nginx官方镜像就可以了。我尝试了下docker多阶段构建，记录一下。

<!--more-->

先看看Dockerfile
```Dockerfile
FROM golang AS build
ADD . /hugo
WORKDIR /hugo
RUN wget https://github.com/gohugoio/hugo/releases/download/v0.55.6/hugo_0.55.6_Linux-64bit.tar.gz && tar -zxf hugo_0.55.6_Linux-64bit.tar.gz && chmod a+x hugo
RUN mkdir /blog
RUN ./hugo -d /blog

FROM nginx:alpine
COPY --from=build /blog /usr/share/nginx/html

```
先使用golang镜像作为基础，下载hugo程序，然后生成博客静态文件，放到/blog目录。然后使用nginx镜像将前一步的/blog目录复制一下就ok了。核心部分就是`COPY`命令的时候使用了一个`--from`。

接着我是使用了docker hub提供的自动构建功能，监控博客仓库的代码更新自动触发构建。下一步还可以添加docker hub上的webhooks，在镜像更新的时候自动更新服务器上的镜像然后重启博客。
