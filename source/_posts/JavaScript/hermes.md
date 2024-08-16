---
title: Hermes
layout: post
author: LuyiaGoe
categories: Javascript
cover: false
coverImg: ""
top: false
mathjax: false
tags:
  - Front-End
excerpt: post
summary: ""
date: 2023-10-30 14:32:36
---

# Hermes

## 简介

Facebook 在 ChainReact2019 大会上正式推出了新一代 JavaScript 执行引擎 Hermes。Hermes 是个轻量级的 JS 引擎，专门对 Android 上运行 ReactNative 进行了优化。

起初 RN 是用 JavaScriptCore 作为引擎的，由于 JavaScriptCore 最初是为桌面浏览器端设计，相较于桌面端，移动端能力有太多的限制，为了能从底层对移动端进行性能优化，Facebook 团队选择自建 JavaScrip 引擎，设计了 Hermes，限于 iOS AppStore 审核限制，目前仅用于 Android 平台。

### Hermes 优化方法

主流JavaScript引擎，例如JSC、V8、SpiderMonkey等几乎都是为了桌面端浏览器服务的，Hermes针对移动终端设备的特点做了一些优化

不像主流引擎通过 JIT (just-in-time) 以占用大量内存的方法，将 JS 实时编译为二进制的方法提升执行速度，Hermes 的一大特点是支持字节码，Hermes 采用了 AOT (ahead-of-time) 将编译环节放在了开发环节而非运行环节

![hermes](../../assets/posts/javascript/hermes.gif)

在运行时解析源码转换字节码是一种时间浪费，所以Hermes选择预编译的方式在编译期间生成字节码。这样做一方面避免了不必要的转换时间，另一方面多出的时间可以用来优化字节码，从而提高执行效率。

因此同时 Hermes 抛弃了 JIT（放弃运行时Hermes引擎对纯文本JS代码的编译优化），认为其在移动设备上有两方面的问题：

- 要在启动时候预热，对启动时间有影响；
- 会增加引擎size大小和运行时内存消耗；

### 弊端

#### bytecode文件占用size过大问题

携程团队在 2019 年测试 APP 中 RN 部分的代码如果从纯文本 JavaScript 文件换成字节码文件，体积将会增大 100%

但他们利用了 Hermes 的特性，将编译过程放回客户端，客户端将会同时存储两种文件，不过是在字节码文件被编译出来前，仍然先加载旧有的 js 文件，直到字节码文件被编译完成

#### 执行纯文本 JS 速度慢

Hermes 因为删除了 JIT 功能，其执行纯文本 JS 速度比 JavaScriptCore 的速度慢 30%

## 获取与编译

### 依赖

Hermes 是一个 C++14 工程，因此需求 `clang`、`gcc`、`Visual C++`。除此之外，还需要 `cmake`、`git`、`ICU`、`python`、`zip`。由 `cmake` 和 `ninja` 工具构建

在 Linux 上安装这些依赖

```shell
$ sudo apt install cmake git ninja-build libicu-dev python3 zip libreadline-dev
```

在 Arch Linux 上安装这些依赖

```shell
$ pacman -S cmake git ninja icu python zip readline
```

在 Mac 通过 Homebrew 安装这些依赖

```shell
$ brew install cmake git ninja
```

### 在 Linux 或 MacOS 上构建

默认情况下，Hermes会将其构建文件放在当前目录中。当然也可以提供明确的源目录和构建目录，使用构建脚本上的--help来了解如何操作。

创建一个工作的基本目录，例如~/workspace，并 cd 进入其中。（提示：避免将其命名为hermes，因为hermes将是工作区中的几个子目录之一）。改变目录之后，按照以下步骤生成Hermes构建系统：

```shell
git clone https://github.com/facebook/hermes.git
cmake -S hermes -B build -G Ninja
```

构建系统现在已在生成目录中预备。要执行构建，请执行以下操作：

```shell
cmake --build ./build
```

### 创建发布版本

上面只是创建了一个未优化的调试构建。`-DCMAKE_BUILD_TYPE=Release` 标志将创建一个发布版本：

```shell
cmake -S hermes -B build_release -G Ninja -DCMAKE_BUILD_TYPE=Release
cmake --build ./build_release
```

## 运行 Hermes

可以通过 Hermes 直接执行 js 文件

```shell
$ hermes test.js
```

也可以将js编译为字节码文件并执行：

```shell
$ hermes -emit-binary -out test.hbc test.js
$ hermes test.hbc
```