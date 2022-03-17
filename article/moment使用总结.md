展示大写的星期，例如：星期四

```js
// 使用 moment
const week = moment().day().toLocaleString('zh-Hans-CN-u-nu-hanidec')
return `星期${week}`

// 使用原生 js
const week = new Date().getDay().toLocaleString('zh-Hans-CN-u-nu-hanidec')
return `星期${week}`

// 参考链接：https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString
```
