```html
<div>test</div>
```

```css
div {
  background-color: yellow;
  width: 100px;
  height: 100px;
}

div {
  background: skyblue;
}
```

### 使用`@layer`降低 css 的层级

从这段代码，一眼就能看出来 div 的背景色一定是`skyblue`。如果把下面的 css 代码使用`@layer`包裹起来，就会把这段样式的层级降低，因此 div 的背景色就成了`yellow`

```css
div {
  background-color: yellow;
  width: 100px;
  height: 100px;
}

@layer {
  div {
    background: skyblue;
  }
}
```

### 给`@layer`添加名字，手动指定其层级

还可以给`@layer`起一个名字，还是这段 css 样式：

```css
@layer l1 {
  div {
    background-color: yellow;
    width: 100px;
    height: 100px;
  }
}

@layer l2 {
  div {
    background-color: skyblue;
  }
}
```

只是加个名字，并不会改变 css 的默认渲染优先级。这个 div 最终渲染还是`skyblue`。如果想让`l1`的优先级比`l2`高，需要再加一句：

```css
@layer l2, l1;
```

### `@layer`嵌套

这段代码渲染完，div 的背景色一定是`yellow`。因为`l2`被嵌套在`l1`中，它的层级就会比`l1`要低。也就是说：被嵌套的越深，层级越低

```css
@layer l1 {
  div {
    background-color: yellow;
    width: 100px;
    height: 100px;
  }

  @layer l2 {
    div {
      background-color: skyblue;
    }
  }
}
```

### 在`@import`中使用`@layer`

在`@import`的时候，可以使用`@layer`来指定优先级，目前还没有浏览器支持

```css
@import 'theme.css' @layer(theme);
```

### 在`link`标签上使用`layer`属性

目前还没有浏览器支持

```html
<link rel="stylesheet" href="./style.css" layer="base" />
```
