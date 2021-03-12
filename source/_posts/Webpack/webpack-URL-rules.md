---
title: webpack 中 使用 url-loader 后图片src="[object Module]" 导致图片无法显示
layout: post
author: LuyiaGoe
categories: Webpack
tags:  Webpack
date: 2021-03-12 13:14:03
---

# webpack 中 使用 url-loader 后图片src="[object Module]" 导致图片无法显示

## 问题描述
在webpack.config.js文件中,对图片的匹配规则如下：
```javascript
module.exports={
  module:{
    rules:[
      { test: /\.jpg|png|gif|bmp|jpeg$/, use: 'url-loader?limit=7630&name=[hash:8]-[name].[ext]' }
    ]
  }
}
```
打包后网页中显示图片为：
![Alt](https://LuyiaGoe.github.io/assets/posts/object-Module1.png#pic_center =30x30)

## 解决方法
在webpack.config.js文件中,对匹配规则修改如下：
```javascript
module.exports={
  module:{
    rules:[
      { test: /\.jpg|png|gif|bmp|jpeg$/, use: 'url-loader?limit=7630&name=[hash:8]-[name].[ext]&esModule=false' }
    ]
  }
}
```

修改完成后，显示为：
![Alt](https://LuyiaGoe.github.io/assets/posts/object-Module2.png#pic_center =30x30)