css grid，可以简称为 grid。是一种完全和以前所有其它的，都截然不同的 web 布局方式

对于界面布局，我们曾经都使用过`table`、`float`、`position`定位以及`inline-block`，这些特性用在布局上，其实本质上都是一种 hack，并且它们也不具备我们常用的一些功能

提到 css grid，你可能就会想到 [flex](https://css-tricks.com/quick-whats-the-difference-between-flexbox-and-grid/)。但我觉得前者更倾向于**布局**，后者更倾向于**对齐**

就像房屋的布局是一个不规则的图形，如果用上面提到的传统方式来实现，并且还能要根据整体面积自适应（对于我们来说，就是页面尺寸的自适应），将会是一件很麻烦的事情。而换成 css grid 来实现就会非常简单（为什么简单？看完下面的详细介绍就清楚了）

而一旦整体布局确定之后，房间里的家具到底是左对齐，还是右对齐，来使用 flex 实现就会很简单了

<img src="https://img14.360buyimg.com/imagetools/jfs/t1/177857/38/27131/43095/62ccdcacEf9821e4e/e2dcbeec23b5bb42.jpg" width="600" />

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

### `grid-row-start`、`grid-column-start`、`grid-row-end`、`grid-column-end`

这几个选择器都只能用于 grid item。用来定义 grid item 在一个 grid container 中的开始行、开始列、结束行和结束列的位置

看下面的示例，在 grid container 中有一个子元素。grid container 被划分为了四行四列。

```html
<div class="container">
  <div class="item-a">A</div>
</div>
```

```css
.container {
  background: #eee;
  display: grid;
  width: 500px;
  height: 500px;
  grid-template-rows: repeat(4, 1fr);
  grid-template-columns: repeat(4, 1fr);
}

.container div {
  border: 5px solid black;
}
```

因为只有一个子元素，默认情况下它会居于左上角的位置：

<img src="https://img10.360buyimg.com/imagetools/jfs/t1/195135/4/27338/13994/62c6da64Eb23051bf/84bc0ac41218e9d6.png" width="500" />

如果想让该子元素的位置挪到第二行，第二列的位置，就可以使用这四『兄弟』来指定一下：

```css
.item-a {
  grid-row-start: 2;
  grid-column-start: 2;
  grid-row-end: 3;
  grid-column-end: 3;
}
```

<img src="https://img12.360buyimg.com/imagetools/jfs/t1/83641/33/20415/11286/62c6db4eEb27f272a/ac291c4fcd5b6d8f.png" width="500">

其实，这四条 css 属性完全可以使用`grid-area`简写为一条，它们的作用是一样。只是顺序必须要遵循：开始行/开始列/结束行/结束列

```css
.item-a{
  grid-area: 2/2/3/3;
}
```

### `grid-area`和`grid-template-areas`

`grid-area`除了可以作为`grid-row-start`、`grid-column-start`、`grid-row-end`、`grid-column-end`四兄弟的简写之外。还可以用来给 grid item 指定一个名称（注意：这里说的是 grid item，并非 grid line）

那指定了名称有什么用呢？可以被`grid-template-areas`引用，用来定义 grid template 以实现更为复杂灵活的布局。来看一个示例：

在下面的 grid container 中有四个子元素，分别表示页面中常见的四个区域，以此来实现这样一个布局，并且完全是自适应的：

```html
<div class="container">
  <div class="item-a">Header</div>
  <div class="item-b">Main</div>
  <div class="item-c">Sidebar</div>
  <div class="item-d">Footer</div>
</div>
```

注意：Main 和 Sidebar 之间的区域是置空的，没有任何元素

<img src="https://img10.360buyimg.com/imagetools/jfs/t1/177675/38/27019/4106/62ccdeddE32435a93/05bf4d507e05df53.png" />

然后使用`grid-area`分别给四个子元素指定了四个名称，并且各指定了一种背景色方便在页面上查看：

```css
.item-a {
  grid-area: header;
  background: orange;
}

.item-b {
  grid-area: main;
  background: skyblue;
}

.item-c {
  grid-area: sidebar;
  background-color: purple;
}

.item-d {
  grid-area: footer;
  background: green;
}
```

将 grid container 划分为三行四列，再使用`grid-area`对四个子元素指定了名称。现在可以使用`grid-template-areas`引用这些名称，对它们进行布局了：

这里的关键就在于`grid-template-areas`，它用**换行分隔**了三行代码表示行，每一行中使用**空格分隔**表示列，因此：

* 第一行的 header 占据了四列，所以要写成：`header header header header`
* 第二行的前两列是 main，第三列是空元素，第四列是 sidebar，所以要写成：`main main . sidebar`
* 第三行四列都是 footer 占据，所以要写成：`footer footer footer footer`

看着就像矩阵，很形象对不对？通过看这几行代码，都能脑补出来实际的布局大概长什么样子

```css
.container {
  background: #eee;
  display: grid;
  width: 500px;
  height: 500px;
  grid-template-rows: repeat(3, 1fr);
  grid-template-columns: repeat(4, 1fr);
  grid-template-areas: 
    "header header header header"
    "main main . sidebar"
    "footer footer footer footer";
}
```

注意：在这个示例里，我们只是对 grid item 指定了名称，并没有对 grid line 指定名称。但是，grid line 依旧会被自动赋予相应的名称，因为它是根据 grid item 名称自动添加的

比如，这里第一行的 grid line 的名称是`header-start`，第一列会有多个名称`header-start main-start`，最后一列有三个名称`header-end sidebar-end footer-end`

<img src="https://img14.360buyimg.com/imagetools/jfs/t1/120040/30/24550/15068/62cce583E5f7ece2f/2947726feb967561.png" />

### `grid-template`

此属性可以作为`grid-template-rows`、`grid-template-columns`两者的简写形式。例如：

```css
.container{
  grid-template-rows: repeat(3, 1fr);
  grid-template-columns: repeat(4, 1fr);
}
```

可以简写成：

```css
.container{
  grid-template: repeat(3, 1fr) / repeat(4, 1fr);
}
```

它也可以作为`grid-template-areas`和`grid-template-rows`、`grid-template-columns`三兄弟的简写。其语法是：

```
grid-template: grid-template-areas grid-template-rows / grid-template-column
```

例如将上面的 container 布局进行简写：

```css
.container {
  background: #eee;
  display: grid;
  width: 500px;
  height: 500px;
  grid-template-rows: repeat(3, 1fr);
  grid-template-columns: repeat(4, 1fr);
  grid-template-areas: 
    "header header header header"
    "main main . sidebar"
    "footer footer footer footer";
}
```

可以简写为：

```css
.container{
  background: #eee;
  display: grid;
  width: 500px;
  height: 500px;
  grid-template:
    "header header header header" 1fr
    "main main . sidebar" 1fr
    "footer footer footer footer" 1fr
    / 1fr 1fr 1fr 1fr;
}
```

应该有人注意到了，斜杠后面的`1fr 1fr 1fr 1fr`是完全相同的，那是不是可以写成`repeat(4, 1fr)`呢？经过我的测试，答案是不可以的。不知道为啥不支持！

### `grid-column-gap`、`grid-row-gap`和`column-gap`、`row-gap`

这几个属性用来设置 grid line 尺寸，其实就是用来设置行和列之间的间距。其中带有`grid-`的前两者是旧的使用方式，现在标准的使用方式应该是后两者（我咋觉得前两者的命名更统一规范呢，也可能是标准组织在下一盘大棋，将来会把它们用在别的地方）

```css
.container{
  column-gap: 10px;
  row-gap: 20px;
}
```

<img src="https://css-tricks.com/wp-content/uploads/2018/11/dddgrid-gap.svg" width="400" />

## 相关资源

* https://css-tricks.com/snippets/css/complete-guide-grid/
* https://cssgrid-generator.netlify.app/
* https://grid.layoutit.com/

