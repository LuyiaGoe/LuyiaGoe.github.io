---
title: Vue 给UI库添加按需加载时启动项目时 babel-preset-es2015 报错
layout: post
author: LuyiaGoe
categories: Vue
tags:  Vue
excerpt: Vue 给UI库添加按需加载时启动项目时 babel-preset-es2015 报错
date: 2021-03-11 16:10:34
---
## 问题描述

项目使用vue cli3脚手架工具构建
按照element 官方文档中所示

`npm install babel-plugin-component -D`

然后添加.babelrc文件

```javascript
{
  "presets": [["es2015", { "modules": false }]],
  "plugins": [
    [
      "component",
      {
        "libraryName": "element-ui",
        "styleLibraryName": "theme-chalk"
      }
    ]
  ]
}
```

报如下错误：

```javascript
Error: Cannot find module 'babel-preset-es2015' from 'C:\Users\Administrator\Des
ktop\vueProject\vuedemo'
```
## 解决方法

### 1.此时应该安装@babel/preset-env

`npm i @babel/preset-env -D`

### 2.并且修改.babelrc文件中的'presets'属性

```javascript
{
  "presets": [["@babel/preset-env", { "modules": false }]],
  "plugins": [
    [
      "component",
      {
        "libraryName": "element-ui",
        "styleLibraryName": "theme-chalk"
      }
    ]
  ]
}
```

解决问题