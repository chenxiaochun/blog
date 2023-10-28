## Network Override Content

Chrome 升级到 117 及以上版本之后，开发者工具中自带了一个 override content 功能。使用它，可以用来模拟浏览器中所有请求的返回内容。当然也可以用来 mock fetch 接口的返回内容

这个功能立刻可以解决前端日常研发中一个很大的痛点，就是当后端定义了接口，但因为具体逻辑没有实现，没有返回值。此时，前端想验证一下自己的逻辑，只能去使用一些第三方 mock 平台去实现

### 开启 override content

1. 在 Network 面板中，找到需要 mock 的接口，鼠标右击，选择 override content

<img src="https://img13.360buyimg.com/imagetools/jfs/t1/229307/8/1034/254273/653cc951Ff5f0cee8/70e0f7d23e9e28fc.png" width="400" />

2. 浏览器会提示你选择一个本地文件夹，并授予权限

<img src="https://img14.360buyimg.com/imagetools/jfs/t1/95938/27/41271/70317/653ccdfeFa1ee99a7/361a500f0322439e.png" width="400" />

3. 授权之后，开发者工具会自动跳转到一个编辑区域，在此处输入你要 mock 的内容即可

<img src="https://img12.360buyimg.com/imagetools/jfs/t1/115547/8/40830/221463/653cce9eFb546736f/81177f3ef1821b5b.png" width="400" />

### 关闭 override content

如果你只是想暂时关闭一下 override content 功能，勾掉`Enabled Local Overrides`前面的勾即可。稍后如果想继续使用 override content 功能，再勾上即可

而`Enabled Local Overrides`后面的“关闭”图标，是完全关闭本次 override content 功能。如果想继续使用，需要按上面的方法重新给浏览器授权

<img src="https://img12.360buyimg.com/imagetools/jfs/t1/186787/39/40878/111521/653cc9d0F5534450f/73ac39b5f26af3af.png" width="400" />

## 相关资料

* https://juejin.cn/post/7083396282072432647