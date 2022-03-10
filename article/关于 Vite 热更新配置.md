1、

如果项目中的热更新突然失效了，可以试着将`vite`和`@vitejs/plugin-react-refresh`降为以下版本：

```json
{
    "vite": "2.3.8",
    "@vitejs/plugin-react-refresh": "1.3.4",
}
```

经过我的测试，这两个依赖项的版本高了不行，低了也行，必须得是这两个版本。也有可能更新其中一个到指定版本就可以了，这你得自己试试

2、

如果项目中使用了修饰器语法，你就可能使用[`@vitejs/plugin-react`](https://github.com/vitejs/vite/tree/main/packages/plugin-react)插件来进行处理。在 vite 中配置了它以后，就不能再使用`@vitejs/plugin-react-refresh`，否则就会产生冲突

```js
{
  plugins: [
    react({
      babel: {
        plugins: [
          ['@babel/plugin-proposal-decorators', { legacy: true }],
          ['@babel/plugin-proposal-class-properties', { loose: true }],
        ],
      },
    }),
  ]
}
```

这时候如果热更新失效了，可以试着将`vite`、`@vitejs/plugin-react`都更新到最新版本试试。如果更新完之后，有一些其它 vite 插件抛异常了，也可以试着将其都更新到最新版本。比如，在我的项目中`vite-plugin-externals`就抛了异常，我直接将其更新到最新版本就解决了
