`accent-color`可以非常方便的用来设置一些表单控件的外观颜色，目前仅支持以下几种表单控件：

```html
<input type="checkbox">
<input type="radio">
<input type="range">
<progress>
```

在线 demo：[https://developer.mozilla.org/en-US/docs/Web/CSS/accent-color](https://codepen.io/geoffgraham/pen/wvewwXL)

而且你会发现，它内部元素的颜色会自动随着你外部颜色的变化，变成与之对比度较高的一个颜色。其实它的模式正好就是[css 混合模式效果](https://developer.mozilla.org/zh-CN/docs/Web/CSS/mix-blend-mode)
