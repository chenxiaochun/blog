## `::backdrop`

这个伪类用来表示[最高层级元素](https://developer.mozilla.org/en-US/docs/Glossary/Top_layer)之下的那个元素，尺寸和当前的视口大小相同

在浏览器中以下几种元素在使用时，会默认为最高层级元素：

1. 使用 [Full Screen Api](https://developer.mozilla.org/en-US/docs/Web/API/Fullscreen_API) 进入全屏状态的元素
2. 使用 [showDialog](https://developer.mozilla.org/en-US/docs/Web/API/HTMLDialogElement/showModal) 方法展示的 dialog 元素
3. 使用 [showPopover](https://developer.mozilla.org/en-US/docs/Web/API/Popover_API) 方法展示的 popover 元素

每种元素都有它自己的`::backdrop`，例如：

```css
dialog::backdrop {
  background: rgba(255, 0, 0, 0.25);
}

video::backdrop {
  background-color: #448;
}
```

在线示例：https://mdn.github.io/css-examples/backdrop/index.html


## `::marker`

这个伪类用来定义『列表元素』的标记样式。一般情况下，标识样式通常为一个黑点儿或者数字

那何为列表元素？凡是设置了`display: list-item`的都为列表元素

```css
li::marker {
    content: '~';
    font-size: 1.2em;
}
```

并不是所有的 css 属性都可以用到`::marker`伪类上。只有以下几类属性会起作用：

1. 所有的设置字体相关属性
2. `white-space`属性
3. `color`属性
4. `text-combine-upright`、`unicode-bidi`、`direction`
5. `content`属性
6. `animation`和`transition`属性


## `::selection`


## `::file-selector-button`


## `::placeholder`


## `::before`


## `::after`


## 相关文章

* https://mp.weixin.qq.com/s/lx9NwTERrL022_TDghmNig