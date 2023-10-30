---
title: fabric.js———笔记 part.1
layout: post
author: LuyiaGoe
categories: Front-End
cover: false
coverImg: ''
top: false
mathjax: false
tags:
  - Front-End
  - canvas
excerpt: post
summary: ''
date: 2023-10-28 10:12:11
---

## 动画

### 动画调用

每一个 fabric 对象都内设了 `animate` 方法使其可以进行动画播放：

```js
// 第一个参数是要设置动画的属性。第二个参数是动画的结束值。如果矩形的角度为-15°，并且我们经过45°，它将被设置为从-15°到45°的动画。第三个参数是一个可选对象，指定动画的更精细的细节——持续时间、回调、easing等。
rect.animate('angle', 45, {
  onChange: canvas.renderAll.bind(canvas)
});

// animate的一个方便功能是它还支持相对值
rect.animate('left', '+=100', { onChange: canvas.renderAll.bind(canvas) });

// 类似地，逆时针旋转物体5度，可以这样完成：
rect.animate('angle', '-=5', { onChange: canvas.renderAll.bind(canvas) });
```

在第三个参数中，animate在每次更改后不会自动重新渲染画布的原因是由于性能。在存在许多对象的情况下，可以使用requestAnimationFrame（或其他基于计时器的）循环来连续渲染画布，而无需为每个对象调用renderAll。但大多数时候，可能需要显式地将canvas.renderAll指定为“onChange”回调。

第三个参数还能添加以下几个可选项：

- from：允许指定可设置动画的属性的起始值（如果我们不希望使用当前值）。
- duration：默认值为500（ms）。可用于更改动画的持续时间。
- onComplete：在动画结束时调用的回调。
- easing：easing函数，默认值为 `easeInSine`。

`fabric.util.ease` 下有一系列宽松选项。例如，如果我们想以有弹性的方式将对象向右移动：

```js
rect.animate('left', 500, {
  onChange: canvas.renderAll.bind(canvas),
  duration: 1000,
  easing: fabric.util.ease.easeOutBounce
});
```

### fabric.runningAnimations

当前由fabric运行的动画是一个对象数组，每个对象都是动画上下文对象

包括如下方法：

- fabric.runningAnimations.findAnimation(signature): 返回动画上下文匹配签名，该签名是fabric.util.animate返回的中止函数。
- fabric.runningAnimations.findAnimationIndex(signature): 与findAnimation相同，返回索引。
- fabric.runningAnimations.findAnimationsByTarget(target): 返回目标属性等于target的所有动画。
- fabric.runningAnimations.cancelAll(): 取消所有正在运行的动画。
- fabric.runningAnimations.cancelByTarget(target): 取消目标属性等于target的动画。
- object.dispose(): 取消由(object.animate(…))创建的所有动画。如果要使用fabric.util.animate而不是object.animate(…)添加动画，可以通过传递target属性将其附加到对象。这样，一旦对象被释放，动画就会取消。

```js
let cancel = fabric.util.animate({...});
let i = fabric.runningAnimations.findAnimationIndex(cancel);
let context = fabric.runningAnimations.findAnimation(cancel);
let cancelled = fabric.runningAnimations.cancelAll();
 
//  the following statements are true
cancelled[i] === context;
cancelled[i].cancel === cancel;
fabric.runningAnimations.length === 0;
```

### 图片过滤器

默认情况下，Fabric只提供很少的过滤器，例如：

```js
// 创建一个灰度图像
fabric.Image.fromURL('pug.jpg', function(img) {

  // add filter
  img.filters.push(new fabric.Image.filters.Grayscale());

  // apply filters and re-render canvas when done
  img.applyFilters();
  // add image onto canvas (it also re-render the canvas)
  canvas.add(img);
});
```

```js
fabric.Image.fromURL('pug.jpg', function(img) {
  img.filters.push(new fabric.Image.filters.Sepia(), ...);
  img.applyFilters();
  // add image onto canvas (it also re-render the canvas)
  canvas.add(img);
});
```

### 创建过滤器

创建过滤器的模板非常简单。需要创建一个“类”，然后定义applyTo方法。可以可选地为JSON方法提供过滤器（支持JSON序列化）和/或初始化方法（支持可选参数）。

```js
fabric.Image.filters.Redify = fabric.util.createClass(fabric.Image.filters.BaseFilter, {

  type: 'Redify',

  /**
   * Fragment source for the redify program
   */
  fragmentSource: 'precision highp float;\n' +
    'uniform sampler2D uTexture;\n' +
    'varying vec2 vTexCoord;\n' +
    'void main() {\n' +
      'vec4 color = texture2D(uTexture, vTexCoord);\n' +
      'color.g = 0.0;\n' +
      'color.b = 0.0;\n' +
      'gl_FragColor = color;\n' +
    '}',

  applyTo2d: function(options) {
    var imageData = options.imageData,
        data = imageData.data, i, len = data.length;

    for (i = 0; i < len; i += 4) {
      data[i + 1] = 0;
      data[i + 2] = 0;
    }

  }
});

fabric.Image.filters.Redify.fromObject = fabric.Image.filters.BaseFilter.fromObject;
```