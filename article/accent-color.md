`accent-color`可以用来设置一些用户交互控件的主题颜色。目前浏览器只支持对以下几种控件进行颜色自定义：

```html
<input type="checkbox">
<input type="radio">
<input type="range">
<progress>
```

在线 demo：https://developer.mozilla.org/en-US/docs/Web/CSS/accent-color

而且你会发现，它内部元素的颜色会自动随着你外部颜色的变化，变成与之对比度较高的一个颜色。其实它的模式正好就是[css 混合模式效果](https://developer.mozilla.org/zh-CN/docs/Web/CSS/mix-blend-mode)
