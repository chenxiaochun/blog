css grid，可以简称为 grid。是一种完全和以前所有其它的，都截然不同的 web 布局方式，可以说它完全颠覆了我们设计界面时的方式

对于界面布局，我们曾经都使用过`table`、`float`、`position`定位以及`inline-block`，这些特性用在布局上，其实本质上都是一种 hack，并且它们也不具备我们常用的一些功能，
比如：垂直居中。这时候，你可能想到了`flexbox`，它确实也是很重要的一种布局工具，但它其实更倾向于控制元素的[对齐方向](https://css-tricks.com/quick-whats-the-difference-between-flexbox-and-grid/)

## 浏览器兼容性

截止目前2022年4月份，基本上所有主流浏览器都支持 css grid

## 重要术语解释

因为 css grid 引入了很多新术语和概念，所以需要提前明确一下，以免后面搞不清楚

### grid container

一个添加了`display: grid`的元素就被称为 grid container，例如下面`class="container"`的元素

```html
<div class="container">
  <div class="item item-1"> </div>
  <div class="item item-2"> </div>
  <div class="item item-3"> </div>
</div>
```
### grid item

<img src="https://css-tricks.com/wp-content/uploads/2018/11/terms-grid-cell.svg" width="300">

一个 grid container 下面的所有直接子元素都被称为 grid item。在下面示例中指的就是所有`class="item"`的元素，但是不包括`class="sub-item"`元素。因为它并不是 grid container 的直接子元素

```html
<div class="container">
  <div class="item"></div>
  <div class="item">
    <p class="sub-item"></p>
  </div>
  <div class="item"></div>
</div>
```

### grid line

就是指的 grid item 之间水平或者垂直方向的线，例如下面示例中的黄线

<img src="https://css-tricks.com/wp-content/uploads/2018/11/terms-grid-line.svg" width="300">


### grid track

<img src="https://css-tricks.com/wp-content/uploads/2021/08/terms-grid-track.svg" width="300">

### grid area

<img src="https://css-tricks.com/wp-content/uploads/2018/11/terms-grid-area.svg" width="300">

## `grid`属性集合

### `display`

* `grid`：生成一个块级元素的 grid container
* `inline-grid`：生成一个内联级元素的 grid container

```css
.container {
  display: grid | inline-grid;
}
```

一个元素变成 grid container 之后，在用 chrome 开发者工具查看它时，会发现旁边多了一个 grid 开关标识：

<img src="https://img13.360buyimg.com/imagetools/jfs/t1/98296/2/29772/4163/62c64819Edade5d5c/3d28134a8d2fea3e.png" width="400" />

打开此开关，再点击右侧的`layout`标签页，就可以查看更多关于 grid 布局的信息，这个后面会再详细介绍，先知道有这样一个功能就行：

<img src="https://img11.360buyimg.com/imagetools/jfs/t1/78723/7/20056/72460/62c6497dE5761f263/3df5529107f082b2.png" width="600">

### `grid-template-columns`和`grid-template-rows`

这两个属性用于将 grid container 划分成若干格子，每个格子被称为 grid item。例如将一个宽度和高度都等于`500px`的 grid container 都划分成`4*4`的格子

```css
.container {
  background: #eee;
  display: grid;
  width: 500px;
  height: 500px;
  grid-template-columns: 1fr 1fr 1fr 1fr;
  grid-template-rows: 1fr 1fr 1fr 1fr;
}
```
点击打开 grid container 上的 grid 开关，就会看到虚线划分的 grid item：

<img src="https://img10.360buyimg.com/imagetools/jfs/t1/92653/6/29512/10407/62c64e20E5afe9bf3/dfe283f32774fc35.png" width="400">

在这里，我们的 grid item 尺寸单位用的`fr`（也可以换成长度、百分比等其它单位），`fr`是在 grid 布局中添加的一个新单位，用于表示 grid container 的剩余空间大小。在此示例中因为将空间分成了 4 等份，所以`1fr`就等于 grid container 的四分之一大小。这个在后面也会详细讲

虚线被称为 grid line，它们默认都有自己的整数编号。如果是从开始列（行）到结束列（行），则是从正 1 开始计数。反之，则是从 -1 开始计数。在 chrome 开发者工具，打开`Layout`标签，然后选择`show line numbers`就能看到编号了：

<img src="https://img13.360buyimg.com/imagetools/jfs/t1/197842/40/24838/21820/62c65279E06cbee38/c04cfe44556fc20e.png" width="300">

<img src="https://img14.360buyimg.com/imagetools/jfs/t1/11540/15/17844/13940/62c651e6E2538492d/d0a6d6bbc16e12ee.png" width="500">

grid line 除了有编号，还可以给它指定一个自定义名称。添加名称的语法就是用中括号括起来：

```css
.container {
  background: #eee;
  display: grid;
  width: 500px;
  height: 500px;
  grid-template-rows: [row1] 1fr [row2] 1fr [row3] 1fr [row4] 1fr;
  grid-template-columns: [col1] 1fr [col2] 1fr [col3] 1fr [col3] 1fr;
}
```

打开开发者工具的`Layout`标签，选择`show line names`，就可以看到 grid line 的名称了：

<img src="https://img13.360buyimg.com/imagetools/jfs/t1/95140/7/29744/17579/62c67eb3E3660b5e5/47ab656150ed0e7d.png" width="500">

每条 grid line 还可以同时拥有多个名称。比如 row2 还可以同时叫做 row1-end：

```css
grid-template-rows: [row1] 1fr [row2 row1-end] 1fr [row3] 1fr [row4] 1fr;
```

<img src="https://img12.360buyimg.com/imagetools/jfs/t1/148627/32/28007/17509/62c67e62E7d971c7e/c5ea2b8b3b63f47e.png" width="500" />

如果相邻多个的 grid line 和 grid item 的名称尺寸都是相同的，则可以直接简写为：

```css
.container {
  grid-template-rows: repeat(4, 1fr [row]);
}
```

注意语法为`repeat(4, 1fr [row])`，而不是`repeat(4, [row] 1fr)`，后者是无效的

上面一直说`fr`指的是 grid container 的**剩余空间**。其实剩余空间指的就是 grid container 减去非弹性元素空间之后的空间

来看下面示例，将 grid container 分成了四列：

```css
.container {
  grid-template-columns: 1fr 50px 1fr 1fr;
}
```

其中第二列的列宽度为固定的`50px`。因此，`1fr`的计算公式为：

```
1fr = (grid 容器尺寸 - 50px) / 3
```

---

---

### `grid-row-start`、`grid-column-start`、`grid-row-end`、`grid-column-end`

这几个选择器都只能用于 grid item。用来定义 grid item 在一个 grid container 中的行列起止位置


```html
<div class="container">
  <div class="item-a">One</div>
</div>
```

```css
.container {
  background: #eee;
  display: grid;
  width: 500px;
  height: 500px;
  grid-template-columns: repeat(4, 1fr);
}

.container div {
  border: 5px solid skyblue;
}

.item-a {
  grid-row-start: 1;
  grid-column-start: 2;
  grid-row-end: 3;
  grid-column-end: 3;
}
```

在上面示例中，将 grid container 设置为了四列，每列的宽度为`1fr`。然后指定`item-a`的行列起止位置为`1/2/3/3`，也就是从第一行、第二列开始，至第三列、第三行结束。那它的渲染结果就是：

<img src="https://img12.360buyimg.com/imagetools/jfs/t1/129835/2/29402/11596/62c4f9f2E98fdf4d4/20467c17f8b1ac93.png" width="400">

其实给`item-a`设置的四条选择器可以简化为一条`grid-area: 1/2/2/3`，效果是完全相同的

### `grid-area`和`grid-template-areas`

## 相关资源

* https://css-tricks.com/snippets/css/complete-guide-grid/
* https://cssgrid-generator.netlify.app/
* https://grid.layoutit.com/

