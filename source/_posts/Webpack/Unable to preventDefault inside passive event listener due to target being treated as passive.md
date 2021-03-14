---
title: Unable to preventDefault inside passive event listener due to target being treated as passive
layout: post
author: LuyiaGoe
categories: Webpack
tags: Webpack
date: 2021-03-14 16:53:34
---
## 问题描述
翻译：chrome 监听touch类事件报错：无法被动侦听事件preventDefault
<br>
<br>
这是chrome浏览器报错，目的是为了最快速地相应touch事件而做出的改变
<br>
<br>
因为preventDefault()是取消默认事件的，如果这个函数起作用的话，比如默认的表单提交，a链接的点击跳转，就不好用了
<br>
<br>
历史：当浏览器首先对默认的事件进行响应的时候，要检查一下是否进行了默认事件的取消。这样就在响应滑动操作之前有那么一丝丝的耽误时间
<br>
<br>
现在：google就决定默认取消了对这个事件的检查，默认时间就取消了。直接执行滑动操作。这样就更加的顺滑了。但是浏览器的控制台就会进行错误提醒了
<br>
<br>
具体情况：从 chrome56 开始，在 window、document 和 body 上注册的 touchstart 和 touchmove 事件处理函数，会默认为是 passive: true。浏览器忽略 preventDefault() 就可以第一时间滚动了
<br>
<br>
导致下面的效果一致：
```javascript
wnidow.addEventListener('touchmove', func) 效果和下面一句一样
wnidow.addEventListener('touchmove', func, { passive: true })
```
这样会出现新的问题:
<br>
如果在以上这 3 个元素的 touchstart 和 touchmove 事件处理函数中调用 e.preventDefault() ，会被浏览器忽略掉，并不会阻止默认行为

## 解决方法
1. 注册处理函数时，用如下方式，明确声明为不是被动的
<br>
window.addEventListener(‘touchmove’, func, { passive: false })
2. 应用 CSS 属性 touch-action: none; 这样任何触摸事件都不会产生默认行为，但是 touch 事件照样触发