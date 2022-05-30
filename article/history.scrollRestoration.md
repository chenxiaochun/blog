```js
history.scrollRestoration
```

用来控制在刷新页面之后，浏览器的滚动条是否依然定位在之前的位置。它有两个值：

* `auto`：默认行为，刷新之后，依然定位到之前滚动条的位置
* `manual`，设置为此值之后，每次刷新页面之后，滚动条都会返回到顶部或者左部。也就是`scrollTop=0`或者`scrollLeft=0`

比如拿`http://jd.com`测试一下。打开页面，向下滚动一段距离。刷新页面，滚动条都会自动恢复到上次的位置

此时，如果在控制台中执行下面的代码：

```js
history.scrollRestoration = "manual"
```

再刷新浏览器，就会发现滚动条每次都会返回到初始位置了。如果关闭当前页面，再重新打开，就又被重置回默认状态`auto`了

参考链接：https://developer.mozilla.org/en-US/docs/Web/API/History/scrollRestoration
