<img src="https://img12.360buyimg.com/imagetools/jfs/t1/47609/35/22505/502534/6375b018E6bc0d03f/e1ec8ad8ad747560.png" />


五个 tab 的样式

相同的地方：

1. 宽度和高度完全相同
2. tab 的`box-shadow`大小完全相同
3. 每个 tab title 的背景色虽然不一样，但都是渐变色，也都是从 40% 透明度渐变到 10% 的透明度
4. 每个 tab 的`box-shadow`虽然颜色不同，但尺寸都是一样的

不同的地方：

1. 颜色，只是颜色不同


先写一段样式，将每个 tab 中都相同的地方基本样式实现出来：

```css
@radius: 4px;

.tabBarItem {
    position: relative;
    cursor: pointer;
    min-width: 216px;
    min-height: 161px;
    border-top-left-radius: @radius;
    border-top-right-radius: @radius;
    margin-right: 25px;
    background: #fff;
    transition: 0.2s all;

    &:last-child {
        margin-right: 0;
    }
}
```

定义这五个 tab 的颜色值：

```css
@tabColor0: #0030ff;
@tabColor1: #1854f3;
@tabColor2: #10a4ff;
@tabColor3: #32c8d1;
@tabColor4: #64cfab;
```

基于上面定义的颜色值，利用 less 循环来自动为五个 tab 实现其个性化的 css 样式：

* `.generate-tabBarItem`，定义一个循环名称
* `@n`，表示要循环的总次数
* `@i: 0`，每一次循环的当前索引值，默认为 0
* `when (@i =< @n)`，每次循环的条件判断，如果满足条件，则继续循环

```css
.generate-tabBarItem(@n, @i: 0) when (@i =< @n) {
    ...
    ...

    .generate-tabBarItem(@n, (@i + 1));
}
```

从设计稿可以看出来，每个 tab 都具有『选中』和『未选中』两种状态

我这里使用`.selfTabBarItem` + 索引值，来表示 tab 的『未选中』状态样式。例如`.selfTabBarItem0`表示第一个 tab 的『未选中』状态样式

使用`.activeTabBarItem` + 索引值，来表示 tab 的『选中』状态。例如`.activeTabBarItem0`表示第一个 tab 的『选中』状态样式。其它 tab 的状态都是按序号依次类推

开始编写样式循环体：

* `@color: "tabColor@{i}";`，根据当前索引值，定义一个变量，分别用来引用上面定义的五个颜色值。例如，第一次循环时生成的就是`@color: tabColor0;`
* `.selfTabBarItem@{i}`，根据当前索引值，生成一个 class 名称。例如，第一次循环时生成的就是`.selfTabBarItem0`
* `box-shadow: 0 0 6px rgba(@@color, 0.3);`，给当前 tab 添加`box-shadow`，注意其颜色值是引用上面定义的`@color`变量，需要在其前面再加一个`@`符号
* 再后面就是给`h4`标签，也就是 tab title 添加渐变背景色，同样引用上面的颜色值变量。渐变角度为`90deg`，也就是从 tab title 左边渐变到右边。这里多补充一下，渐变的角度是从下往上渐变是`0deg`，顺时针旋转。从左往右渐变是`90deg`，从上下往下渐变是`180deg`，从右往左渐变是`270deg`。因此，这里应该设置成`90deg`，从左往右渐变
* 再然后就是生成是每个 tab 『选中』状态的样式，道理和上面是一样的。只不过是 tab title 的文字颜色和`box-shadow`尺寸是不同的
* 最后需要添加一行`.generate-tabBarItem(@n, (@i + 1));`来不断的进行递归调用

```css
.generate-tabBarItem(@n, @i: 0) when (@i =< @n) {
  @color: "tabColor@{i}";

  .selfTabBarItem@{i} {
    box-shadow: 0 0 6px rgba(@@color, 0.3);
    & > h4 {
      background: linear-gradient(
        90deg,
        rgba(@@color, 0.4) 100%,
        rgba(@@color, 0.1) 0%
      );
    }
  }

  .activeTabBarItem@{i} {
    box-shadow: 0 0 30px rgba(@@color, 0.4);
    & > h4 {
      color: #fff;
      background: linear-gradient(
        90deg,
        rgba(@@color, 1) 100%,
        rgba(@@color, 0.5) 0%
      );
    }
  }

  .generate-tabBarItem(@n, (@i + 1));
}
```

根据设计稿，定义 tabPanel 样式：

```css
.tabPanel {
  box-shadow: 0 -6px 6px -3px rgba(0, 0, 0, 0.1);
  min-height: 300px;
  padding: 1em;
  z-index: 1;
  position: relative;
  background: #fff;
}
```

写完之后预览一下效果：

<img src="https://img10.360buyimg.com/imagetools/jfs/t1/176517/20/31378/10357/637b32e8E6dceae3b/70aa63dea62bca2b.jpg" />

在 tab 与 tabPanel 衔接的位置，还需要用一个元素盖一下底部的阴影：

```css
.generate-tabBarItem(@n, @i: 0) when (@i =< @n) {
  ...

  .activeTabBarItem@{i} {
    box-shadow: 0 0 30px rgba(@@color, 0.4);
    & > h4 {
      color: #fff;
      background: linear-gradient(
        90deg,
        rgba(@@color, 1) 0%,
        rgba(@@color, 0.5) 100%
      );
    }

    &::after {
      display: block;
      content: "";
      width: 100%;
      height: 15px;
      position: absolute;
      left: 0;
      bottom: -5px;
      z-index: 3;
    }
  }
  .generate-tabBarItem(@n, (@i + 1));
}

```

