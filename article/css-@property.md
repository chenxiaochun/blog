`@property`是 CSS Houdini api 的一部分，它允许开发者不仅可以定义 css 变量的名称，还可以定义变量的类型、是否可以被继续以及默认值等

`@property`支持的类型定义：

```
length，指的是 css 绝对长度单位，例如：`px`、`pt`等
number
percentage
length-percentage
color
image
url
integer
angle
time
resolution
transform-list
transform-function
custom-ident (a custom identifier string)
```

定义好类型。这意味着浏览器可以提前知道如何去处理这个变量值，而不是只能假设将所有的值都当作字符串去处理

例如下面的示例中，我们定义了一个`--r`变量配合`@keyframes`。以期望实现`.box`能够旋转的动画，但实际上它并不能运行起来

因为浏览器并不知道`0deg`和`360deg`是一个合法的度数值

```css
.box {
  --r: 0deg;
  transform: rotate(var(--r));
  animation: spin 1s linear infinite;
  width: 100px;
  height: 100px;
  border: 1px solid skyblue;
}

@keyframes spin {
  100% {
    --r: 360deg;
  }
}
```

那如果换成使用`@property`来定义就能完美的运行起来了：

```css
@property --r {
  syntax: '<angle>';
  initial-value: 0deg;
  inherits: false;
}

.box {
  --r: 0deg; /* 此处也可以省略 */
  transform: rotate(var(--r));
  animation: spin 1s linear infinite;
  width: 100px;
  height: 100px;
  border: 1px solid skyblue;
}

@keyframes spin {
  100% {
    --r: 360deg;
  }
}
```

### 相关文章

* https://css-tricks.com/a-complete-guide-to-custom-properties/#top-of-site
* https://mp.weixin.qq.com/s/bpI9EycubFRc-7zMHRxCHA
* https://segmentfault.com/a/1190000039826626
