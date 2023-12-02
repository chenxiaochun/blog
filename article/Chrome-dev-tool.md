# Chrome Dev Tools

打开开发工具快捷键：

```
F12
```

或者

```
Command/Ctrl + Alt + J
```

## 命令菜单

打开命令菜单方式快捷键：

```
Command/Ctrl + Shift + P
```

<img src="https://img14.360buyimg.com/imagetools/jfs/t1/167203/35/47897/68393/6731c9d7F0b2a3c89/98e585b03940e104.png" width="400" style="border: 1px solid #ccc" />

| 命令名称                       | 作用                                                                                               |
| ------------------------------ | -------------------------------------------------------------------------------------------------- |
| `Capture node screenshot`      | 对选中的当前 dom 节点截图                                                                          |
| `Capture full size screenshot` | 全屏截图。如果当前页面有滚动条，它会自动往下滚动截图                                               |
| `Show Search`                  | 打开 More Tools 中的搜索工具。可全局搜索当前站点中的文本。或者使用`Command + Option + F`快捷键打开 |
| `Show Coverage`                | 查看 js/css 的覆盖率。点击文件链接，会在`Source`面板中展示对应文件的覆盖情况。红色表示未用到的代码 |

## `Elements`

调整 css 样式值的快捷键：

| 快捷键               | 作用     |
| -------------------- | -------- |
| `Option/Alt + Up`    | 增加 0.1 |
| `Option/Alt + Down`  | 减少 0.1 |
| `Shift + Up`         | 增加 10  |
| `Shift + Down`       | 减少 10  |
| `Command/Win + Up`   | 增加 100 |
| `Command/Win + Down` | 减少 100 |

## `Console`

| 命令名称          | 作用                                                                                       |
| ----------------- | ------------------------------------------------------------------------------------------ |
| `$_`              | 可以引用上一次的执行结果                                                                   |
| `copy()`函数      | 可以将传入的值复制到剪贴板，例如执行`copy(location)`就能将`location`对象中的值复制到剪贴板 |
| `keys()`          | 快速查看一个对象的所有 key                                                                 |
| `values()`        | 快速查看一个对象的所有 value                                                               |
| `console.log()`   | 打印日志                                                                                   |
| `console.table()` | 以表格的形式打印对象或者数组数据。如果传入的是一个普通类型，则直接输出                     |

## `Network`

### 重新发起`xhr`请求

鼠标右击请求地址，选择`Replay XHR`，可以重新发起请求，不一定非要刷新页面（但我发现，并不是每个 xhr 链接上都有此功能，不太明白是为什么）

### `Screenshots`

点击右侧齿轮，然后勾选`Screenshot`。重新刷新页面，就可以捕获页面加载时的每一帧截图，就像幻灯片一样。单击每一帧截图，就会展示当前时刻发生的网络请求。这种可视化的展现形式可以让你非常清楚的了解当前页面网络请求情况

<img src="https://img10.360buyimg.com/imagetools/jfs/t1/155906/27/51211/116533/6731f332F27dbd947/3905d08db4130037.png" width="600" style="border: 1px solid #ccc" />

## `Performance`

### `CPU throttling`和`Network throttling`

可模拟低端设备性能和慢网络环境：

<img src="https://img11.360buyimg.com/imagetools/jfs/t1/246452/12/24129/15741/67359b7fF59d0ee4f/ca289a4cb979dcaf.png" width="400" style="border: 1px solid #ccc" />

## More Tools

这其实是一个工具集合，位置比较隐藏，具体打开方式如下图所示：

<img src="https://img14.360buyimg.com/imagetools/jfs/t1/167277/39/50909/96954/6732ad67F3fcab077/a9504a4b02263bbf.png" width="400" style="border: 1px solid #ccc" />

## 参考链接

- https://juejin.cn/post/6844904162602254350
- https://mp.weixin.qq.com/s/IHzGFrhHuOWv0tIyZqVEGg
