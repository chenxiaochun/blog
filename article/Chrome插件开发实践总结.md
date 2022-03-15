
### 传统发布方式

一个插件开发完成之后，下面这张图描述了发布浏览器插件最常见的几个步骤。你可以看到其中每一步放在公司的业务环境中都会存在一些问题：

<img width="700" src="https://img12.360buyimg.com/imagetools/jfs/t1/151546/17/9878/262706/5fd75d99E7d5afb96/ea9bc95c3f530ec4.jpg">

1. 发布到 Chrome 的插件市场上，需要每年 5 美金的费用。也因为我们是内部系统，需要考虑业务及代码的保密性，所以发布到外网上也是不合适的
2. 发布之后需要等待 Chrome 官方的审核，这个时间可能就不确定了。如果是修复线上bug，可能就比较麻烦了
3. 即使成功发布到了 Chrome 的插件市场，公司内某些部门也因为受网络限制无法访问插件市场，而没有办法安装插件😓

一般安装插件都是使用打包之后的`crx`文件。但是，由于目前 Chrome 浏览器的安全限制，所有插件必须得经过它的应用市场审核通过之后，用户才能进行安装。否则，即使安装到了浏览器上，也无法启用。

<img width="400" src="https://img10.360buyimg.com/imagetools/jfs/t1/154879/17/9846/154666/5fd81ff5Efa28d8fc/fcb6695690f4cd81.jpg">

没有经过 Chrome 官方审核通过的插件，只能让用户使用开发者模式安装源代码的形式进行安装。也就是说，我们开发完的插件不能打包成`crx`文件，只能将源代码发给用户进行安装。可是，后续插件有更新怎么办？难道还要将源代码一个个的发给相关用户，他们再重新进行安装？

### 改进发布方式

由于以上问题，在插件的发布方式上就必须做一些技术创新。否则，用户最终的使用体验将会非常差。目前采用的解决方案是：

