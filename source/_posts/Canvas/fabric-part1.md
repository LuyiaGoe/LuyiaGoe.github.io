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

## Fabric.js 简介

[fabric.js](http://fabricjs.com/) 是一个可以让 HTML5 Canvas 开发变得简单的框架 。 它是一种基于 Canvas 元素的 可交互 对象模型，也是一个 SVG 到 Canvas 的解 析器（让SVG 渲染到 Canvas 上）。

它是一个用 Canvas 实现的对象模型。如果需要用 HTML Canvas 来绘制一些东西，并且这些东西可以响应用户的交互，比如：拖动、变形、旋转等 操作。 那用 fabric.js 是非常合适的，因为它内部不仅实现了 Canvas 对象模型，还将一 些常用的交互操作封装好了，可以说是开箱即用。

内部集成的主要功能如下：

- 几何图形绘制，如：形状（圆形、方形、三角形）、路径
- 位图加载、滤镜
- 自由画笔工具，笔刷
- 文本、富文本渲染
- 模式图像
- 对象动画
- Canvas 对象之间的序列化与反序列化

## 基础使用

```shell
$ yarn add fabric
```

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <script src="https://rawgit.com/fabricjs/fabric.js/master/dist/fabric.js"></script>
  </head>
  <body>
    <canvas id="c" width="300" height="300" style="border:1px solid #ccc"></canvas>
    <script>
      (function() {
        // c 即 id="c" 的 canvas
        var canvas = new fabric.Canvas('c');
        // 创建一个矩形
        var react = new fabric.Rect({...});
        // 将矩形添加到画布中
        canvas.add(react);
        // 获取画布中第一个对象（也就是最早被 add 的对象）
        canvas.item(0);
        // 获取画布中所有对象
        canvas.getObjects();
        // 从画布上移除矩形
        canvas.remove(rect); // remove previously-added fabric.Rect
      })();
    </script>
  </body>
</html>
```

### 常用形状

在 fabric 中有7种常用形状

- fabric.Circle: 圆形
- fabric.Ellipse: 椭圆
- fabric.Line: 直线
- fabric.Polygon: 多边形
- fabric.Polyline: 多段线
- fabric.Rect: 矩形
- fabric.Triangle: 三角形

如果想要在画布中画出这些形状，只需要用 add 方法把形状对象添加进画布即可：

```js
var circle = new fabric.Circle({
  radius: 20, fill: 'green', left: 100, top: 100
});
var triangle = new fabric.Triangle({
  width: 20, height: 30, fill: 'blue', left: 50, top: 50
});

canvas.add(circle, triangle);
```

### Image

可以通过 fabric.Image 对象将图片添加进画布上：

```html
<canvas id="c"></canvas>
<img src="my_image.png" id="my-image">
```

```js
var canvas = new fabric.Canvas('c');
var imgElement = document.getElementById('my-image');
// 可以直接将 img DOM 生成为 fabric.Image 对象
var imgInstance = new fabric.Image(imgElement, {
  left: 100,
  top: 100,
  angle: 30,
  opacity: 0.85
});
canvas.add(imgInstance);

// 也可以使用 URL 添加图片
fabric.Image.fromURL("my_image.png", function(oImg) {
  // 此时可以在回调中控制实例对象
  oImg.scale(0.5).set('flipX', true);
  canvas.add(oImg);
});
```
### Path & Groups

当想要创建更复杂的形状的时候，可以通过 `Path` 和 `Groups` 围出想要的形状对象

Fabric中的 `Path` 表示形状的轮廓，该轮廓可以通过其他方式填充、描边和修改。路径由一系列命令组成，这些命令基本上模拟了笔从一个点到另一个点的移动。

在 `move`、`line`、`curve` 或 `arc` 等命令的帮助下，路径可以形成极其复杂的形状。在路径组（PathGroup）的帮助下，可能性更加开放。

```js
var canvas = new fabric.Canvas('c');
// 1. “M”表示“移动”命令，并告诉不可见的笔移动到0，0点
// 2. “L”代表“线”，并使钢笔画一条线到200，100点
// 3. 另一个“L”创建一条到170200的直线
// 4. “z”告诉强制绘图笔关闭当前路径并最终确定形状
var path = new fabric.Path('M 0 0 L 200 100 L 170 200 z');
path.set({ left: 120, top: 120, fill: 'red', stroke: 'green', opacity: 0.5 });
canvas.add(path);
```

Fabric中的 `path` 与 `SVG＜path＞` 元素非常相似。它们使用相同的命令集，因此可以从 `＜path＞` 元素创建，并将其序列化。
但手工创建一个路径并不是一个好的方法，因为较为复杂，比如：

```js
...
var path = new fabric.Path('M121.32,0L44.58,0C36.67,0,29.5,3.22,24.31,8.41\
c-5.19,5.19-8.41,12.37-8.41,20.28c0,15.82,12.87,28.69,28.69,28.69c0,0,4.4,\
0,7.48,0C36.66,72.78,8.4,101.04,8.4,101.04C2.98,106.45,0,113.66,0,121.32\
c0,7.66,2.98,14.87,8.4,20.29l0,0c5.42,5.42,12.62,8.4,20.28,8.4c7.66,0,14.87\
-2.98,20.29-8.4c0,0,28.26-28.25,43.66-43.66c0,3.08,0,7.48,0,7.48c0,15.82,\
12.87,28.69,28.69,28.69c7.66,0,14.87-2.99,20.29-8.4c5.42-5.42,8.4-12.62,8.4\
-20.28l0-76.74c0-7.66-2.98-14.87-8.4-20.29C136.19,2.98,128.98,0,121.32,0z');

canvas.add(path.set({ left: 100, top: 200 }));
```

与其如此，不如使用 `fabric.loadSVGFromString` 或 `fabric.loadSVGFromURL` 方法来加载整个SVG文件，并让fabric的SVG解析器遍历所有SVG元素并创建相应的Path对象。

谈到整个SVG文档，Fabric的 `Path` 通常表示 `SVG＜Path＞` 元素，这是SVG文档中经常出现的一组路径，它们被表示为`Groups`（Fabric.Group实例）。`Groups` 不过是一组路径和其他对象。并且它可以像任何其他对象一样添加到画布中，并以相同的方式进行操作。

### 操作对象

fabric 负责渲染和状态管理，因此对于画布上的图形，可以直接对这些图形对象进行修改：

```js
var canvas = new fabric.Canvas('c');
...
canvas.add(rect);

rect.set('fill', 'red');
rect.set({ strokeWidth: 5, stroke: 'rgba(100,200,200,0.5)' });
rect.set('angle', 15).set('flipY', true);
```