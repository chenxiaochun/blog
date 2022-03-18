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

`diff`，以某个维度对时间进行比较，返回值为其差值：

```js
moment().add(2, 'day').diff(moment(), 'day') 
// 结果为 2
```

`startOf`，将时间设置为以某个点的开始时间：

```js
moment().startOf('year') // 设置为今年的 00:00:00
moment().startOf('day') // 设置为今天的 00:00:00
```

`endOf`，将时间设置为以某个点的结束时间：

```js
moment().endOf('year') // 设置为今年的 23:59:59
moment().endOf('day') // 设置为今天的 23:59:59
```