> 用户在安装时只是安装了一个插件的『加载器』，插件的内部业务逻辑代码会发布到京东私服上，可以通过[unpkg.jd.com](http://unpkg.jd.com) 来进行访问。加载器再通过请求 unpkg 平台，将获取到的业务代码使用 script 标签塞到当前页面中完成渲染

主要流程如下图所示：

<img width="700" src="https://img11.360buyimg.com/imagetools/jfs/t1/142630/24/18463/257232/5fd827bdEbdce885a/f2a363053777da17.jpg">

使用这种方式，可以完美规避传统发布方式中的各种问题：

1. 前端使用`npm publish`进行发布，不需要任何审核，速度非常快
2. 发布到京东内部私服，保证业务代码的安全性
3. 因为是通过 unpkg 平台进行访问，没有网络限制，只要是内部员工均可访问

这里要注意的是，加载器请求的 unpkg 远程地址千万不要加版本号，这样 unpkg 每次才会自动重定向到当前模块最新的版本上。比如说，我这样请求一个 react 文件`http://unpkg.jd.com/react`，因为没有在请求路径上添加版本号，unpkg 默认就会把它重定向到`http://unpkg.jd.com/react@17.0.1/index.js`

可是，如果在请求地址中添加了版本号，unpkg 返回的就永远都是指定的固定版本号的文件了，而不是最新版本的文件

现在当用户安装完插件之后，会根据当前页面地址来判断是否需要进行渲染。因为目前插件只支持商城主站的四个业务页面：

```
search.jd.com
list.jd.com
item.jd.com
e.jd.com
```

如果是的话，就会从 unpkg 平台请求加载最新版本的业务代码，将其塞到页面中完成渲染。这样，理论上基本可以让用户只安装一次就可以了

## 搭建开发环境

### 主要技术栈

* 主要开发语言使用`React + TypeScript`
* 本地服务与打包使用`Webpack`。因为插件是『寄生』在其它页面上的，所以此工程没有任何页面，也就不需要配置`webpack-dev-server`。直接使用`npx webpack --watch`监听本地文件的变化进行热更新
* 此项目使用`EsBuild`将`TypeScript`编译为`Javascript`
* 同样也是使用`EsBuild`对代码文件进行压缩
* 最终编译包会发布到公司私服`npm.m.jd.com`上，然后使用`unpkg.jd.com`平台进行远程引用

### 插件配置文件

一个 Chrome 插件中必须要包含一个`manifest.json`配置文件，当前插件的所有配置也都必须写在这里，这里面有几个主要配置项需要说明一下：

1. `content_scripts`

指定插件在当前页面中的入口文件。也就是插件加载完成之后，需要执行的文件。我在这里面根据当前页面地址进行判断，看是否需要

2. `background_scripts`

指定可以在当前插件后台中运行的代码文件。这个后台是相对于前台页面的，类似于 js 中的 worker，但和它又不一样。后台文件中的`window`对象和前台中的`window`并不相同。在后台文件中请求数据时，可以不受跨域限制，这也是我能实现远程加载业务代码的关键所在，后面会详细介绍

Chrome 为后台文件也提供了一个开发工具，打开它的方式就是进入到浏览器的『扩展程序』，在当前插件中有一个『背景页』的按钮，点击它就能打开一个开发者工具，可以用来查看当前插件中后台的各种请求信息：

<img width="400" src="https://img14.360buyimg.com/imagetools/jfs/t1/130613/28/19895/115972/5fd85480E2943ac0f/766d8268954a1acc.jpg">

### 保存自动更新

我们在开发时，都希望每次更改代码保存之后，浏览器能够自动更新插件，刷新页面。我使用的插件是[crx-hotreload](https://github.com/xpl/crx-hotreload)。它的使用方式也非常简单：

![](https://img11.360buyimg.com/imagetools/jfs/t1/141194/3/15666/24154/5fbdb457E4c8c6e09/d02096b6b7682bc1.png)

1. 将目录中的`hot-reload.js`文件放到你自己的工程目录中
2. 在插件的`manifest.json`配置中添加：`"background": { "scripts": ["hot-reload.js"] }`

## 功能实现

### 资源请求问题

在打定初步方案之后，开始动手开发。马上就遇到了两个棘手的问题：

1. 资源请求安全问题

因为京东主站完全是基于 https 协议，而我们的慧定价系统是基于 http 协议。在一个 https 的页面中直接使用`fetch`请求 http 资源的话，浏览器会认为这是不安全的，它会被直接`block`掉，连请求发出去的机会都没有，下面是在请求时，浏览器抛出的异常：

```
VM1162:1 Mixed Content: The page at 'https://search.jd.com/Search?keyword=%E5%A4%A7%E5%AE%B6%E7%94%B5&enc=utf-8&suggest=1.his.0.0&wq=&pvid=d36930ed82724119bfbac5a92dbd5bf8' was loaded over HTTPS, but requested an insecure resource 'http://hdj.jd.com/'. 
This request has been blocked; the content must be served over HTTPS.
```

2. 跨域问题

假设慧定价升级到 https 协议，其实还是没办法直接拿到数据。因为此插件需要支持若干业务页面（上面已经提到过），每个页面的域名都不一样，直接在主站发起`fetch`请求依然会存在跨域的问题，导致拿不到接口数据

### 解决思路

我们暂且把在 html 页面中直接发起的请求称为：前台请求。

首先，在 Chrome 浏览器插件的`manifest.json`配置文件中，有两个重要的配置字段：

* `content_scripts`，`js`字段用来配置在插件加载之后，默认可以在当前页面上执行的 js 文件
* `background`：`scripts`字段用来配置在插件后台可以执行的 js 文件

于是，我就发现，在插件的后台 js 中文件发起`fetch`请求，是不受跨域和协议安全限制的！

```json
{
  "manifest_version": 2,
  "name": "慧定价比价插件",
  "version": "2.0",
  "content_scripts": [
    {
      "matches": ["http://*/*", "https://*/*"],
      "js": ["./content.js"]
    }
  ],
  "background": {
    "scripts": ["./background.js", "./asset/hot-reload.js"]
  },
}
```

在Chrome 插件体系中有`content_scripts`和`background_scripts`两种脚本。

它们分别运行在浏览器互相隔离的两个沙盒中。也就是说，`content_scripts`中指定的脚本是运行在当前你能看到的前台页面中，这些脚本可以像正常 js 一样，获取当前页面中所有信息。而`background_scripts`中指定的脚本运行在 Chrome 插件的一个后台环境中，这些脚本访问不到前台页面的任何信息。

并且两个运行环境中的`window`对象是不同的，不能互相访问。

那其实，`chrome.extension`对象提供了`sendRequest`方法，可以用来从`content_scripts`中发起请求。又提供了`onRequest.addListener`可以在`background_scripts`中来监听请求。  

> 注意：这里的请求并不是指的常规网络请求，而是在浏览器插件中`content_scripts`和`background_scripts`之间用来通信的一种方式。

`chrome.extension.sendRequest`的使用方式如下所示：

```ts
chrome.extension.sendRequest(options: any, callback: (res: any) => void)
```

* 它的第一个参数是一个自定义值，在发起请求之后，会传递给`background_scripts`中的`onRequest.addListener`监听器
* 它的第二个参数是一个自定义回调函数，用来接收从`onRequest.addListener`中的返回值

`chrome.extension.onRequest.addListener`的使用方式如下所示：

```ts
chrome.extension.onRequest.addListener(message: any, sender: any, sendResponse: (data: any) => void)
```

* 它的第一个参数就是从`sendRequest`中接收到的参数值
* 第二个参数就是当前发起请求的 tab 页信息
* 第三个参数是一个函数，用来发送数据给请求方

## 具体实现

### 基于上面的技术分析，在`content_scrips`中发起请求：

```ts
  chrome.extension.sendRequest(
    { title: 'getBundle', script: `http://unpkg.jd.com/@rmb/hdj-chrome-extension` },
    (res: any) => {
      const id = 'hdjBundleScript'
      const script = document.createElement('script')
      script.setAttribute('type', 'text/javascript')
      script.id = id

      if (isDevelopment) {
        script.src = chrome.extension.getURL('bundle.js')
      } else {
        script.text = res
      }

      if (!document.getElementById(id)) {
        document.head.appendChild(script)
      }
    },
  )
```

* `title: getBundle`用来告诉`onRequest`来加载远程的一个 bundle 包
* `script`用来告诉`onRequest`加载远程 bundle 的地址是什么。我把 bundle 发布到了自建的`unpkg.jd.com`cdn上了

拿到请求数据之后，创建一个`script`标签，将代码插入到页面时，还要区分两种环境：

在开发环境使用`chrome.extension.getURL`直接加载本地打包好的脚本，赋值给`script`的`src`属性即可：

```html
<script src="bundle.js"></script>
```

在生产环境中，拿到的是 bundle 源码，所以需要将它直接塞到`script`标签中：

```html
<script>
// bundle 代码
</script>
```

最后还需要判断一下，此标签在页面中是否已经存在，避免重复插入。

### 在`background_scripts`中响应请求：

```ts
chrome.extension.onRequest.addListener((message, sender, sendResponse) => {
  if (message.title === 'getPriceData') {
    fetch(message.api, message.options)
      .then((response) => {
        return response.json()
      })
      .then((data) => {
        sendResponse(data)
      })
  }

  if (message.title === 'getBundle') {
    fetch(message.script)
      .then((response) => {
        return response.text()
      })
      .then((data) => {
        sendResponse(data)
      })
  }
})
```

* 根据`title`参数来判断当前请求的数据类型
* 然后直接发起`fetch`请求，将拿到的数据转换成对应类型之后，直接使用`sendResponse`返回。`content_scripts`就可以拿到数据了。

## 适配 Firefox 浏览器

在网上搜索了一下相关资料，发现 Chrome 插件基本上是可以直接安装到 Firefox 浏览器上运行的。其实想想，它们之间互相兼容也是合理的，毕竟浏览器插件本质都是由 html、css 和 js 组成的。这里说是『基本』上，是因为如果你将其直接安装到 Firefox 上，有可能根本跑不起来。

开发过插件的同学都知道，在 Chrome 浏览器插件的运行环境中，有一个全局对象`chrome`。它里面封装了 Chrome 插件提供的相关工具函数。因为有些方法在 Firefox 中是不支持的，所以，直接安装到 Firefox 上就有可能跑不起来。那么，到底怎么知道哪些方法是不兼容的呢？

应该是 Firefox 官方，提供了一个插件代码检查工具，可以用于扫描出代码中那些不兼容的方法。你需要将 Chrome 插件在本地打包为一个`.ctx`文件包，然后在下面的工具网站中将其上传，此工具就会对其自动进行扫描：

![https://www.extensiontest.com/](https://img13.360buyimg.com/imagetools/jfs/t1/135832/25/17162/75258/5fbce483Ef448827c/0112cec44fde7296.png)

扫描完成，它会列出所有不兼容的代码，你需要根据情况进行改造即可。（现在发现，Firefox 为了繁荣自己的插件市场也真是煞费苦心😳）

我遇到一个问题，就是在 Chrome 插件中使用了`chrome.extension.onMessage`方法。经过检测，Firefox 认为它是一个已经被废弃的方法，坚决让我改成`chrome.runtime.onMessage`方法才可以。可是在改完之后，就会一直报下面的错误：

```
Error: 'Unchecked runtime.lastError: The message port closed before a response was received' chrome issue?
```

后来，经过苦苦搜索，才发现在`chrome.runtime.onMessage`的回调函数中，必须在最后返回一个`true`才可以（不理解，这样设计的目的是啥😓）

```js
chrome.runtime.onMessage.addListener((message, sender, sendResponse) => {
  //其它代码逻辑
  ...

  return true //这句很重要
})
```

我参考的是这篇帖子中的答案：https://www.edureka.co/community/65627/unchecked-runtime-lasterror-message-response-received-chrome



