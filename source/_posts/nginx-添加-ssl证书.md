---
title: Nginx 添加 ssl证书
categories:
  - oam
tags:
  - 运维
  - Nginx
abbrlink: 18457
date: 2019-12-18 23:19:06
---

简单的记一下在 `laradock` 中如何给 `Nginx` 添加 **ssl证书**

在站点配置文件原有的80端口的 `server` 中加入以下内容，其中注意需要修改的是**两个证书文件的地址**

```nginx
listen 443 ssl;
# 注意文件的地址
ssl_certificate /etc/nginx/ssl/3243201_yeek.top.pem;
ssl_certificate_key /etc/nginx/ssl/3243201_yeek.top.key;
ssl_session_timeout 5m;
ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
ssl_prefer_server_ciphers on;
```



![](http://markdown.yeek.top/20191218232943.png)