```js
history.scrollRestoration
```

用来控制在刷新页面之后，浏览器的滚动条是否依然定位在之前的位置。它有两个值：

* `auto`：默认行为，刷新之后，依然定位到之前滚动条的位置
* `manual`，设置为此值之后，每次刷新页面之后，滚动条都会返回到顶部或者左部。也就是`scrollTop=0`或者`scrollLeft=0`

如何使用：

```js
history.scrollRestoration = "manual"
```

参考链接：https://developer.mozilla.org/en-US/docs/Web/API/History/scrollRestoration
