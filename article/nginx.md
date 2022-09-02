重启 nginx：

```
nginx -s reload
```

查看 nginx 配置信息：

```
nginx -V
```

添加一个虚拟服务器：

```nginx
http {
  server {
    listen 80; # 监听指定端口
    server_name vhost1.com www.vhost1.com;
    index index.html index.html;
    root /Users/chenxiaochun/Documents/MyProject/test/vhost1;
  }
}
```

使用`sub_filter`替换指定的响应内容：

```nginx
http {
  server {
    listen 80;
    server_name vhost1.com www.vhost1.com;
    index index.html index.html;
    root /Users/chenxiaochun/Documents/MyProject/test/vhost1;
    sub_filter vhost cxc;
    sub_filter_once off;
  }
}
```
