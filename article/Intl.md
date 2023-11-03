## `Intl.NumberFormat`

`Intl.NumberFormat`用来根据语言环境来对数值进行格式化

下面三个示例，展示了在三个不同国家语言环境下，同一个数值的不同展示形式

```js
const number = 123456.789;

console.log(
  new Intl.NumberFormat('de-DE', { style: 'currency', currency: 'EUR' }).format(
    number,
  ),
);
// Expected output: "123.456,79 €"

// The Japanese yen doesn't use a minor unit
console.log(
  new Intl.NumberFormat('ja-JP', { style: 'currency', currency: 'JPY' }).format(
    number,
  ),
);
// Expected output: "￥123,457"

// Limit to three significant digits
console.log(
  new Intl.NumberFormat('en-IN', { maximumSignificantDigits: 3 }).format(
    number,
  ),
);
// Expected output: "1,23,000"
```

### `Intl.NumberFormat.format`

在 js 中，一个数值如果太大或者太小，都有可能会丢失精度。比如你正在进行的运算中，如果有数值超过了`Number.MAX_SAFE_INTEGER`的范围，那么就应该使用`BigInt`类型来代替`Number`类型

例如有一个数值`x`已经超过了`Number.MAX_SAFE_INTEGER`：

```js
cont x = 1234567891234567891
console.log(x > Number.MAX_SAFE_INTEGER) // true
```

这时如果用`Intl.NumberFormat`来对这个数值进行格式化，就会丢失精度：

```js
new Intl.NumberFormat("en-US").format(1234567891234567891) // 1,234,567,891,234,568,000
```

而如果换成`BigInt`类型（数值后面加一个小写的 n 即可），就不会丢失精度：

```js
new Intl.NumberFormat("en-US").format(1234567891234567891n) // 1,234,567,891,234,567,891
```

还有一种方式就是使用字符串的形式来表式数值，这样使用`Intl.NumberFormat`进行格式化也不会丢失精度：

```js
new Intl.NumberFormat("en-US").format("1234567891234567891"); // 1,234,567,891,234,567,891
```

## 原文链接

* https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/NumberFormat