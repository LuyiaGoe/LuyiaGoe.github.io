---
title: Web Component
layout: post
author: LuyiaGoe
categories: Front-End
cover: false
coverImg: ''
top: true
mathjax: false
tags:
  - Front-End
  - Gaia
  - Component
  - JavaScript
excerpt: Web Component
summary: ''
date: 2021-11-22 20:41:05
---
# 前言

​		我们在不断地进行前端开发时，总会碰到需要用到之前做到的一些组件，此时直接找原来的代码整段复制粘贴效率并不是很理想——毕竟我们也不想在一大堆代码中找那么几行，还要理清样式，甚至更大可能做完上述工作后，还要再找它的方法进行修修改改。如此下来效率低下是个问题，万一这个组件某些部分跟项目其他冲突了又是个令人烦躁的事情，比如样式污染，原组件的方法与项目耦合度高等等。

​		此时，组件化开发的思想便能很好地解决上述问题，正好也有这么一套原生的技术提供给我们进行组件化开发，那便是`Web Components`：

> 利用这种技术，我们可以描述一个可重用的且带有自己方法、属性和事件等的类来创建自定义HTML元素，如此便能像使用内置标签一样使用自定义元素

# 使用简介

自定义元素（`Custom Element`）有两种

> **Autonomous custom elements （自主自定义标签）** —— “全新的” 元素, 继承自 `HTMLElement` 抽象类

> **Customized built-in elements （自定义内置元素）** —— 继承内置的 HTML 元素，比如自定义 `HTMLButtonElement` 等

## Autonomous custom elements

​		其中基本方法包括：

```js
class MyElement extends HTMLElement {
  constructor() {
    super();
    // 元素在这里创建
  }

  connectedCallback() {
    // 在元素被添加到文档之后，浏览器会调用这个方法，因此渲染也最好是放在这里
    // 但是在这里并不能用同步的方法访问到自定义元素的子元素（因为子元素还没连上DOM）
    //（如果一个元素被反复添加到文档／移除文档，那么这个方法会被多次调用）
  }

  disconnectedCallback() {
    // 在元素从文档移除的时候，浏览器会调用这个方法
    // （如果一个元素被反复添加到文档／移除文档，那么这个方法会被多次调用）
  }

  static get observedAttributes() {
    return [/* 属性数组，这些属性的变化会被监视 */];
  }

  attributeChangedCallback(name, oldValue, newValue) {
    // 当上面数组中的属性发生变化的时候，这个方法会被调用
  }

  adoptedCallback() {
    // 在元素被移动到新的文档的时候，这个方法会被调用
    // （document.adoptNode 会用到, 非常少见）
  }

  // 还可以添加更多的元素方法和属性
}
```

​		在申明上述方法后，需要进行组件注册：

```js
// 让浏览器知道我们新定义的类是为 <my-element> 服务的
customElements.define("my-element", MyElement);
```

​		如此每当页面中添加了一个`<my-element>`，`MyElement`类便会实例化一遍，如果没有注册组件，页面中的自定义元素将会被识别为一个未知元素，并且，**自定义元素必须包含一个短横线‘-’**。

### Tips

> `:not(:defined)` CSS 选择器可以对「未定义」的元素加上样式。



## Customized built-in elements

​		`Autonomous custom elements`创建的标签并不带语义，搜索引擎不会识别，同时无障碍设备也无法处理。因此为了解决以上问题我们可以通过继承`HTML`已有的元素来进行自定义拓展来解决。

​		其基本方法与`Autonomous custom elements`类似：

```html
<script>
	class HelloButton extends HTMLButtonElement { /* custom element 方法 */ }

	customElements.define('hello-button', HelloButton, {extends: 'button'});
</script>
<button is="hello-button">...</button>
```

​		

# 影子DOM

​		当我们采用封装的形式，就不想它会被项目其他的样式或者方法所影响，这时候我们需要将其隔离起来，就能用到影子`DOM`，

> Shadow DOM 为封装而生。它可以让一个组件拥有自己的「影子」DOM 树，这个 DOM 树不能在主文档中被任意访问，可能拥有局部样式规则，还有其他特性。

## 内建shadow DOM

​		我们可以在一个页面上放入`<input type="range">`，接着在开发者工具中打开「Show user agent shadow DOM」选项，我们就可以在文档树中看到如下结构：

