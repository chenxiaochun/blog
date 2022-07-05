css grid，可以简称为 grid。是一种完全和以前所有其它的，都截然不同的 web 布局方式，可以说它完全颠覆了我们设计界面时的方式

对于界面布局，我们曾经都使用过`table`、`float`、`position`定位以及`inline-block`，这些特性用在布局上，其实本质上都是一种 hack，并且它们也不具备我们常用的一些功能，
比如：垂直居中。这时候，你可能想到了`flexbox`，它确实也是很重要的一种布局工具，但它其实更倾向于控制元素的[对齐方向](https://css-tricks.com/quick-whats-the-difference-between-flexbox-and-grid/)

## 浏览器兼容性

截止目前2022年4月份，基本上所有主流浏览器都支持 css grid

## 重要术语解释

因为 css grid 引入了很多术语在概念上差不多，所以需要提前明确一下，以免后面搞糊涂

### `grid`容器

一个被添加了`display: grid`的元素就被称为`grid`容器，例如下面的`container`元素

```html
<div class="container">
  <div class="item item-1"> </div>
  <div class="item item-2"> </div>
  <div class="item item-3"> </div>
</div>
```

### `grid`项

一个`grid`容器下面的所有直接子元素都被称为`grid`项。在这里就是所有`class="item"`的元素，但是不包括`class="sub-item"`元素。因为它并不是 container 的直接子元素

```html
<div class="container">
  <div class="item"></div>
  <div class="item">
    <p class="sub-item"></p>
  </div>
  <div class="item"></div>
</div>
```

### `grid`分隔线

就是指的 grid 项之间水平或者垂直方向的线，例如下面示例中的黄线

<img src="https://css-tricks.com/wp-content/uploads/2018/11/terms-grid-line.svg" width="300">

### `grid`单元格

<img src="https://css-tricks.com/wp-content/uploads/2018/11/terms-grid-cell.svg" width="300">

### `grid`轨道

<img src="https://css-tricks.com/wp-content/uploads/2021/08/terms-grid-track.svg" width="300">

### `grid`区域

<img src="https://css-tricks.com/wp-content/uploads/2018/11/terms-grid-area.svg" width="300">

## `grid`属性集合

可用于`grid`容器的属性集合：

### `display`

* `grid`：生成一个 grid 块级容器
* `inline-grid`：生成一个 grid 内联级容器

```css
.container {
  display: grid | inline-grid;
}
```

### `grid-template-columns`和`grid-template-rows`

1. 它们都可以指定一个以空格分隔的列表，用于定义行和列的尺寸。而空格就会成为 grid 项之间的分隔线

* `<track-size>`：用来指定`grid`轨道尺寸。可以是一个长度、百分比或者是 grid 容器中剩余空间的一部分（单位使用[`fr`](https://css-tricks.com/introduction-fr-css-unit/)）
* `<line-name>`：用来任意指定你要选择的一个`grid`分隔线的名称

`grid`分隔线会自动被分配一个整数序列号。如果是从开始列（行）到结束列（行），则是从正 1 开始计数。反之，则是从 -1 开始计数：

<img src="https://css-tricks.com/wp-content/uploads/2018/11/template-columns-rows-01.svg" width="400" />

2. 可以给分隔线指定一个明确的名称：

```css
.container {
  grid-template-columns: [first] 40px [line2] 50px [line3] auto [col4-start] 50px [five] 40px [end];
  grid-template-rows: [row1-start] 25% [row1-end] 100px [third-line] auto [last-line];
}
```

一条分隔线还可以有多个名称，比如第二条水平线既可以被称为`row1-end`，也可以被称为`row2-start`。分隔线的名称可以随意定义，可以叫`abc`或者其它什么，这里只是为了名称有意义：

```css
.container {
  grid-template-rows: [row1-start] 25% [row1-end row2-start] 25% [row2-end];
}
```

而且 chrome dev tools 也提供了查看分隔线名称和序号的功能：

<img src="https://img10.360buyimg.com/imagetools/jfs/t1/208670/1/23588/81256/62c3db0cEac6fd336/b0ad5089e166d762.jpg" width="500">

<img src="https://img10.360buyimg.com/imagetools/jfs/t1/26191/32/17300/13127/62c3dbabEd5901980/dfa4b04bd847c741.jpg" width="500">

<img src="https://img11.360buyimg.com/imagetools/jfs/t1/213382/30/19651/57785/62c3dc2fE089c626c/d418213fb32a84d3.jpg" width="500">


3. 如果多个相邻多个`grid`项的定义是相同的，可以使用`repeat()`函数进行简写：

```css
.container {
  grid-template-columns: 20px [col-start] 20px [col-start] 20px [col-start];
}
```

可以简写为：

```css
.container {
  grid-template-columns: repeat(3, 20px [col-start]);
}
```

4. `fr`用来设置`grid`项在`grid`容器中剩余空间的尺寸：

例如，下面示例就是将每个`grid`项尺寸设置为`grid`容器剩余空间的**三分之一**大小

```css
.container {
  grid-template-columns: 1fr 1fr 1fr;
}
```

一直说是**剩余空间**，是因为计算`fr`时，所使用的空间尺寸不包含那些非弹性元素。比如，下面示例中的`1fr`就是：

```
1fr = (grid 容器尺寸 - 50px) / 3
```

```css
.container {
  grid-template-columns: 1fr 50px 1fr 1fr;
}
```



