---
title: nginx 强制转https
categories:
  - oam
tags:
  - 运维
date: 2020-03-14 22:02:31
---



有了 `SSL` 证书，就要好好的利用。

<!-- more -->

将 `nginx` 站点配置中原来监听的 80 端口拎出来，用来重定向到 `https`。

注释掉原先的 80 端口的监听

```nginx
server {
    # listen 80 default_server;
    # listen [::]:80 default_server;
    
    listen 443 ssl ;
    listen [::]:443 ssl  ipv6only=on;
    
    server_name *.example.top;
    // 其他配置略
}
```

另起一个 80 端口监听用于重定向

```nginx
 server{
    listen  80 default_server;
    listen [::]:80  ipv6only=on;

    server_name *.example.top;
    return 301 https://$server_name$request_uri;
}
```

检测配置，重新加载

```bash
nginx -t
nginx -s reload
```

有**多个站点**的话就弄多个监听 80 端口的 `server`

```nginx
 server{
    listen  80 default_server;
    listen [::]:80  ipv6only=on;
    server_name www.example.top example.top *.example.top;
    return  301 https://$server_name$request_uri;
}

server{
    listen  80 ;
    listen [::]:80  ;
    server_name www.example.com example.com *.example.com;
    return  301 https://$server_name$request_uri;
}
```