<img src="https://img10.360buyimg.com/imagetools/jfs/t1/134349/37/28162/10427/637b3405E12afccae/8e1d641c80ba1109.jpg" />

到目前为止，主要交互样式貌似都实现了。但这里还有一个细节没有实现。就是 tab 与 tabPanel 的衔接位置的圆角没有实现

圆角怎么实现呢？这里我准备使用 css 的径向渐变来实现这个圆角。先来看一个最简单的径向渐变示例

下面是一个从红色渐变到蓝色的简单示例，从左到右，每个参数的含义为：

1. `circle`，表示生成一个圆形渐变。因为它还支持椭圆形渐变`ellipse`
2. `50px`，表示圆形渐变的半径是多少
3. `at 50% 50%`，表示圆形渐变的圆心位置
4. `red blue`，表示从红色渐变到蓝色

```css
div{
  width: 200px;
  height: 200px;
  background: radial-gradient(circle 50px at 50% 50%, red, blue);
}
```

<img src="https://img10.360buyimg.com/imagetools/jfs/t1/199064/29/28019/63585/638066f0E94ee7516/5c774f2de564bac5.png" width="200" />

明白渐变的基本用法之后，将圆形渐变的坐标改为右上角，渐变颜色改成从透明色渐变到蓝色：

```css
div{
  background: radial-gradient(circle 50px at 100% 0, transparent, blue);
}
```

<img src="https://img13.360buyimg.com/imagetools/jfs/t1/176443/36/31135/38029/638067e0E061bd01a/534b6895be364f4a.png" width="200" />

可以看出来。两种颜色之间没有明显的界限，还是缓慢渐变过去的。再进一步，如果给透明色添加一个`50px`的渐变范围，再看一下效果：

```css
div{
  background: radial-gradient(circle 50px at 100% 0, transparent 50px, blue);
}

```

<img src="https://img10.360buyimg.com/imagetools/jfs/t1/126214/22/25533/28154/63806862E33f7c0f4/980599c0d550d167.png" width="200" />

所以，为什么是`50px`？改为`40px`或者`60px`是否可以？

这是因为圆形渐变的半径是`50px`，而渐变中第一个颜色的渐变停止范围就达到了`50px`，仅仅是第一个颜色就已经覆盖了整个圆形，也就不存在和其它颜色的过渡了。因此，你设置成`40px`不可以，但只要设置成大于`50px`都是可以的

明白了上面的原理，再来实现我们项目中这个 tab 圆角就很简单了

首先，需要把刚才用来遮挡的伪元素拉长一点儿。为了自适应 tab 宽度，可以设置成`width: calc( 100% + 10px )`

<img src="https://img11.360buyimg.com/imagetools/jfs/t1/169857/31/32539/10707/637b3572E6ba0f128/cc4bf52271b99347.jpg" />


然后把突出来的部分，使用径向渐变将其变成一个内圆角：

```css
.generate-tabBarItem(@n, @i: 0) when (@i =< @n) {
  ...

  .activeTabBarItem@{i} {
    ...

    &::before,
    &::after {
      display: block;
      content: "";
      width: calc(100% + 10px);
      height: 15px;
      position: absolute;
      bottom: -5px;
      z-index: 3;
    }
    &::before {
      background: radial-gradient(
        circle 10px at 0 0,
        transparent 60px,
        white 30px
      );
      left: -10px;
    }
    &::after {
      background: radial-gradient(
        circle 10px at 100% 0,
        transparent 60px,
        white 30px
      );
      left: 0px;
    }
  }
  .generate-tabBarItem(@n, (@i + 1));
}
```

比如，把渐变背景色改成红色，会更明显的多：

<img src="https://img14.360buyimg.com/imagetools/jfs/t1/144973/2/31830/10657/637b36fdE1d94f71d/07802c5ca10f64d9.jpg" />


下面是效果的完整实现：

```css
.generate-tabBarItem(@n, @i: 0) when (@i =< @n) {
  @color: "tabColor@{i}";

  .selfTabBarItem@{i} {
    box-shadow: 0 0 6px rgba(@@color, 0.3);
    & > h4 {
      background: linear-gradient(
        270deg,
        rgba(@@color, 0.1) 0%,
        rgba(@@color, 0.4) 100%
      );
    }
  }

  .activeTabBarItem@{i} {
    box-shadow: 0 0 30px rgba(@@color, 0.4);
    & > h4 {
      color: #fff;
      background: linear-gradient(
        270deg,
        rgba(@@color, 0.5) 0%,
        rgba(@@color, 1) 100%
      );
    }

    &::before,
    &::after {
      display: block;
      content: "";
      width: calc(100% + 10px);
      height: 15px;
      position: absolute;
      bottom: -5px;
      z-index: 3;
    }
    &::before {
      background: radial-gradient(
        circle 10px at 0 0,
        transparent 60px,
        white 30px
      );
      left: -10px;
    }
    &::after {
      background: radial-gradient(
        circle 10px at 100% 0,
        transparent 60px,
        white 30px
      );
      left: 0px;
    }
  }
  .generate-tabBarItem(@n, (@i + 1));
}
```

利用径向渐变实现 tab 底部圆角：



## 参考文章

* https://www.jianshu.com/p/1b0ec60dae85
* https://github.com/chenxiaochun/blog/issues/4