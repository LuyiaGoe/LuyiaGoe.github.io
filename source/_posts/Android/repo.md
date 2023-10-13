---
title: repo
layout: post
author: LuyiaGoe
categories: Android
cover: false
coverImg: ""
top: false
mathjax: false
tags:
  - Entry
excerpt: post
summary: ""
date: 2023-10-13 11:30:08
---

# repo

repo 是谷歌用 python 脚本写的调用 git 的一个脚本，并不是用于取代 git，它简化了对多个 git 版本库的管理。用 repo 管理的版本库都需要使用 git 命令来进行操作。
大型项目模块化/组件化之后，各模块也作为独立的 git 仓库从主项目里剥离了出去，各模块各自管理自己的版本。每一个子项目都是一个 git 仓库，每个 git 仓库都有很多分支版本，为了方便统一管理各个子项目的 git 仓库，需要一个上层工具批量进行处理，因此诞生了 repo

## Linux 安装

1. 从清华源中安装 repo
   ```bash
   $ mkdir ~/bin
   $ PATH=~/bin:$PATH
   $ curl https://mirrors.tuna.tsinghua.edu.cn/git/git-repo > ~/bin/repo
   $ chmod a+x ~/bin/repo
   ```
2. 添加或修改 REPO_URL

   ```bash
   $ vim ~/.bashrc
   ```

   在文件空行添加 export REPO_URL=‘https://mirrors.tuna.tsinghua.edu.cn/git/git-repo/’ 已有就修改后面的地址

   保存后同步 bashrc 文件

   ```bash
   $ source ~/.bashrc
   ```

3. 准备 python 环境
   ```bash
   $ sudo apt-get install python3
   $ sudo ln -s /usr/bin/python3 /usr/bin/python
   ```
4. 初始化 repo
   ```bash
   $ repo init -u https://aosp.tuna.tsinghua.edu.cn/platform/manifest -b <仓库名>
   $ repo sync -c -j8
   ```
5. 其他 lib 库安装
   ```bash
   $ sudo apt-get install -y gnupg flex bison build-essential zip zlib1g-dev gcc-multilib g+±multilib libc6:i386 lib32ncurses-dev x11proto-core-dev libx11-dev lib32z1-dev libgl1-mesa-dev libxml2-utils xsltproc unzip fontconfig openjdk-8-jdk vim libncurses5
   ```
