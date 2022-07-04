css grid，可以简称为 grid。是一种完全和以前所有其它的，都截然不同的 web 布局方式，可以说它完全颠覆了我们设计界面时的方式

对于界面布局，我们曾经都使用过`table`、`float`、`position`定位以及`inline-block`，这些特性用在布局上，其实本质上都是一种 hack，并且它们也不具备我们常用的一些功能，
比如：垂直居中。这时候，你可能想到了`flexbox`，它确实也是很重要的一种布局工具，但它其实更倾向于控制元素的[对齐方向](https://css-tricks.com/quick-whats-the-difference-between-flexbox-and-grid/)

## 浏览器兼容性

截止目前2022年4月份，基本上所有主流浏览器都支持 css grid

## 重要术语解释

因为 css grid 引入了很多术语在概念上差不多，所以需要提前明确一下，以免后面搞糊涂

### `grid`容器

一个被添加了`display: grid`的元素就称为`grid`容器，它就是所有子元素的直接父元素

```html
<div class="container">
  <div class="item item-1"> </div>
  <div class="item item-2"> </div>
  <div class="item item-3"> </div>
</div>
```

### `grid`元素

一个`grid`容器下面的所有直接子元素都被称为`grid`元素。在这里就是所有`class="item"`的元素，但是不包括`class="sub-item"`的元素

```html
<div class="container">
  <div class="item"> </div>
  <div class="item">
    <p class="sub-item"> </p>
  </div>
  <div class="item"> </div>
</div>
```

### `grid`线

就是指的 grid 元素之间水平或者垂直方向的线，例如下面示例中的黄线

<img src="https://css-tricks.com/wp-content/uploads/2018/11/terms-grid-line.svg" width="300">

### `grid`单元格

<img src="https://css-tricks.com/wp-content/uploads/2018/11/terms-grid-cell.svg" width="300">

### `grid`轨道

<img src="https://css-tricks.com/wp-content/uploads/2021/08/terms-grid-track.svg" width="300">

### `grid`区域

<img src="https://css-tricks.com/wp-content/uploads/2018/11/terms-grid-area.svg" width="300">

## `grid`属性集合