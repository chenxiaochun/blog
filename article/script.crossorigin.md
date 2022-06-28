在 html 页面中直接加载一个远程的 js 文件。它和 html 页面是跨域关系：

```html
<script src="http://local.jd.com:8080/src/test.js"></script>
```

正常情况下，script 标签加载的远程代码是支持跨域的。可直接在当前页面中运行。但如果远程加载的 js 代码发生了错误呢？

例如，试图在一个定时器中去访问一个不存在对象的属性。然后在 html 页面中使用`window.onerror`去进行捕获，并输出其具体错误信息

```js
setTimeout(() => {
    console.log(obj.a)
}, 1000)
```

```html
<script>
window.onerror = error => {
    console.log(error)
}
</script>
```

可是，实际运行之后，你会发现，它只是输出一个简短的象征性信息。这是因为浏览器为了安全性，自动屏蔽了远程跨域 js 中的错误信息

```
Script error.
```

想要查看完整的报错信息，需要在 html 的 script 标签上添加`crossorigin`属性，并且远程对方服务器在响应头中返回`Access-Control-Allow-Origin: *`：

```
Uncaught ReferenceError: obj is not defined
```

此外，`crossorigin`还支持两个属性值：

* `anonymous`代表同域会带上 cookie，跨域则不带上 cookie，相当于 fetch 请求的`credentials: 'same-origin'`
* `use-credentials`，跨域也会带上 cookie，相当于 fetch 请求的`credentials: 'include'`。这种情况，需要对方在响应头中返回`Access-Control-Allow-Credentials: true`，否则会请求失败
