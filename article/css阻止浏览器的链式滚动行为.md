当我们在浏览器中滚动一个内嵌元素，并且滚动到底部时。外部容器的滚动条就会接着继续滚动，这种行为被称为链式滚动

这种行为，有时候让我们觉得很恼火。现在可以在 body 元素上添加`overscroll-behavior: contain`来禁用此行为

```css
body {
  overscroll-behavior: contain;
}
```

目前 Chrome、Firefox 都支持此特性，在 Adge 中必须添加前缀`-ms-scroll-chaining`。详情可查看[caniuse](https://caniuse.com/?search=overscroll-behavior)

在线 demo：https://output.jsbin.com/sodewaw/quiet

参考链接：https://webplatform.news/issues/2019-02-11
