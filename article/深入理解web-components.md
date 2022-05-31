# web-components 深入理解

<!-- @import "[TOC]" {cmd="toc" depthFrom=2 depthTo=3 orderedList=false} -->

<!-- code_chunk_output -->

- [自主定制元素](#自主定制元素)
- [自定义内置元素](#自定义内置元素)
- [组件生命周期方法](#组件生命周期方法)
  - [1. `constructor`](#1-constructor)
  - [2. `connectedCallback`](#2-connectedcallback)
  - [3. `attributeChangedCallback`](#3-attributechangedcallback)
  - [4. `adoptedCallback`](#4-adoptedcallback)
  - [5. `disconnectedCallback`](#5-disconnectedcallback)
- [`element.attachShadow(opt)`](#elementattachshadowopt)
  - [1. `mode`](#1-mode)
  - [2. `delegatesFocus`](#2-delegatesfocus)
- [几个工具方法](#几个工具方法)
  - [1. `window.customElements.get`](#1-windowcustomelementsget)
  - [2. `window.customElements.upgrade`](#2-windowcustomelementsupgrade)
  - [3. `window.customElements.whenDefined`](#3-windowcustomelementswhendefined)
  - [4. `element.shadowRoot.getRootNode(opt)`](#4-elementshadowrootgetrootnodeopt)
  - [5. `element.assignedSlot`](#5-elementassignedslot)
  - [6. `element.slot`](#6-elementslot)
  - [7. `slotchange`](#7-slotchange)
- [支持的 css 伪类](#支持的-css-伪类)
  - [1. `:host`](#1-host)
  - [2. `:host-context([选择器])`](#2-host-context选择器)
  - [3. `:defined`](#3-defined)
  - [4. `::slotted([选择器])`](#4-slotted选择器)
  - [5. `::part([选择器])`](#5-part选择器)
- [参数资料](#参数资料)

<!-- /code_chunk_output -->


## 自主定制元素

独立元素，它们不继承某个内置的具体 html 元素

看下面的代码示例：

* 定义一个自定义定制元素`CustomEle`，它直接继承自`HTMLElement`
* 在`connectedCallback`函数中，创建一个`div`，并给其塞入了两个元素：一个`img`和一个`h3`。并且这两个元素显示的值是从自主定制元素外部传进来的
* 通过`this.attributes`上的属性值，来引用从外部传给自主定制元素的属性值

```html
<custom-ele img=" https://img13.360buyimg.com/n1/jfs/t1/174969/5/11254/137611/60ac9df2E132ebe19/fb0626e40dd46917.jpg" text="我是一段说明">
</custom-ele>
```

```js
class CustomEle extends HTMLElement {
  constructor() {
    super();
  }

  connectedCallback() {
    const div = document.createElement("div");
    div.innerHTML = `
      <img src="${this.attributes.img.nodeValue}" width="200" />
      <h3>${this.attributes.text.nodeValue}</h3>
    `;
    this.appendChild(div);
  }
}

customElements.define("custom-ele", CustomEle);
```
在线示例：https://codepen.io/sjzcxc/pen/ExvqvEM

使用方式：

1. 直接作为 html 标签来使用：

```html
<custom-ele
  img="https://img13.360buyimg.com/n1/jfs/t1/174969/5/11254/137611/60ac9df2E132ebe19/fb0626e40dd46917.jpg"
  text="我是一段说明">
</custom-ele>
```

2. 在 js 中通过`document.createElement('custom-ele')`创建标签来使用：

```js
const customEle = document.createElement('custom-ele');
customEle.setAttribute('img', 'https://img13.360buyimg.com/n1/jfs/t1/174969/5/11254/137611/60ac9df2E132ebe19/fb0626e40dd46917.jpg');
customEle.setAttribute('text', '我是一段说明');
document.body.appendChild(customEle);
```

3. 在 js 中通过`new CustomEle()`实例来使用：

```js
const customEle = new CustomEle() // 注意，只是这里和上面不同
customEle.setAttribute('img', 'https://img13.360buyimg.com/n1/jfs/t1/174969/5/11254/137611/60ac9df2E132ebe19/fb0626e40dd46917.jpg');
customEle.setAttribute('text', '我是一段说明');
document.body.appendChild(customEle);
```

## 自定义内置元素

这种元素继承并扩展了内置的 html 元素

```html
<div is="custom-ele" img="https://img13.360buyimg.com/n1/jfs/t1/174969/5/11254/137611/60ac9df2E132ebe19/fb0626e40dd46917.jpg" text="我是一段说明">
</div>
```

```js
class CustomEle extends HTMLDivElement {
  constructor() {
    super();
  }

  connectedCallback() {
    const div = document.createElement("div");
    div.innerHTML = `
      <img src="${this.attributes.img.nodeValue}" width="200" />
      <h3>${this.attributes.text.nodeValue}</h3>
    `;
    this.appendChild(div);
  }
}

customElements.define("custom-ele", CustomEle, { extends: "div" });
```

在线示例：https://codepen.io/sjzcxc/pen/XWavaYO

使用方式：

1. 直接作为 html 元素来使用：

这里在使用的时候，不能直接自定义元素名称，而必须使用一个`div`元素，并使用`is`属性给其指定要渲染的自定义元素是什么

这里必须要使用`div`，是因为上面的自定义元素继承于它。如果上面换成了其它元素，那么下面在使用的时候也要跟着换。保持一致即可

```html
<div is="custom-ele"
  img="https://img13.360buyimg.com/n1/jfs/t1/174969/5/11254/137611/60ac9df2E132ebe19/fb0626e40dd46917.jpg"
  text="我是一段说明">
</div>
```

2. 在 js 中通过`document.createElement('custom-ele')`创建标签来使用：

注意，这里创建元素的时候就和上面不同了。我们创建的是一个`div`，并且给`document.createElement`的第二个参数传入了`{is: 'custom-ele'}`。其实和上面 html 标签使用形式一样

```js
const customEle = document.createElement('div', {is: 'custom-ele'});
customEle.setAttribute('img', 'https://img13.360buyimg.com/n1/jfs/t1/174969/5/11254/137611/60ac9df2E132ebe19/fb0626e40dd46917.jpg');
customEle.setAttribute('text', '我是一段说明');
document.body.appendChild(customEle);
```

3. 在 js 中通过`new CustomEle()`实例来使用：

和上面的方式完全一样

运行效果：

![](https://img11.360buyimg.com/imagetools/jfs/t1/208610/39/4383/217168/61613907E29a20d94/927c2160bddc2615.jpg)

## 组件生命周期方法

### 1. `constructor`

自定义组件的第一个生命周期，用来初始化自定义组件本身。触发的时机在自定义组件被document.createElement的时候(前提是组件已经被customElements.define过，如果组件是先create，后defined，那么constructor的执行时机在append到主文档里时)

```js
class CustomEle extends HTMLDivElement {
  constructor() {
    super()
    console.log('constructor被执行');
  }
}
customElements.define('custom-ele', CustomEle, { extends: 'div' })
```

```
constructor被执行
```

### 2. `connectedCallback`

在组件被成功添加到主文档时触发的生命周期，在constructor之后

```js
class CustomEle extends HTMLDivElement {
  constructor() {
    super()
    console.log('constructor被执行');
  }

  connectedCallback() {
    console.log('connectedCallback被执行')
  }
}
customElements.define('custom-ele', CustomEle, { extends: 'div' })
```

```
constructor被执行
connectedCallback被执行
```

### 3. `attributeChangedCallback`

* 当组件的属性被增加、修改、删除的时候，会被触发
* 如果组件被 append 到之前设置了属性，则会在`connectedCallback`之前触发。反之，则在其后触发
* 必须要配合静态方法`static get observedAttributes`一起使用

```html
<div is="custom-ele" img="https://img13.360buyimg.com/n1/jfs/t1/174969/5/11254/137611/60ac9df2E132ebe19/fb0626e40dd46917.jpg" text="我是一段说明">
</div>
```
```js
class CustomEle extends HTMLDivElement {
  constructor() {
    super();
    console.log("constructor被执行");
  }

  connectedCallback() {
    const shadow = this.attachShadow({ mode: "open" });
    const div = document.createElement("div");
    const style = document.createElement("style");

    console.log("connectedCallback被执行");

    div.innerHTML = `
      <img src="${this.attributes.img.nodeValue}" width="200" />
      <slot name="text"></slot>
    `;

    style.textContent = `
    `;
    shadow.appendChild(style);
    shadow.appendChild(div);
  }

  static get observedAttributes() {
    return ["img", "text"];
  }

  attributeChangedCallback() {
    console.log("attributeChangedCallback被执行");
  }
}
customElements.define("custom-ele", CustomEle, { extends: "div" });
```

```
constructor被执行
attributeChangedCallback被执行
connectedCallback被执行
```

在线示例：https://codepen.io/sjzcxc/pen/vYJoegZ?editors=1111

### 4. `adoptedCallback`

当元素被移动到新的文档时，被调用。即元素是另一个文档的元素，而`adoptedCallback`是新文档下的自定义组件的回调

```js
class CustomEle extends HTMLDivElement {
  adoptedCallback() {
    console.log('adoptedCallback被执行');
  }
}
customElements.define('custom-ele', CustomEle, { extends: 'div' })
```

```html
<iframe id="iframe></iframe>
<div id="ele" is="custom-ele"
  img="https://img13.360buyimg.com/n1/jfs/t1/174969/5/11254/137611/60ac9df2E132ebe19/fb0626e40dd46917.jpg"
  text="我是一段说明">
  <h3 slot="text">我是一段说明</h3>
</div>
```

```js
setTimeout(() => {
  const ele = document.querySelector('#ele')
  const iframe = document.querySelector('#iframe')

  iframe.contentWindow.document.body.appendChild(ele)
}, 2000)
```


### 5. `disconnectedCallback`

组件的最后一个生命周期，当组件从文档中被移除时触发

```js
class CustomEle extends HTMLDivElement {
  attributeChangedCallback() {
    console.log('attributeChangedCallback被执行')
  }
}
customElements.define('custom-ele', CustomEle, { extends: 'div' })
```

两秒之后，将组件从文档中移除

```js
setTimeout(() => {
  document.body.removeChild(document.querySelector('#ele'))
}, 2000)
```

![](https://img14.360buyimg.com/imagetools/jfs/t1/202807/8/10219/1111276/61601ffbE3f77f8df/676e7a94c35d8d94.gif)

## `element.attachShadow(opt)`

此方法的参数是一个对象，支持两个配置项：

### 1. `mode`

如果为`open`，表示外部可以通过`element.shadowRoot`获取 shadowDOM 节点，并且会返回 shadowDOM 对象

```js
const shadow = this.attachShadow({mode: 'open'})
```

```html
<div id="ele" is="custom-ele"
  img="https://img13.360buyimg.com/n1/jfs/t1/174969/5/11254/137611/60ac9df2E132ebe19/fb0626e40dd46917.jpg"
  text="我是一段说明">
  <h3 slot="text">我是一段说明</h3>
</div>
```

```js
const ele = document.querySelector('#ele')
console.log(ele.shadowRoot)
```

![](https://img14.360buyimg.com/imagetools/jfs/t1/171488/7/23870/126395/615562bdE0e6c153a/563c9651c992cad2.jpg)

如果为`closed`，则表示不允许外部获取 shadowDOM 节点，并返回 null

### 2. `delegatesFocus`

它的参数是一个 boolean 值。示是否减轻自定义元素的聚焦性能问题。当 shadow DOM 中不可聚焦的部分被点击时, 让第一个可聚焦的部分成为焦点, 并且 shadow host 将提供所有可用的`:focus`样式

```html
<div id=" ele" is="custom-ele" img="https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fcdn.duitang.com%2Fuploads%2Fitem%2F201409%2F06%2F20140906020558_h4VfY.png" text="我是一段说明">
  <h3 slot="text">我是一段说明</h3>
  </div>
```

```js
class CustomEle extends HTMLDivElement {
  constructor() {
    super()
  }

  connectedCallback() {
    const shadow = this.attachShadow({mode: 'open', delegatesFocus: true})
    const div = document.createElement('div')

    div.innerHTML = `
      <input type="text" />
      <slot name="text"></slot>
    `
    shadow.appendChild(div)
  }
}
customElements.define('custom-ele', CustomEle, { extends: 'div' })
```
在线示例：https://codepen.io/sjzcxc/pen/wvqVrJX

![](https://img12.360buyimg.com/imagetools/jfs/t1/211614/29/2875/108981/615563e2E04377996/c996510e4e2653e3.gif)

## 几个工具方法

### 1. `window.customElements.get`

用来获取自定义组件的构造函数

```js
const ele = customElements.get('custom-ele')
console.log(ele)
```

### 2. `window.customElements.upgrade`


```js
class CustomEle extends HTMLElement {
  constructor() {
    super()
  }

  connectedCallback() {
    const shadow = this.attachShadow({ mode: 'open' })
    const div = document.createElement('div')
    const style = document.createElement('style')

    div.innerHTML = `
      <img src="${this.attributes.img.nodeValue}" width="200" />
      <slot name="text"></slot>
    `

    style.textContent = `
    `
    shadow.appendChild(style)
    shadow.appendChild(div)
  }
}
```

```js
//先创建了自定义元素 
const customEle = document.createElement('custom-ele');
customEle.setAttribute('img', 'https://img13.360buyimg.com/n1/jfs/t1/174969/5/11254/137611/60ac9df2E132ebe19/fb0626e40dd46917.jpg');
customEle.setAttribute('text', '我是一段悬停说明');
//后声明自定义元素 
customElements.define('custom-ele', CustomEle);
//结果为false，null 
console.log(customEle instanceof CustomEle, customEle.shadowRoot);
//进行更新节点 
// customElements.upgrade(customEle);//或document.querySelector('#app').appendChild(customEle); 
document.body.appendChild(customEle)
// document.querySelector('#app').setAttribute('is', 'custom-ele')
//true，#document-fragment 
console.log(customEle instanceof CustomEle, customEle.shadowRoot);
```

* 经过测试，只能用在自主定制元素上。用在自定义元素上没有效果
* 如果使用`customElements.upgrade(customEle)`，则最后一句输出的是`true, null`，也就是无法获取到其对应的 shadowRoot
* 如果使用`document.body.appendChild(customEle)`，则才会输出`true`和它对应的 shadowRoot

### 3. `window.customElements.whenDefined`

该方法是用来检测并提供自定义组件被定义声明完毕的时机得，接受一个参数，即自定义元素的name，返回值是一个promise(只检测自定义组件是否被defined，不检测是否被挂载于主文档)。若提供的name无效，则触发promise的catch

```js
customElements.whenDefined('custom-ele').then(() => {
  console.log('定义完毕')
})
```

### 4. `element.shadowRoot.getRootNode(opt)`

此方法的参数是一个对象，目前只支持一个选项配置`composed`

```html
<div id="ele" is="custom-ele"
  img="https://img13.360buyimg.com/n1/jfs/t1/174969/5/11254/137611/60ac9df2E132ebe19/fb0626e40dd46917.jpg"
  text="我是一段说明">
  <h3 slot="text">我是一段说明</h3>
</div>
```

```js
const ele = document.querySelector('#ele')
console.log(ele.shadowRoot.getRootNode({ composed: true }))
```

当`composed: true`时，会检索到根元素为 document 时为止：

![](https://img10.360buyimg.com/imagetools/jfs/t1/200035/1/10734/40347/61557094E28ffcbc7/9e9b6110c1e14465.jpg)

当`composed: false`时，如果 ele 是一个 shadowDOM 元素，则检索到 shadowDOM 的根元素为止。否则，还是检索到 document 为止

![](https://img10.360buyimg.com/imagetools/jfs/t1/114113/16/21047/51132/61557126E80d0e2ec/2d347be94c7c62a7.jpg)

### 5. `element.assignedSlot`

用来获取自定义元素中对应的 slot 元素。但如果`ele.attachShadow`中的`mode`是`closed`时，返回null

```html
<div id="ele" is="custom-ele"
  img="https://img13.360buyimg.com/n1/jfs/t1/174969/5/11254/137611/60ac9df2E132ebe19/fb0626e40dd46917.jpg"
  text="我是一段说明">
  <h3 slot="text">我是一段说明</h3>
</div>
```

```js
const h3 = document.querySelector('#ele h3')
console.log(h3.assignedSlot)
```

![](https://img14.360buyimg.com/imagetools/jfs/t1/198006/37/10844/26017/615572faE94511176/a3ea74920be3d6a9.jpg)

### 6. `element.slot`

用来获取自定义元素上 slot 的 name 值

```js
const h3 = document.querySelector('#ele h3')
console.log(h3.slot)
```

```
text
```

### 7. `slotchange`

当 slot 被插入或者删除的时候会触发此事件

```js
class CustomEle extends HTMLDivElement {
  constructor() {
    super()
    console.log('constructor被执行');
  }

  connectedCallback() {
    const shadow = this.attachShadow({ mode: 'open' })
    const div = document.createElement('div')
    const style = document.createElement('style')

    console.log('connectedCallback被执行')

    div.innerHTML = `
      <img src="${this.attributes.img.nodeValue}" width="200" />
      <slot name="text"></slot>
    `

    style.textContent = `
    `
    shadow.appendChild(style)
    shadow.appendChild(div)

    const slot = shadow.querySelector('slot')
    slot.addEventListener('slotchange', event => {
      console.log(event)
    }, false)
    console.log(slot)
  }
}
customElements.define('custom-ele', CustomEle, { extends: 'div' })
```

![](https://img13.360buyimg.com/imagetools/jfs/t1/197488/19/12221/122719/6161328dEfbf4ff01/42e7b1509692c8cc.jpg)

## 支持的 css 伪类

### 1. `:host`

只能用于 shadowDOM 内，表示自定义元素实例本身

* `:host`和`:host(*)`的意义一样的
* `:host(div)`表示只有自定义元素的根节点是`div`的时候，相应的 css 样式才会起作用

```js
class CustomEle extends HTMLDivElement {
  constructor() {
    super()
  }

  connectedCallback() {
    const shadow = this.attachShadow({mode: 'open'})
    const div = document.createElement('div')
    const style = document.createElement('style')

    div.innerHTML = `
      <img src="${this.attributes.img.nodeValue}" width="200" />
      <slot name="text"></slot>
    `

    style.textContent = `
      :host{
        background: skyblue;
      }
    `
    shadow.appendChild(style)
    shadow.appendChild(div)
  }
}

customElements.define('custom-ele', CustomEle, { extends: 'div' })
```

![](https://img11.360buyimg.com/imagetools/jfs/t1/109113/10/19366/184033/61555396Ea8d0e506/daa0cba7cde9aa46.jpg)

### 2. `:host-context([选择器])`

* 只能用在 shadowDOM 的样式中使用。表示自定义元素的父元素必须符合选择器括号中的名称，才会起作用
* 如果选择器为`*`，则表示其父元素可以为任意元素

下面示例中`:host-context(span)`中的样式只有在自定义元素的父元素为`span`元素时，才会起作用

```js
class CustomEle extends HTMLDivElement {
  constructor() {
    super()
  }

  connectedCallback() {
    const shadow = this.attachShadow({mode: 'open'})
    const div = document.createElement('div')
    const style = document.createElement('style')

    div.innerHTML = `
      <img src="${this.attributes.img.nodeValue}" width="200" />
      <h3>${this.attributes.text.nodeValue}</h3>
    `

    style.textContent = `
      :host-context(span){
        background: skyblue;
      }
    `
    shadow.appendChild(style)
    shadow.appendChild(div)
  }
}

customElements.define('custom-ele', CustomEle, { extends: 'div' })
```

```html
<span>
  <div is="custom-ele"
    img="https://img13.360buyimg.com/n1/jfs/t1/174969/5/11254/137611/60ac9df2E132ebe19/fb0626e40dd46917.jpg"
    text="我是一段说明">
  </div>
</span>
```

### 3. `:defined`

这个选择器得分两种情况来说，它既可以用在 shadowDOM 元素内部，也可以用在外部

当用在 shadowDOM 内部时，它选中的是除自定义根元素之外，其内部所有的元素

```js
class CustomEle extends HTMLDivElement {
  constructor() {
    super()
  }

  connectedCallback() {
    const shadow = this.attachShadow({mode: 'open'})
    const div = document.createElement('div')
    const style = document.createElement('style')

    div.innerHTML = `
      <img src="${this.attributes.img.nodeValue}" width="200" />
      <slot name="text"></slot>
    `

    style.textContent = `
      :defined{
        background: skyblue;
      }
    `
    shadow.appendChild(style)
    shadow.appendChild(div)
  }
}
customElements.define('custom-ele', CustomEle, { extends: 'div' })
```

![](https://img12.360buyimg.com/imagetools/jfs/t1/209146/7/2890/1738036/61555c43E941bec64/c3c07ce6c1d90c0a.gif)

当用在 shadowDOM 外部，放在一个普通的`style`标签中时。它选中的是自定义元素的根节点，以及不在 shadowDOM 内部的所有元素节点

```html
<style>
:defined{
  background: skyblue;
}
</style>
```

![](https://img11.360buyimg.com/imagetools/jfs/t1/208503/40/2895/3680659/61555eccEb9601634/37be509a281433e9.gif)

### 4. `::slotted([选择器])`

前面必须要加两个冒号

```js
class CustomEle extends HTMLDivElement {
  constructor() {
    super()
  }

  connectedCallback() {
    const shadow = this.attachShadow({mode: 'open'})
    const div = document.createElement('div')
    const style = document.createElement('style')

    div.innerHTML = `
      <img src="${this.attributes.img.nodeValue}" width="200" />
      <slot name="text"></slot>
    `

    style.textContent = `
      ::slotted(h3){
        background: skyblue;
      }
    `
    shadow.appendChild(style)
    shadow.appendChild(div)
  }
}

customElements.define('custom-ele', CustomEle, { extends: 'div' })
```

```html
<div is="custom-ele"
  img="https://img13.360buyimg.com/n1/jfs/t1/174969/5/11254/137611/60ac9df2E132ebe19/fb0626e40dd46917.jpg"
  text="我是一段说明">
  <h3 slot="text">我是一段说明</h3>
</div>
```

![](https://img10.360buyimg.com/imagetools/jfs/t1/202656/22/8929/212063/615453c4E04896c69/5146f61915043ebc.jpg)

### 5. `::part([选择器])`

`part`属性可以使用空格分隔添加多个属性名称。只有在`shadow-dom`中使用`part`属性时，才能使用`::part`伪类选择器进行选择

```html
<template id="box-element">
  <style type="text/css">
    *,
    ::before,
    ::after {
      box-sizing: border-box;
      padding: 1rem;
    }

    :host {
      display: flex;
    }
  </style>
  <div part="box">test</div>
</template>

<box-element></box-element>

<script>
  let template = document.querySelector("#box-element");
  globalThis.customElements.define(template.id, class extends HTMLElement {
    constructor() {
      super();
      this.attachShadow({ mode: "open" });
      this.shadowRoot.appendChild(template.content);
    }
  });
</script>

<style>
  box-element::part(box) {
    color: #0c0dcc;
  }
</style>
```

## 参数资料

* https://mp.weixin.qq.com/s/VzbKRXfrrFWgWqhHfEDxDw
