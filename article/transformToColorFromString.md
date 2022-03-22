通过字符串生成颜色值：

```js
function hashCode(str) {
  var hash = str.charCodeAt(i) + ((hash << 5) - hash);
  return hash;
}

function intToRGB(i) {
  var c = (i & 0x00FFFFFF)
    .toString(16)
    .toUpperCase();
  return "00000".substring(0, 6 - c.length) + c;
}
```

调用方式：

```js
intToRGB(hashCode('cxc'))
```
