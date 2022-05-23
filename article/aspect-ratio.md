`aspect-ratio`用来给容器设置一个期待的纵横比

```html
<div class="box"></div>
```

```css
.box {
  border: 1px solid #ccc;
  width: 300px;
  aspect-ratio: 1 / 1; // 也可以简写为 aspect-ratio: 1;
}
```

通过设置`aspect-ratio: 1 / 1;`，虽然只设置了`width: 300px`，高度也会按照比例渲染为`height: 300px;`。反之，只设置`height: 300px`，宽度也会按照比例进行渲染

所以，通过它可以所以容易的设置元素的宽度和高度比例，例如：

```css
aspect-ratio: 1 / 2; //高度为宽度的 2 倍
aspect-ratio: 1 / 0.5; //高度为宽度的 0.5 倍
```

![image](https://user-images.githubusercontent.com/1744713/160584124-dcc0f1ee-bcdb-447b-9a96-1b987a014630.png)

aspect-ratio: 1 / 0.5; //高度为宽度的 0.5 倍

这个属性尤其适用于限制图片的展示比例：

```css
img {
  border: 1px solid #ccc;
  max-width: 300px;
  aspect-ratio: 1 / 1;
}
```

![image](https://user-images.githubusercontent.com/1744713/160583308-a2a2cc0e-5adb-4e62-b3c6-ba39a81c4f33.png)

最后，此特性目前兼容性还挺不错，现在就可以用起来了：https://caniuse.com/?search=aspect-ratio



