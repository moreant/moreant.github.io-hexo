---
title: 解决 GitHub 头像加载错误
categories:
  - mix
tags:
  - GitHub
abbrlink: 13173
date: 2019-12-17 23:52:35
---

update in 1-17：

现在有更好的解决办法了，那就是 [SwitchyOmega](chrome-extension://padekgcemlokbadohgkifijomclgjgif/options.html#!/about)

<hr>

最近发现 GitHub 的头像不能正常加载了，发现是404，直接就想到了是**DNS污染**。挂上全局酸酸乳，`ping` 加载失败的域名，得到 `ip` 地址，然后在 `host` 里加上就行了。

<!-- MORE -->



```
# GitHub Start 
151.101.108.133 avatars1.githubusercontent.com
# GitHub End
```



![](https://markdown.yeek.top/20191217235619.png)



![](https://markdown.yeek.top/20191217235918.png)



![](https://markdown.yeek.top/20191218000029.png)