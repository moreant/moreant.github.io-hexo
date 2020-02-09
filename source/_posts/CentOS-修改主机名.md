---
title: CentOS 修改主机名
categories:
  - oam
tags:
  - 运维
  - Linux
abbrlink: 65134
date: 2019-12-17 22:30:12
---



`CentOS` 与其他 `Linux` 有些许不同，可以直接使用下面的指令设置主机名

```
hostnamectl set-hostname 主机名
```

但是传入的 hostname 中的大写字母会自动转化成小写字母，可以接着用下面这个指令来另外设置

```
hostnamectl --static set-hostname 主机名
```

一旦修改了静态主机名，`/etc/hostname` 将被自动更新。

然而，`/etc/hosts` 不会更新以保存所做的修改，所以你每次在修改主机名后一定要手动更新`/etc/hosts`，之后再**重启CentOS 7**。否则系统再启动时会很慢。

> 参考资料：https://www.jianshu.com/p/39d7000dfa47