当我们在 html 页面中直接加载一个远程的 js 文件，即便它和当前页面是跨域关系，此 js 文件中的代码一般都是可以正常运行的。因为 script 标签默认支持跨域加载远程资源

```html
<script src="http://local.jd.com:8080/src/test.js"></script>
```

如果加载的远程 js 代码发生了内部错误，我们可以在当前 html 页面中通过`window.onerror`去进行捕获，并输出其错误信息

例如，下面的示例就是远程加载的`test.js`中的一段代码。在一个定时器试图去访问一个不存在的对象属性，结果肯定会抛出异常

```js
setTimeout(() => {
    console.log(obj.a)
}, 1000)
```

在当前 html 页面中，使用`window.onerror`对其进行捕获

```html
<script>
window.onerror = error => {
    console.log(error)
}
</script>
```

可是，实际运行之后，你会发现，它只是输出一个简短的象征性信息。具体错误信息是什么，都不得而知。是因为浏览器为了安全性，自动屏蔽了远程跨域 js 中的错误信息

```
Script error.
```

想要查看完整的报错信息，需要在加载远程 js 的 script 标签上添加`crossorigin`属性，并且远程对方服务器需要在响应头中返回`Access-Control-Allow-Origin: *`。此时，就可以
看到完整和错误信息了：

```html
<script src="http://local.jd.com:8080/src/test.js" crossorigin></script>
```

```
Uncaught ReferenceError: obj is not defined
```

此外，`crossorigin`还支持两个属性值：

* `anonymous`代表同域会带上 cookie，跨域则不带上 cookie，相当于 fetch 请求的`credentials: 'same-origin'`
* `use-credentials`，跨域也会带上 cookie，相当于 fetch 请求的`credentials: 'include'`。这种情况，需要对方在响应头中返回`Access-Control-Allow-Credentials: true`，否则会请求失败
