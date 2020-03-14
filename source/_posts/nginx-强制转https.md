---
title: nginx 强制转https
categories:
  - oam
tags:
  - 运维
date: 2020-03-14 22:02:31
---



有了 `SSL` 证书，就要好好的利用。将 `nginx` 站点配置中原来监听的 80 端口拎出来，用来重定向到 `https`

注释掉 80 端口的监听

```nginx
server {
    # listen 80 default_server;
    # listen [::]:80 default_server;
    
    server_name *.example.top;
    root /var/www/public;
    index index.html index.htm;
    // 其他配置略
}
```

另起一个 80 端口监听用于重定向

```
 server{
    listen  80 default_server;
    listen [::]:80  ipv6only=on;

    server_name *.*.*;
    rewrite ^(.*)$  https://$host$1 permanent;
}
```

检测配置，重新加载

```
nginx -t
nginx -s reload
```

