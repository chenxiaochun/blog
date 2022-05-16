## node url 库方法说明

### `format`方法

将传入的 url 对象转换为一个标准的 url 字符串

```js
url.format({
  pathname: "/test",
  query: {
    a: 1,
  },
  hash: "#hash",
})
```

```js
"/test?a=1#hash"
```

### `parse`方法

将传入的标准的 url 字符串转换为一个 url 对象，作用和`format`方法相反

```js
url.parse("/test?a=1#hash")
```

我们看`query`属性，它的值还是字符串的形式。如果想要将其转换为对象，需要将`parse`方法的第二个参数设置为`true`

```js
{
  "protocol": null,
  "slashes": null,
  "auth": null,
  "host": null,
  "port": null,
  "hostname": null,
  "hash": "#hash",
  "search": "?a=1",
  "query": "a=1",
  "pathname": "/test",
  "path": "/test?a=1",
  "href": "/test?a=1#hash"
}
```

重新输出：

```js
url.parse("/test?a=1#hash", true)
```

```js
{
 ... 
  "query": {
    "a": "1"
  },
  ...
}
```

`parse`方法还有第三个参数，它的默认值为`false`。表示是否以斜线解析主机名

```js
url.parse("//foo/bar")
```

从下面的输出结果中的`pathname`属性可以看到。`parse`方法没有对传入的 url 以斜线做进一步的解析，而是直接将其作为了`pathname`属性值

```js
{
  "protocol": null,
  "slashes": null,
  "auth": null,
  "host": null,
  "port": null,
  "hostname": null,
  "hash": null,
  "search": null,
  "query": null,
  "pathname": "//foo/bar",
  "path": "//foo/bar",
  "href": "//foo/bar"
}
```

如果将`parse`方法的第三个参数设置为`true`之后。以斜线分隔的`foo`被解析成了`host`，而`bar`被解析成了`pathname`

```js
{
  "protocol": null,
  "slashes": true,
  "auth": null,
  "host": "foo",
  "port": null,
  "hostname": "foo",
  "hash": null,
  "search": "",
  "query": {},
  "pathname": "/bar",
  "path": "/bar",
  "href": "//foo/bar"
}
```