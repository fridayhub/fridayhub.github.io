---
title: "用travis自动构建Hugo的Github Pages[转]"
date: 2017-12-20T23:59:24+08:00
lastmod: 2017-12-20T23:59:24+08:00
draft: false
keywords: ["CI", "travis"]
description: ""
tags: ["travis", "CI"]
categories: ["CI"]
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
博客搭建好后，一个重要的问题就是每次有新写的md文件难道要手动执行hugo，然后将public文件夹推送到github pages仓库？显然这不是我想要的。
<!--more-->

在网络上搜索了一下，发现可以借助一些CI/CD工具来帮助我完成这件事。CI/CD全称Continuous Integration and Delivery，中文翻译过来就是持续集成和交付。原理是借助git的hook功能，监控git仓库的提交，然后触发自动构建，自动部署。

其中，travis.io是在线的用的比较多的一个CI/CD工具，而且可以免费使用。使用方法还算简单，就是在项目根目录创建一个.travis.yml文件，文件中按照其语法，声明项目运行的环境和构建脚本。
以下是我的博客的构建脚本
``` yml
  langguage: go

  go:
    - master
  install:
    - wget https://github.com/gohugoio/hugo/releases/download/v0.31.1/hugo_0.31.1_Linux-64bit.tar.gz
    - tar -zxf hugo_0.31.1_Linux-64bit.tar.gz
    - chmod a+x hugo

  script:
    - ./hugo

  deploy:
    provider: pages
    skip_cleanup: true
    github_token: $GITHUB_TOKEN
    on:
      branch: master
    local_dir: ./public
    repo: hulb/hulb.github.io
    email: hanglinux@163.com
    target_branch: master
```
以上是表示使用go的运行环境，go版本选择为master版本，这是从[travis文档](https://docs.travis-ci.com/user/languages/go/ "travis文档")学习来的。

使用travis我们可以自动对Push到博客仓库的md文件执行hugo，生成public文件夹，那么如何把这个文件夹推送到对应的pages仓库呢？这就需要用到github的access token。在github的setting->developer setting中有一个personal access token，根据travis文档教程生成一个新的access token，填入travis环境变量里，然后我们在.travis.yml中就可以用到，就如上面的@GITHUB_TOKEN。

接下来设置部署的命令，就是deploy这一节。根据[travis有关github pages的文档](https://docs.travis-ci.com/user/deployment/pages/)填入一些配置，这里有个坑，repo只用写你的github用户名/项目名就行了，不用写完成的git clone的地址，一直在这个地方搞错了好几次。
然后就是推送一个更新到博客源文件仓库，触发自动构建和部署。
