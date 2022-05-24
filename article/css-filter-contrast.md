```css
body {
  width: 100vw;
  background-color: black;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}

.container {
  width: 300px;
  height: 300px;
  border: 10px solid blue;
  background-color: #222;
  overflow: hidden;
  border-radius: 50%;
  font-size: 36px;
  filter: blur(10px) contrast(10);
  display: flex;
  justify-content: center;
  align-items: center;
}

.box {
  width: 2em;
  height: 2em;
  transform: translate(0px, 0px);
  background-color: blue;
  border-radius: 50%;
  animation: move 2s linear infinite;
}

@keyframes move {
  from {
    transform: translateX(-300px);
  }

  to {
    transform: translateX(300px);
  }
}
```

```html
<div class="container">
  <div class="box"></div>
</div>
```
