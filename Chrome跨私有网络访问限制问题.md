## CORS for private networks

Chrome 升级 94 版本之后，访问某些网站，有可能会发生以下错误：

```
p.hdj.jd.com/:1 Access to script at 'http://unpkg.jd.com/@rmb/events@0.2.0/dist/index.js' from origin 'http://p.hdj.jd.com' has been blocked by 
CORS policy: The request client is not a secure context and the resource is in more-private address space `private`.
```

预发环境：

```
Request URL: http://p.hdj.jd.com/
Request Method: GET
Status Code: 200 OK
Remote Address: 11.20.12.110:80
Referrer Policy: strict-origin-when-cross-origin
```

开发环境：

```
Request URL: http://d.hdj.jd.com/
Request Method: GET
Status Code: 200 OK
Remote Address: 10.170.162.125:80
Referrer Policy: strict-origin-when-cross-origin
```

unpkg 服务：

```
Request URL: http://unpkg.jd.com/
Request Method: GET
Status Code: 200 OK
Remote Address: 172.18.227.27:80
Referrer Policy: strict-origin-when-cross-origi
```

私有 IP 地址定义：

* Range from 10.0.0.0 to 10.255.255.255 — a 10.0.0.0 network with a 255.0.0.0 or /8 (an 8-bit) mask
* Range from 172.16.0.0 to 172.31.255.255 — a 172.16.0.0 network with a 255.240.0.0 or /12
* A 192.168.0.0 to 192.168.255.255 range, which is a 192.168.0.0 network masked by 255.255.0.0 or /16
* A special range 100.64.0.0 to 100.127.255.255 with a 255.192.0.0 or /10 network mask; this subnet is recommended according to rfc6598 for use as an address pool for CGN (Carrier-Grade NAT)

会影响的三种请求类型：

* Requests from the public network to a private network
* Requests from a private network to a local network
* Requests from the public network to a local network

![](https://web-dev.imgix.net/image/admin/kYpJXAxP6a3hphO4uzZX.png?auto=format&w=845)

解决办法：

1. 修改 chrome 配置，只适用于本地调试临时修改

```
chrome://flags/#block-insecure-private-network-requests
```

2. 所有将访问的远程资源服务 IP 地址都改成公有地址

```
unpkg.jd.com
rmbcdn.jd.com
```

3. 使用 nginx 代理

```
location ~ ^/package/(?<section>.+)$ {
  proxy_set_header Host unpkg.jd.com;
  proxy_pass http://172.18.227.27/$section;
}
```

4. 把服务都升级成 https 应该也是可以的，我没有试过

5. 在未来，给所有远程服务添加响应头。但不知道这个未来指的是什么时候？

在将来，当公共网络试图请求私有网络或者本地网络的资源时，chrome 都会先发一个包含`Access-Control-Request-Private-Network: true`请求头的预请求。而服务端如果允许其进行请求，返回的响应中需要包含`Access-Control-Allow-Private-Network: true`

> These headers are still under development and may change in the future. No action is currently required.

为什么会添加这个机制？

这是因为在 2014 年发生了一起叫做[Drive-By Pharming](https://link.springer.com/chapter/10.1007/978-3-540-77048-0_38)的钓鱼事件，导致 30000 多无线路由器被攻击中招，路由器的 DNS 被恶意篡改

### 参考资料

* https://stackoverflow.com/questions/66534759/chrome-cors-error-on-request-to-localhost-dev-server-from-remote-site
* https://help.keenetic.com/hc/en-us/articles/213965789-What-is-the-difference-between-a-public-and-private-IP-address-
* https://web.dev/cors-rfc1918-feedback/


