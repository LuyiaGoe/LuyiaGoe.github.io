---
title: 实现call、apply、bind
date: 2020-2-17 10:16:46
layout: post
categories: JavaScript
tags: Polyfill
excerpt: 实现call、apply、bind
---

# 面试题：实现call、apply、bind

## 实现call

```js
Function.prototype.myCall = function(context){
    // 首先判断调用者是不是函数
    if(typeof this !== 'function'){
      return console.log('type error')
    }
    // 处理传入参数
    var args = [...arguments].slice(1)
    var result = null
    // 判断是否有传入上下文,未传入则设为全局上下文
    context = context || window
    // 将调用函数设为context的方法
    context.fn = this
    // 调用
    result = context.fn(...args)
    return result;
}
```
apply实现方法类似，只需要更改传入参数的形式即可
## bind

```js
Function.prototype.myBind = function(context){
  if(typeof this !== 'function'){
      return console.log('type error')
    }
  context = context || window;
  var args = [...arguments].slice(1)
  // 保存当前调用函数
  fn = this;
  // 创建一个函数返回
  return function Fn(){
    // 根据不同的方式，传入不同的绑定值
    return fn.apply(
      this instanceof Fn ? this : context,
      args.concat(...arguments)
    )
  }
}
```


