## 相关文章

`@property`是 CSS Houdini api 的一部分，它允许开发者显式的自定义 css 属性，可以定义属性的值类型、是否可被继续、默认值等

使用 css 自定义属性值的好处：

1. 可以最大化的提高代码利用率，减少代码重复
2. 在某些场景尤其适用于 css 自定义属性，比如说，定义网站的主题颜色
3. 可以使用 js 更新操作 css 自定义属性，这就又带来了更多有意思的可能性

### 自定义属性的命名规范

自定义属性必须存在于一个 css 选择器中，并且必须以两个横杠（--）开头，否则无效

```css
/* 错误，必须定义在一个选择器中 */
--foo: 1;

body {
  /* 错误，变量必须以两个横杠开头 */
  foo: 1;
  -foo: 1; 

  /* 正确的定义 */
  --foo: 1;

  /* 正确，但它们是两个不同的变量 */
  --FOO: 1;
  --Foo: 1;
  
  /* 都没有问题 */
  --mainColor: red;
  --main-color: red;

  /* 错误，自定义变量不支持任何特殊字符 */
  --color@home: red;
  --black&blue: black;
  --black^2: black;
}
```

自定义变量可以使用另外一个自定义变量的值，作为初始值：

```css
html {
  --red: #a24e34;
  --green: #01f3e6;
  --yellow: #f0e765;

  --error: var(--red);
  --errorBorder: 1px dashed var(--red);
  --ok: var(--green);
  --warning: var(--yellow);
}
```

```html
<div class="box">这是一段文字</div>
```

```css
@property --my-color {
  syntax: '<color>';
  inherits: false;
  initial-value: #fff;
}

.box {
  color: var(--my-color);
}
```

支持的类型定义：



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

### 相关文章

* https://css-tricks.com/a-complete-guide-to-custom-properties/#top-of-site
* https://mp.weixin.qq.com/s/bpI9EycubFRc-7zMHRxCHA
* https://segmentfault.com/a/1190000039826626
