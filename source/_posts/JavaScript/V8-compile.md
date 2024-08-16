---
title: V8
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
date: 2023-10-30 15:12:02
---

## 编译 V8

1. 下载工具

```shell
$ git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git
```

2. 设置环境变量，让之后的 gclient 可以直接调用

```shell
$ export PATH=$PATH:/path/to/depot_tools
```

3. 配置

```shell
$ gclient config https://chromium.googlesource.com/v8/v8.git
```

4. 执行 gclient sync (成功后当前目录下多一个 v8 目录，这时候有 depot_tools 和 v8 两个目录)
5. 执行命令：

```shell
$ alias gm=/code/v8_code/v8/tools/dev/gm.py
$ cd v8
```

6. 执行命令：

```shell
$ gm x64.release
$ echo "console.log('hello world')" > test.js
$ ./out/x64.release/d8 test.js
```

## 编译 V8 为静态库

1. 执行

```shell
$ alias v8gen=/code/v8_code/v8/tools/dev/v8gen.py
```

2. v8 源码目录执行

```shell
# 生成配置文件
$ v8gen x64.release.sample
$ ninja -C out.gn/x64.release.sample v8_monolith
```
