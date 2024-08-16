---
title: Quickjs
layout: post
author: LuyiaGoe
categories: JavaScript
cover: false
coverImg: ""
top: true
mathjax: false
tags:
  - Front-End
excerpt: post
summary: ""
date: 2023-10-30 15:24:21
---

## 简介

QuickJS 只有 210KB，体积小，启动快，解释执行速度快，支持最新 ECMA2020 标准（ECMA-262）。

QuickJS 还支持 JS 与 C 的交互，支持 JS 通过像导入 JS 模块的方式调用 C 的方法。

## 编译

1. 获取源码并编译

```shell
$ git clone https://github.com/bellard/quickjs.git
$ cd quickjs && make
```

2. 设置全局变量

```shell
$ export PATH=$PATH:/path/to/quickjs
$ source ~/.bashrc
```

## 使用

编译完成后，在 quickjs 文件目录下将会生成可执行文件 qjs 所谓命令行解析器：

```shell
$ qjs test.js
```

也可以用命令行编译器 qjsc 将 js 文件编译为一个没有外部依赖的可知性文件：

```shell
$ ./qjsc -o test ./test.js
$ ./test
```

qjsc 还可以把 js 文件编译成.c 文件:

```shell
$ ./qjsc -e -o test.c ./test.js

```

## JS 与 C

可以在 JS 代码中导入一个 C 库，并执行其中的方法。比如 QuickJS 中的 fib.c 文件，其函数 js_fib 就是对 js 里可调用的 fib 函数的实现，使用的是 JS_CFUNC_DEF 宏来做 js 方法和对应 c 函数的映射。映射代码如下：

```c
static const JSCFunctionListEntry js_fib_funcs[] = {
    JS_CFUNC_DEF("fib", 1, js_fib ),
};
```

此时即可在 js 中调用：

```js
import { fib } from "./fib.so";
var f = fib(10);
```
