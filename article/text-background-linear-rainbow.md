# github 文章标题彩虹渐变动效

昨天逛 github，看到其博客文章，鼠标移上去，会显示这样一个酷炫的动效。引起我极大的好奇，遂想要研究一下其是如何实现的

<img src="https://storage.jd.com/hdjfiles/text-background-linear-rainbow01.gif" width="350" />


## 实现思路

新建 html 文件，添加一个包含 span 的 h3 元素，给 h3 指定 class 为 title。具体结构和样式如下所示：

```html
<h3 class="title">
  <span>Understanding the SDLC: Software Development Lifecycle Explained</span>
</h3>
```

```css
.title {
  width: 300px;
  font-size: 30px;
}
```

<img src="https://img13.360buyimg.com/imagetools/jfs/t1/97579/34/33153/18367/65815f7dF6f53c180/5578f4a84333e82d.png" width="300" />

给 title 添加一个渐变，并设置为 no-repeat。此时的样式如下所示：

```css
.title {
  width: 300px;
  font-size: 30px;
  background-image: linear-gradient(90deg, #8250df 0, #d42a32 49.9%, #24292f 50%, #24292f 100%);
  background-repeat: no-repeat;
}
```

<img src="https://img10.360buyimg.com/imagetools/jfs/t1/228772/36/9690/43743/65815fefFde98d254/14962874347359b1.png" width="300" />

我们要实现的效果是，文字的初始颜色为黑色，只是鼠标移上去时，才渐变为彩虹色。而且我们知道背景渐变色的最右侧的颜色才是黑色
因此，需要给 title 元素设置  background-position: 100% 0，也就是将其背景色的水平起始位置设置为最右侧，垂直起始位置还是最上方。再添加一个 background-size: 200%，这样可以直接将背景渐变以最右侧为基准放大拉伸 200%，此时就只能看到黑色背景色了
代码和效果如下所示：

```css
.title {
  width: 300px;
  font-size: 30px;
  background-image: linear-gradient(90deg, #8250df 0, #d42a32 49.9%, #24292f 50%, #24292f 100%);
  background-repeat: no-repeat;
  background-position: 100% 0;
  background-size: 200%;
}
```

<img src="https://storage.jd.com/hdjfiles/text-background-linear-rainbow02.png" width="300" />

为了能将文字显示出来，需要先将其文字颜色设置为 transparent，再设置其镂空效果为 text：

```css
.title {
  width: 300px;
  font-size: 30px;
  color: transparent;
  background-image: linear-gradient(90deg, #8250df 0, #d42a32 49.9%, #24292f 50%, #24292f 100%);
  background-repeat: no-repeat;
  background-position: 100% 0;
  background-size: 200%;
  background-clip: text;
}
```

此时看到的效果如下所示。呃，好像没什么变化？🌝不要着急，因为我们还没有给其添加 hover 渐变动画

<img src="https://img13.360buyimg.com/imagetools/jfs/t1/97579/34/33153/18367/65815f7dF6f53c180/5578f4a84333e82d.png" width="300" />

title hover 时，需要展示彩色背景色。上面我们把背景渐变的 background-position 设置为了水平最右侧，默认情况下展示的是黑色背景。因此，title hover 时，只需要将 background-position 设置为水平最左侧，再配合上 transtion，即可实现一个彩虹背景渐变动画了🏳️‍🌈

```css
.title {
  transition: background-position 1s cubic-bezier(.16, 1, .3, 1);
}

.title:hover {
  background-position: 0;
}
```

<img src="https://storage.jd.com/hdjfiles/text-background-linear-rainbow03.gif" width="300" />

接着给文字添加下划线动画，这部分动画需要加在 title 内的 span 元素上，也是用线性渐变来实现

具体实现思路是：

1. 渐变角度设置为 180deg，也就是从上往下渐变
2. 渐变颜色从 transparent 渐变到 #a74395
3. 为了看起来是下划线的效果，必须让 transparent 渐变到元素背景 97% 的位置，让另外一种颜色没有渐变，也就是设置渐变终止位置为 0
4. 设置 background-repeat: no-repeat。如果不设置它，动画会有一些瑕疵，没想明白原因🤔
5. 设置 background-size: 0 100%，也就是默认情况下不展示此渐变背景

```css
.title span {
    background: linear-gradient(180deg, transparent 97%, #a74395 0);
    background-repeat: no-repeat;
    background-size: 0 100%;
    transition: background-size 1s cubic-bezier(.16, 1, .3, 1);
}
```

6. 当 title hover 时，设置 span 元素背景的 background-size: 100% 100% 即可：

```css
.title:hover span {
    background-size: 100% 100%;
}
```

## 完整实现

html 代码：

```html
<h3 class="title">
  <span>Understanding the SDLC: Software Development Lifecycle Explained</span>
</h3>
```

css 代码：

```css
.title {
    width: 300px;
    font-size: 30px;
    color: transparent;
    background-image: linear-gradient(90deg, #8250df 0, #d42a32 49.9%, #24292f 50%, #24292f 100%);
    background-repeat: no-repeat;
    background-position: 100% 0;
    background-clip: text;
    background-size: 200%;
    transition: background-position 1s cubic-bezier(.16, 1, .3, 1);
}

.title:hover {
    background-position: 0;
}

.title span {
    background: linear-gradient(180deg, transparent 97%, #a74395 0);
    background-repeat: no-repeat;
    background-size: 0 100%;
    transition: background-size 1s cubic-bezier(.16, 1, .3, 1);
}

.title:hover span {
    background-size: 100% 100%;
}
```

## 效果来源

https://resources.github.com/