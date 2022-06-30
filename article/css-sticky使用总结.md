Antd 的 [Table](https://ant.design/components/table-cn/#header) 组件添加了一个`sticky`功能，可以使表头在浏览器滚动的时候，固定在当前窗口的顶部

于是，我就想愉快的应用起来。但是，很快就遇到了两个问题：

### 1. 如果`sticky`元素的祖先元素带有非`overflow: visible`的 css 属性，则`sticky`不会起作用

```css
html,
body {
  padding: 0;
  margin: 0;
}

.box1 {
  background: skyblue;
  height: 100vh;
  padding: 1em;
  overflow: auto;
}

.box2{
  height: 2000px;
  overflow: auto;
}

.box {
  height: 99px;
  background: #ccc;
  position: sticky;
  top: 0;
}
```

```html
  <div class="box1">
    <div style="height: 100px; background: blue;"></div>
    <div class="box2">
      <div class="box">test</div>
    </div>
    <div style="height: 100px; background: blue;"></div>
  </div>
```

### 2. `sticky`元素顶部为什么会有空隙

```css
html,
body {
  padding: 0;
  margin: 0;
}

.box1 {
  background: skyblue;
  height: 100vh;
  padding: 1em;
  overflow: auto;
}

.box {
  height: 99px;
  background: #ccc;
  position: sticky;
  top: 0;
}
```

```html
<div class="box1">
  <div style="height: 100px; background: blue;"></div>
  <div class="box2" style="height: 2000px">
    <div class="box">test</div>
  </div>
  <div style="height: 100px; background: blue;"></div>
</div>
```

最外层的`box1`高度为`100vh`。所以，页面中实际上使用的是`box1`元素的滚动条，而且在`box1`上还添加了`padding`和`overflow: auto`。这就会使得`sticky`元素在滚动的时候，即使设置了`top: 0;`，也不会紧靠视窗顶部，而是会有一个`1em`的空隙

解决办法：

1. 移除`box1`元素上的`overflow: auto`属性
2. 移除`box1`元素上的`padding`属性

### 3. 可以实现的一个效果

```css
html,
body {
  padding: 0;
  margin: 0;
}

.box {
  background: #ccc;
  height: 500px;
}

.box nav{
  position: sticky;
  top: 0;
  background: skyblue;
  padding: 0.5em 1em;
}
```

```html
  <div class="box">
    <nav>menu1</nav>
  </div>
  <div class="box">
    <nav>menu2</nav>
  </div>
  <div class="box">
    <nav>menu3</nav>
  </div>
  <div class="box">
    <nav>menu4</nav>
  </div>
```

### 4、`sticky`会相对于离它最近一个拥有『滚动机制』（当该祖先的`overflow`是`hidden`、`scroll`、`auto` 或`overlay`时）的祖先元素上

```html
<div style="height: 50px; background: skyblue;"></div>
<div class="box">
  <header></header>
  <div class="content">content</div>
</div>
```

```css
html,
body {
  padding: 0;
  margin: 0;
  overflow: hidden;
}

.box {
  border: 1px solid blue;
  height: calc(100vh - 50px);
  width: 1000px;
  margin: 0 auto;
  overflow: scroll;
}

.box header {
  height: 50px;
  background: #ccc;
  position: sticky;
  top: 0;
  margin-top: 100px;
}

.content {
  height: 2000px;
}
```
