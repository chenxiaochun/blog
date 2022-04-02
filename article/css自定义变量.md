
使用 css 自定义变量的好处：

1. 可以最大化的提高代码利用率，减少代码重复
2. 在某些场景尤其适用于 css 自定义属性，比如说，定义网站的主题颜色
3. 可以使用 js 更新操作 css 自定义属性，这就又带来了更多有意思的可能性

### 自定义变量的命名规范

自定义变量必须存在于一个 css 选择器中，并且必须以两个横杠（--）开头，否则无效

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

可以根据需要，将自定义变量进行拆分再组合：

```css
button {
  --h: 100;
  --s: 50%;
  --l: 50%;
  --a: 1;

  background: hsl(var(--h) var(--s) var(--l) / var(--a));
}

/* hover 的时候可以单独改变其亮度 */
button:hover {
  --l: 75%;
}

/* focus 的时候可以单独改变其饱合度 */
button:focus {
  --s: 75%;
}

button[disabled] {
  --s: 0%;
  --a: 0.5;
}
```

控制阴影的大小：

```css
button {
  --spread: 5px;
  box-shadow: 0 0 20px var(--spread) black;
}
button:hover {
  --spread: 10px;
}
```

控制渐变的角度：

```css
body {
  --angle: 180deg;
  background: linear-gradient(var(--angle), red, blue);
}
body.sideways {
  --angle: 90deg;
}
```

## 自定义变量的层叠规则

例如，我在`body`和`.sidebar`上都定义了一个`--background`变量，然后在`.module`使用了此变量：

```css
body {
  --background: white;
}
.sidebar {
  --background: gray;
}
.module {
  background: var(--background);
}
```

它的 html 结构如下所示：

```html
<body> <!-- --background: white -->
  <main>
    <div class="module">
      I will have a white background.
    </div>
  <main>

  <aside class="sidebar"> <!-- --background: gray -->
    <div class="module">
      I will have a gray background.
    </div>
  </aside>
</body>
```

这里的话，sidebar 的背景色为灰色。因为在 module 会自动去引用离它最近的祖先元素上定义的变量

### 相关文章

* https://css-tricks.com/a-complete-guide-to-custom-properties/#top-of-site