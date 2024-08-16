---
title: JavaScriptCore
layout: post
author: LuyiaGoe
categories: JavaScript
cover: false
coverImg: ""
top: false
mathjax: false
tags:
  - Front-End
excerpt: post
summary: ""
date: 2023-10-31 11:07:04
---

## 编译 jsc

1. 下载工具

```shell
$ sudo apt install libicu-dev python ruby bison flex cmake build-essential ninja-build git gperf
```

2. 获取代码

```shell
$ git clone https://github.com/WebKit/WebKit.git

```

3. 仅仅编译 JSC

```shell
$ Tools/Scripts/build-webkit --jsc-only
```

4. 也可以用静态 JSC 库构建 JSC shell

```shell
$ Tools/Scripts/build-webkit --jsc-only --cmakeargs="-DENABLE_STATIC_JSC=ON -DUSE_THIN_ARCHIVES=OFF"
```