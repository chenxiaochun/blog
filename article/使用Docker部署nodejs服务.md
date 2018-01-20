![](http://img10.360buyimg.com/uba/jfs/t5353/211/2657755713/59030/370f3e8f/591cf098N7a73ed27.png)

开始之前，我假设你的电脑上已经安装了Docker，如果没有安装过，请自行搜索相应的安装方式。

### 一、创建容器

首先打开[Docker Store](https://store.docker.com/)，在首页的搜索框抛入`node`寻找我们需要的合适镜像。

![](https://camo.githubusercontent.com/c3000f5e7cfa13859af7c99606f7d2ef6d3457e0/687474703a2f2f696d6733302e333630627579696d672e636f6d2f7562612f6a66732f74353239392f3330322f323532383439323831362f3130393331392f64353130343335622f35393162613335364e32363163613434612e706e67)

点击进去之后，看到右上角的拉取镜像命令，复制它到你的命令行终端中执行一下，即可将当前镜像安装到你的设备上。
```
docker pull node
```

接下来利用此镜像创建一个新容器
```
docker run -idt -p 8080:8888 node /bin/bash
```
* `docker run`，创建一个新容器
* `-i`，让Docker的标准输入保持打开，允许用户与容器进行交互
* `-d`，在后台运行Docker
* `-t`，让Docker分配一个伪终端（pseudo-tty）并绑定到容器的标准输入上
* `8080`，我们一会儿要在nodejs服务中使用的端口
* `8888`，Docker用来暴露给外部访问的端口
* `bin/bash`，启动Docker的bash终端

不出意外的话，现在你已经成功的启动了一个Docker，运行此命令可以看到当前正在运行的所有Docker
```
docker ps
```

### 二、编写nodejs应用
假设我们打算使用`express`来启动一个静态服务器，用来输出一行文本`Hello World`。
首先执行`npm init`初始化一个`package.json`文件，然后执行`npm install express --save`，安装`express`依赖包
```json
{
  "name": "test",
  "version": "1.0.0",
  "description": "",
  "main": "server.js",
  "scripts": {
     "start": "node server.js"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "express": "^4.15.2"
  }
}
```
执行`vi service.js`，编写nodejs代码：
```js
var express = require('express');
var PORT = 8080;
var app = express();
app.get('/', function (req, res) {
    res.send('Hello world');
});
app.listen(PORT);
console.log('Running on http://localhost:' + PORT);
```
注意：此处的`PORT`要与上面我们创建Docker时用的`8080`端口保持一致。
编写完毕，可以先在本机执行`node service.js`测试一下，看服务是否正常。

### 三、将nodejs应用复制到Docker里

执行`docker ps`命令，查看Docker的`CONTAINER ID`
```
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                    NAMES
cc53e1cefcf5        node                "/bin/bash"         44 minutes ago      Up 44 minutes       0.0.0.0:8080->8888/tcp   grave_jang
````
执行以下命令就可以将当前目录的文件复制到此`cc53e1cefcf5`Docker的`/var/node`目录中
```
docker cp ./ cc53e1cefcf5:/var/node
```
为了验证一下，我们可以登录Docker，然后执行`ls`命令查看是否真的复制成功了
```
docker attach cc53e1cefcf5
ls /var/node
```
但是，此`docker cp`命令在`Docker@1.8.0`以下版本貌似不支持，一直报错误提示：
```
Error: Path not specified
```
解决办法有两个，一是升级你的Docker版本，再一个就是在创建Docker的时候加上`-v`参数，它可以直接把你当前设备上指定的目录复制到指定的Docker目录中。例如下面这条命令就是在创建一个Docker的同时，把我当前设备上的`/var/data`目录文件直接复制到了Docker中的`/home/data`目录
```
docker run -v /var/data:/home/data -i -t node /bin/bash
```
最后在Docker里执行`node server.js`启动服务，然后在浏览器中访问`http://localhost:8080`，就可以看到`Hello World`了

### 四、参考链接
* http://stackoverflow.com/questions/39397831/docker-cp-error-path-not-specified
