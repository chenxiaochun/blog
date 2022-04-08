
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

最终 sidebar 的背景色为灰色。因为在 module 会自动去引用离它最近的祖先元素上定义的变量

媒体查询里定义的 css 变量会覆盖之前的定义：

```css
body {
  --foo: skyblue;
}

@media (max-width: 1200px) {
  body {
    --foo: orange;
  } 
}

.box{
  height: 300px;
  background: var(--foo);
}
```


## 在`:root`上定义变量

在`:root`上定义变量等同于在`html`元素上定义：

```css
:root {
  --color: red;
}

html {
  --color: red;
}
```

## `var`函数默认值机制

`var`函数支持默认参数

下面示例中，如果`--scale`没有定义的话，会使用默认值`1.2`：

```css
.bigger {
  transform: scale(var(--scale, 1.2));
}
```

下面示例中，如果`--scale`没有定义，会去尝试使用`--second-fallback`的值，如果它也没有定义，就会使用默认值`1.2`：

```css
.bigger {
  transform: scale(var(--scale, var(--second-fallback, 1.2));
}
```

## `calc`函数和自定义变量

在`calc`中使用自定义变量，只是必须要在运算符之间添加空格

```css
main {
  --spacing: 2rem;
}

.module {
  padding: var(--spacing);
}

.module.tight {
  padding: calc(var(--spacing) / 2)); 
}
```

另外一个技巧。下面的`--font-size`等于两个变量值的乘积。但是，在浏览器中并不支持这种用法：

```css
body {
  --base-font-size: 16px;
  --modifier: 2;
  --font-size: var(--base-font-size) * var(--modifier);
  font-size: var(--font-size);
}
```

但是，如果把它们放在`calc`中就能运行了：

```css
body {
  --base-font-size: 16px;
  --modifier: 2;
  --font-size: var(--base-font-size) * var(--modifier);

  font-size: calc(var(--font-size));
}
```

## 使用`initial`和『空格』的技巧

在自定义变量中使用`initial`会触发使用默认值回退机制，但使用空格却不会触发这个机制

```html
<div class="module">
  123
</div>

<div class="module dark">
  456
</div>
```

下面的示例，在`:root`上定义了两个变量：`--ON`和`--OFF`

在`.module`上定义了一个`--dark`变量，并引用了`var(--OFF)`。然后在`background`上使用`--dark`，也就间接的引用了`--OFF`。但因为`--OFF`的默认值为空格，不会触发默认值回退机制。
在`color`属性上也同理。所以，最终第一个`div`上最终展示为白底黑字

在`.dark`上就好理解了，因为我们在第二个`div`上同时了`module dark`，而且在`.dark`上重新定义了`--dark`，导致覆盖了`.module`上定义的`--dark`变量。又因为它引用的`--ON`变量，默认值为`initial`会触发默认值回退机制。所以，最终展示的就是黑底白字了

```css
:root {
  --ON: initial;
  --OFF: ;
}

.module {
  --dark: var(--OFF);
  padding: 1rem;

  background: var(--dark, black);
  color: var(--dark, white);
  letter-spacing: var(--dark, 0.2px);
  border: var(--dark, 3px solid #888);
}

.dark {
  --dark: var(--ON);
}
```

### 相关文章

* https://css-tricks.com/a-complete-guide-to-custom-properties/#top-of-site