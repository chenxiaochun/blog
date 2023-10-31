## `Intl.NumberFormat`

```js
Intl.NumberFormat()
```

在 js 中，一个数字如果太大或者太小，都会有可能丢失精度。比如你正在进行的运算中，如果有数字超过了`Number.MAX_SAFE_INTEGER`的范围，那么就应该使用`BigInt`类型来代替`Number`类型

例如有一个数字`x`已经超过了`Number.MAX_SAFE_INTEGER`：

```js
cont x = 1234567891234567891
console.log(x > Number.MAX_SAFE_INTEGER) // true
```

这时如果用`Intl.NumberFormat`来对这个数字进行格式化，就会丢失精度：

```js
new Intl.NumberFormat("en-US").format(1234567891234567891) // 1,234,567,891,234,568,000
```

而如果换成`BigIni`类型（数字后面加一个小写的 n 即可），就不会丢失精度：

```js
new Intl.NumberFormat("en-US").format(1234567891234567891n) // 1,234,567,891,234,567,891
```

还有一种方式就是使用字符串的形式来表式数字，这样使用`Intl.NumberFormat`也不会丢失精度：

```js
new Intl.NumberFormat("en-US").format("1234567891234567891"); // 1,234,567,891,234,567,891
```

## 原文链接

* https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/NumberFormat