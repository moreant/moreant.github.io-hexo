---
title: 解决 IDEA 控制台乱码
categories:
  - tool
tags:
  - 后端
  - 工具
abbrlink: 62768
date: 2020-07-29 00:52:34
---

百度到的办法基本都试过了，只有一个有用。

在 `Help -> Edit Custom VM Options...` 中的最后一行加上

```
-Dfile.encoding=UTF-8
```

接着重启 IDEA 就 ok 了