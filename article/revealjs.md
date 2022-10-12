### 使用 markdown 格式编写 ppt

本地新建一个`markdown.md`文件，然后在`index.html`中将其引入

```html
<div class="reveal">
  <div class="slides">
    <section data-markdown="markdown.md" data-separator="^\n---\n$" data-separator-vertical="^\n-----\n$"
      data-separator-notes="^Note:">
    </section>
  </div>
</div>
```

### 在 markdown 中编写 ppt 时的语法说明

* `---`或者`section`元素，表示添加一页水平方向滚动的 ppt

* `-----`，表示添加一页垂直方向滚动的 ppt

* 给当前 ppt 页面添加背景图或者背景色

```html
<!-- .slide: data-background-image="https://miro.medium.com/max/851/1*TLqYA0gwLrLAfXMGoTWFlQ.png" data-background-opacity="0.1" data-background-size="contain" -->
ppt content
```

* 代码依次按行高亮

<pre>
```js [1-2|3|4-6]
const a = 1;

function test() {
  console.log(a)
}

const b = 2;
```
</pre>

### 操作说明

* 演讲者模式

按`s`键，进入演讲者模式

```html
<section>
  <p>This slide has some speaker notes</p>
  <aside class="notes">
    These are the speaker notes.
    They are only visible in a separate window after pressing the s key.
  </aside>
</section>
```

### 参考资料

* https://github.com/gitqwerty777/revealjs-markdown/blob/gh-pages/markdown.md