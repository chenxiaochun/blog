```html
<div id="app">
  <div class="box top"></div>
  <div class="box bottom"></div>
  <div class="box front"></div>
  <div class="box back"></div>
  <div class="box right"></div>
  <div class="box left"></div>
</div>
```

```css
@keyframes fastspin{
    0% {
        transform: rotateX(-33.5deg) rotateY(45deg)
    }

    40%,to {
        transform: rotateX(-33.5deg) rotateY(-315deg)
    }
}

#app{
  transform-style: preserve-3d;
  transform: rotateX(-30deg) rotateY(45deg);
  position: absolute;
  top: 200px;
  left: 50%;
  margin-left: -100px;
  animation: fastspin 6s ease-in-out infinite;
  transform-origin: 100px;
}

.box{
  position: absolute;
  width: 200px;
  height: 200px;
  opacity: 0.6;
}

.top{
  background-color: red;
  transform: rotateX(90deg) translateZ(100px);
}

.bottom{
  background-color: yellow;
  transform: rotateX(-90deg) translateZ(100px);
}

.front{
  background-color:green;
  transform: translateZ(100px);
}

.back{
  background-color: orange;
  transform: rotateX(-180deg) translateZ(100px);
}

.right{
  background-color: orchid;
  transform: rotateY(90deg) translateZ(100px);
}

.left{
  background-color: blue;
  transform: rotateY(-90deg) translateZ(100px);
}
```

在线 demo：https://code.juejin.cn/pen/7227007694908391485


参考资料：

https://www.cnblogs.com/coco1s/p/5414153.html