部门里进行日常开发时，经常需要在移动设备上访问测试服务器上的文件目录。但是，测试服务器返回的默认目录页面在移动设备上没有做任何适配，导致每次想要精确点击一个目录链接时，需要不断的放大或者缩小页面，用起来略麻烦，相信各位能想象出来我说的意思。

于是，我就想能不能在nginx上做些配置，让它能对返回的目录页面加上这个`meta`标签进行自动适配呢。
```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=0">
```

我们用`ngx_http_sub_module`来解决这个问题，它是`nginx`的一个过滤器模块，可以对服务器响应进行字符串替换。但是没有默认内置在`nginx`中，所以我们需要重新编译源码安装`nginx`，并且指定`--with-http_sub_module`参数。

```vim
wget http://nginx.org/download/nginx-1.10.3.tar.gz
tar -xzvf nginx-1.10.3.tar.gz
cd nginx-1.10.3 --prefix=/usr/local/nginx-1.10.3 --with-http_sub_module
make
make install
```
安装完毕之后，打开`nginx.conf`配置文件，一般是在`/usr/local/nginx/conf`目录下面，添加如下配置：
```vim
local / {
    sub_filter '<head>' '<head><meta name="viewport" content="width=device-width, initial-scale=1.0"/>';
}
```
看起来语法很简单吧，使用这个模块的`sub_filter`指令把html中的`<head>`标签替换成了后面指定的标签，保存退出。然后重启`nginx`，然后访问页面就能看到修改了。
```vim
nginx -s reload
```
此模块还包含若干其它指令：
* `sub_filter_last_modified on | off;`：启用之后，在进行替换时会保留原始响应中`Last-Modified`的值不变，也就使得当前资源在浏览器中的缓存不会改变。
* `sub_filter_once on | off;`：这个应该很好理解，表示是否只替换一次。
* `sub_filter_types mime-type ...`：指定用来替换哪些`mime-type`，如果指定`*`就是替换任何一种`mime-type`。

参考链接：http://nginx.org/en/docs/http/ngx_http_sub_module.html

