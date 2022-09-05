重启 nginx：

```
nginx -s reload
```

查看 nginx 配置信息：

```
nginx -V
```

`~`，表示匹配 URI 时是大小写敏感的  
`~*`，表示忽略 URI 大小写敏感  
`^~`，表示匹配 URI 时，只需要前半部分能够匹配即可，例如：
```
location ^~ /images/ {
  # 以 images 开头的请求都会被匹配上
}
```

添加一个虚拟服务器：

```nginx
http {
  server {
    listen 80; # 监听指定端口
    access_log logs/error.log; # 指定错误日志的位置
    
    location / {
      autoindex on; # 开启可查看目录
      root /Users/chenxiaochun/Documents/MyProject/test;
    }  
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

返回指定的状态码，`return`的第二个参数是可选的，可指定要跳转的 url：
```nginx
http {
  server {
    listen 80; # 监听指定端口
    access_log logs/error.log; # 指定错误日志的位置
    
    location / {
      autoindex on; # 开启可查看目录
      root /Users/chenxiaochun/Documents/MyProject/test;
      return 301 http://baidu.com;
    }  
  }
}
```
