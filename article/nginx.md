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
    root /www/data;
    
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
    root /www/data;
    
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
    root /www/data;
    
    location / {
      try_files $uri $uri/ @backend;
    }
    
    location @backend {
      proxy_pass http://backend.example.com;
    }
  }
}
```

### `proxy_pass`指令

将指定的请求传递到指定的地址上：

```nginx
http {
  server {
    location /some/path/ {
      proxy_pass http://www.example.com/link/;
    }
  }
}
```

请求上游服务器时，需要添加额外的请求头，使用`proxy_set_header`：

```nginx
http {
  server {
    location /some/path/ {
      proxy_set_header X-Real-IP $remote_addr;
      proxy_pass http://www.example.com/link/;
    }
  }
}
```

### `add_header`指令

给浏览器返回一些头信息：

```nginx
http {
  server {
    location /some/path/ {
      add_header X-Real-IP $remote_addr;
      proxy_pass http://www.example.com/link/;
    }
  }
}
```

### 压缩和解压缩

启用压缩：

```nginx
gzip on
```

默认情况下，仅会对`text/html`的 mime 类型进行压缩。如果想支持其它 mime 类型，需要设置：

```nginx
gzip_types text/plain application/xml;
```

指定要压缩的响应的最小长度，默认为 20 字节。例如，修改为 1000 字节：

```nginx
gzip_min_length 1000;
```

默认情况下，nginx 不会对代理服务器的响应进行压缩（由响应头中的 via 字段来确定是否为代理服务器）。如果想对其压缩，需要使用`gzip_proxied`指令进行配置。
它支持多个参数，用于告诉 nginx 来压缩哪种代理请求：

```nginx
gzip_proxied no-cache no-store private expired auth;
```

发送压缩文件。例如针对`/file`请求，nginx 会尝试发送`/file.gz`文件。如果文件不存在，或者客户端不支持 gzip，则 nginx 将发送未压缩的文件：

```nginx
location / {
  gzip_static on;
}
```

如果客户端不支持 gzip，需要进行解压缩：

```nginx
gunzip on;
```

### 启用响应缓存

在顶层的 http 上下文中添加`proxy_cache_path`指令。第一个参数指定缓存内容的本地系统路径。`keys_zone`用来指定元数据的共享内存名称和大小：

```nginx
http {
  proxy_cache_path /data/nginx/cache keys_zone=one:10m;
}
```

然后在要缓存的 server 上下文中使用`proxy_cache`指令指定要缓存的区域名称：

```nginx
http {
    ...
    proxy_cache_path /data/nginx/cache keys_zone=one:10m;

    server {
        proxy_cache one;
        location / {
            proxy_pass http://localhost:8000;
        }
    }
}
```

### 正向代理

正向代理，指的是一个位于客户端和原始服务器之间的服务器。为了从原始服务器获取内容，客户端向代理服务器发送一个请求并指定目标（原始服务器），然后由代理服务器向原始服务器转发请求，并将获得的内容返回给客户端

```
（国内外）目前比较主流的DNS：
（国外）谷歌：8.8.8.8 developers.google.com
（国外）OpenDNS：208.67.222.222 signup.opendns.com
（国内）114：114.114.114.114 www.114dns.com
（国内）腾讯：119.29.29.29 www.dnspod.cn
（国内）阿里：223.5.5.5 alidns.com
（国内）百度：180.76.76.76 dudns.baidu.com
```

下面示例是对百度做一个正向代理：

```nginx
http {
  server {
    resolver 180.76.76.76;
    listen 80;
    access_log logs/error.log;

    location / {
        proxy_pass                 http://$host$request_uri;
        proxy_set_header           HOST $host;
        proxy_buffers              256 4k;
        proxy_max_temp_file_size   0k;
        proxy_connect_timeout      30;
        proxy_send_timeout         60;
        proxy_read_timeout         60;
        proxy_next_upstream error  timeout invalid_header http_502;
    }
  }
}
```

测试是否代理成功：

```
curl http://www.baidu.com/ -v -x 127.0.0.1:80
```

### 相关资源

* https://www.yiibai.com/nginx/nginx-feature.html
* https://bbs.huaweicloud.com/blogs/301714
