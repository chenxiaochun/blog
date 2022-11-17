<img src="https://img12.360buyimg.com/imagetools/jfs/t1/47609/35/22505/502534/6375b018E6bc0d03f/e1ec8ad8ad747560.png" />


五个 tab 切换样式：

相同点：

1. 宽度和高度完全相同
2. tab 的`box-shadow`大小完全相同
3. 每个 tab title 的背景色虽然不一样，但都是渐变色，也都是从 40% 透明度渐变到 10% 的透明度
4. 每个 tab 的`box-shadow`虽然颜色不同，但尺寸都是一样的

不同点：

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

定义五个颜色值：

```css
@tabColor0: #0030ff;
@tabColor1: #1854f3;
@tabColor2: #10a4ff;
@tabColor3: #32c8d1;
@tabColor4: #64cfab;
```

基于上面定义的颜色值，利用 less 循环来自动为五个 tab 实现其个性化的 css 样式：

```css
.generate-tabBarItem(@n, @i: 0) when (@i =< @n) {
    ...
}
```

* `.generate-tabBarItem`，定义一个循环名称
* `@n`，表示要生成的 class
* `@i: 0`，每一次循环的索引值，默认为 0
* `when (@i =< @n)`，每次循环的条件判断，如果满足条件，则继续循环

每个 tab 具有『选中』和『未选中』两种状态。因此，需要给每个 tab 都定义两种样式。例如，我这里使用`.selfTabBarItem0`来表示第一个 tab 的『未选中』状态，使用`.activeTabBarItem0`来表示第一个 tab 的『选中』状态。其它 tab 的状态都是按序号依次类推

* `@color: "tabColor@{i}";`，根据当前索引值，定义一个变量，用来引用上面定义的五个颜色值
* `.selfTabBarItem@{i}`，根据当前索引值，生成一个 class 名称
* `box-shadow: 0 0 6px rgba(@@color, 0.3);`，给当前 tab 添加`box-shadow`，但是其颜色值是引用上面定义的`@color`变量，需要在其前面再加一个`@`符号才可以
* 再后面就是给`h4`标签，也就是 tab title 添加渐变背景色，同样引用上面的颜色值变量。渐变角度为`90deg`，也就是从 tab title 左边渐变到右边。这里多补充一下，渐变的角度是从下往上渐变是`0deg`，顺时针旋转。从左往右渐变是`90deg`，从上下往下渐变是`180`度。因此，这里应该设置成`90deg`，从左往右渐变
* 再后面带有`.activeTabBarItem`的就是每个 tab 选中状态的样式了，道理和上面是一样的。只不过是 tab title 的文字颜色和`box-shadow`尺寸是不同的
* 最后添加一行`.generate-tabBarItem(@n, (@i + 1));`来不断的进行递归调用

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
      border-top-left-radius: @radius;
      border-top-right-radius: @radius;
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
```

完整实现：

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
      border-top-left-radius: @radius;
      border-top-right-radius: @radius;
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