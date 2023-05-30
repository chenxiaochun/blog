## `::backdrop`

通常用来表示[最高层级元素](https://developer.mozilla.org/en-US/docs/Glossary/Top_layer)之下的那个元素，尺寸和当前的视口大小相同

在浏览器中以下几种元素在使用时，会默认为最高层级元素：

1. 使用 [Full Screen Api](https://developer.mozilla.org/en-US/docs/Web/API/Fullscreen_API)进入全屏状态的元素
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


## `::marker`


## `::selection`


## `::file-selector-button`


## `::placeholder`


## `::before`


## `::after`


## 相关文章

* https://mp.weixin.qq.com/s/lx9NwTERrL022_TDghmNig