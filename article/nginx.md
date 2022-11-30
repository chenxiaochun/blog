重启 nginx：

```
nginx -s reload
```

查看 nginx 配置信息：

```
nginx -V
```

## 标识符

* `~`，表示匹配 URI 时是大小写敏感的  
* `~*`，表示忽略 URI 大小写敏感  
* `^~`，表示匹配 URI 时，只需要前半部分能够匹配即可，例如：
```nginx
location ^~ /images/ {
  # 以 images 开头的请求都会被匹配上
}
```


## 虚拟服务器

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

使用`sub_filter`替换指定的响应内容。例如，将响应内容中的所有『vhost』文本替换成『cxc』：

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

## `try_files`指令

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

## `proxy_pass`指令

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

### `location`截取代理路径

主要有两种逻辑：

一是`proxy_pass`后面加`/`的时候：

当我们访问`http://44.179.118.54:80/shop/xxx`的时候，nginx 会把`/shop/`后面的部分直接拼接到`proxy_pass`上。那我们实际访问的是`http://44.179.118.54:8007/xxx`

```nginx
location ^~ /shop/ {
  proxy_pass  http://44.179.118.54:8007/;
}
```

上面的配置等同于：

```nginx
location ~ ^/addrdata/(.*) {
  proxy_pass  http://44.179.118.54:8007/$1$is_args$args;
}
```

二是`proxy_pass`后面不加`/`的时候，实际就是一层普通的代理，不会对地址做任何修改：

当访问`http://44.179.118.54:80/shop/xxx`，实际访问的就是`http://44.179.118.54:8007/shop/xxx`

```nginx
location ^~ /shop/ {
  proxy_pass  http://44.179.118.54:8007;
}
```

## `add_header`指令

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

## 压缩和解压缩

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

## 启用响应缓存

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

## 正向代理

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

## 内置变量参数

参数名 | 意义
---|---
`$geo` | 获取客户端的 ip 地址  
`$arg_PARAMETER` | http 请求中某个参数的值，例如`/index.html?size＝100`，可以用`$arg_size`取得 100 这个值
`$args` | http 请求中的完整参数。例如，在请求`/index.html?_w=120&_h=120`中，`$args`表示字符串`_w=120&_h=120`
`$binary_remote_addr` | 二进制格式的客户端地址。例如`\x0A\xEOB\xOE`
`$body_bytes_sent` | 表示在向客户端发送的 http 响应中，包体部分的字节数
`$content_length` | 表示客户端请求头部中的 Content-Length 字段
`$content_type` | 表示客户端请求头部中的 Content-Type 字段
`$cookie_COOKIE` | 表示在客户端请求头部中的 cookie 字段
`$document_root` | 表示当前请求所使用的 root 配置项的值
`$uri` | 表示当前请求的 URI，不带任何参数
`$document_uri` | 与`$uri`含义相同
`$request_uri` | 表示客户端发来的原始请求 uri，带完整的参数。`$uri`和`$document_uri`未必是用户的原始请求，在内部重定向后可能是重定向后的 URI，而`$request_uri`永远不会改变，始终是客户端的原始 uri
`$host` | 表示客户端请求头部中的 Host 字段。如果 Host 字段不存在，则以实际处理的 server（虚拟主机）名称代替。如果 Host 字段中带有端口，如`IP：PORT`，那么`$host`是去掉端口的，它的值为 IP。`$host`是全小写的。这些特性与 http_HEADER 中的 http_host 不同， http_host 只是『忠实』地取出 Host 头部对应的值
`$hostname` | 表示 Nginx 所在机器的名称，与 gethostbyname 调用返回的值相同
`$http_HEADER` | 表示当前 HTTP 请求中相应头部的值。HEADER 名称全小写。例如，用`$http_host`表示请求中 Host 头部对应的值
`$sent_http_HEADER` | 表示返回客户端的 HTTP 响应中相应头部的值。HEADER 名称全小写。例如，用`$sent_http_content_type`表示响应中 Content-Type 头部对应的值
`$is_args` | 表示请求中的URI是否带参数，如果带参数，`$is_args`值为`?`，如果不带参数，则是空字符串
`$limit_rate` | 表示当前连接的限速是多少，0 表示无限速
`$nginx_version` | 表示当前Nginx的版本号，如`1.0.14`
`$query_string` | 请求 URI 中的参数，与`$args`相同，然而`$query_string`是只读的不会改变
`$remote_addr` | 表示客户端的地址
`$remote_port` | 表示客户端连接使用的端口
`$remote_user` | 表示使用 Auth Basic Module 时定义的用户名
`$request_filename` | 表示用户请求中的 URI 经过 root 或 alias 转换后的文件路径
`$request_body` | 表示 HTTP 请求中的包体，该参数只在`proxy_pass`或`fastcgi_pass`中有意义
`$request_body_file` | 表示 HTTP 请求中的包体存储的临时文件名
`$request_completion` | 当请求已经全部完成时，其值为`ok`。若没有完成，就要返回客户端，则其值为空字符串；或者在断点续传等情况下使用HTTP range 访问的并不是文件的最后一块，那么其值也是空字符串
`$request_method` | 表示 HTTP 请求的方法名，如 GET、PUT、POST 等
`$scheme` | 表示 HTTP scheme，如在请求 https://nginx.com/ 中表示 https
`$server_addr` | 表示服务器地址
`$server_name` | 表示服务器名称
`$server_port` | 表示服务器端口
`$server_protocol` | 表示服务器向客户端发送响应的协议，如 HTTP／1.1 或 HTTP／1.0

## 相关资源

* https://www.yiibai.com/nginx/nginx-feature.html
* https://bbs.huaweicloud.com/blogs/301714