![](https://LuyiaGoe.github.io/assets/posts/input.range.png)

​		这之中`#shadow-root` 下看到的就是被称为「shadow DOM」的东西。

## Shadow tree

​		一个DOM元素可以拥有两种DOM子树：

> Light tree（光明树） —— 一个常规 DOM 子树，由 HTML 子元素组成。我们在之前章节看到的所有子树都是「光明的」

> Shadow tree（影子树） —— 一个隐藏的 DOM 子树，不在 HTML 中反映，无法被察觉



​		如果一个元素同时有以上两种子树，那么浏览器只渲染 shadow tree，但是可以通过插槽结合两种树。影子树可以在自定义元素中被使用，其作用是**隐藏组件内部结构**和**添加只在组件内有效的样式**。

​		影子树的构建方式如下：

```html
<script>
customElements.define('show-hello', class extends HTMLElement {
  connectedCallback() {
    const shadow = this.attachShadow({mode: 'open'});  // 创建一个shadow tree
    shadow.innerHTML = `<p>
      Hello, ${this.getAttribute('name')}
    </p>`;
  }
});
</script>

<show-hello name="John"></show-hello>
```

其中：

1. 在每个元素中，我们只能创建一个 shadow root
2. `elem`  必须是自定义元素，或者是以下元素的其中一个：「article」、「aside」、「blockquote」、「body」、「div」、「footer」、「h1…h6」、「header」、「main」、「nav」、「p」、「section」或者「span」。其他元素，比如 `<img>`，不能容纳 shadow tree
3. `mode` 选项可以设定封装层级。他必须是以下两个值之一：

   - `「open」` —— shadow root 可以通过 `elem.shadowRoot` 访问
   -   `「closed」` —— `elem.shadowRoot` 永远是 `null
4. `attachShadow` 返回的 `shadow root`，和任何元素一样：我们可以使用 `innerHTML` 或者 DOM 方法，比如 `append` 来扩展它
5. 拥有`shadow root`的元素叫做「shadow tree host」，可以通过`shadow root`的`host`属性访问到
6. 影子树外的主文档的`JS`选择器无法获取它的`DOM`，样式同样也无法被影响到



# Shadow DOM插槽

​		当我们采用影子树时，外部文档将无法控制影子DOM，那怎么做到定制化使用组件呢？此时我们可以用光明树结合影子树做到控制组件，两者的关键便在于插槽。

## 具名插槽

举例：

```html
<script>
customElements.define('user-card', class extends HTMLElement {
  connectedCallback() {
    this.attachShadow({mode: 'open'});
    this.shadowRoot.innerHTML = `
      <div>Name:
        <slot name="username"></slot>
      </div>
      <div>Birthday:
        <slot name="birthday"></slot>
      </div>
    `;
  }
});
</script>

<user-card>
  <span slot="username">John Smith</span>
  <span slot="birthday">01.01.2001</span>
</user-card>
```

​		在 shadow DOM 中，`<slot name="X">` 定义了一个“插入点”，一个带有 `slot="X"` 的元素被渲染的地方。

​		然后浏览器执行"组合"：它从 light DOM 中获取元素并且渲染到 shadow DOM 中的对应插槽中。最后，正是我们想要的 —— 一个能被填充数据的通用组件。

​		这是编译后，不考虑组合的 DOM 结构：

```html

<user-card>
  #shadow-root
    <div>Name:
     <!-- 位置一 -->
      <slot name="username"></slot> 
    </div>
    <div>Birthday:
      <!-- 位置二 -->
      <slot name="birthday"></slot>
    </div>
  <!-- 会被填充入位置一 -->
  <span slot="username">John Smith</span>
  <!-- 会被填充入位置二 -->
  <span slot="birthday">01.01.2001</span>
</user-card>
```

​		渲染后，在开发者工具中我们可以看到：

```HTML
<user-card>
  #shadow-root
    <div>Name:
      <slot name="username">
        <!-- slotted element is inserted into the slot -->
        <span slot="username">John Smith</span>
      </slot>
    </div>
    <div>Birthday:
      <slot name="birthday">
        <span slot="birthday">01.01.2001</span>
      </slot>
    </div>
</user-card>
```

> 但是！这种扁平化DOM仅仅用作渲染和事件传播，是虚拟的，实际上的节点并没有被移动，可以通过`document.querySelector`验证。

> 
