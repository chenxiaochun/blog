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

`$geo`，获取客户端的 ip 地址  


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

### `try_files`指令

用来检查与 uri 相同的文件是否存在。如果没有，则返回一个默认图片：

```nginx
http {
  server {
    root /www/data
    
    location /images/ {
      try_files $uri /images/default.gif;
    }
  }
}
```

用来检查与 uri 相同的文件是否存在。如果都没有，则返回指定的状态码：

```nginx
http {
  server {
    root /www/data
    
    location /images/ {
      try_files $uri $uri/ $uri.html =404;
    }
  }
}
```

如果尾部带有斜线的 uri 不能匹配到现有的文件和目录，则将请求重定向到`@backend`：

```nginx
http {
  server {
    root /www/data
    
    location / {
      try_files $uri $uri/ @backend;
    }
    
    location @backend {
      proxy_pass http://backend.example.com;
    }
  }
}
```

### 相关资源

* https://www.yiibai.com/nginx/nginx-feature.html
