---
title: 解决Window s Treminal 中 git bash 中文转义/乱码
categories:
  - tool
tags:
  - 工具
abbrlink: 60086
date: 2020-08-04 00:05:17
---



![](http://markdown.yeek.top/20200804002829.png)

在 git 安装目录下的 `etc/bash.bashrc` 文件中末尾添上这两行，保存重启 bash 即可

```bash
export LANG="zh_CN.UTF-8"
export LC_ALL="zh_CN.UTF-8"
```

 

![](http://markdown.yeek.top/20200804003011.png)

这种在 status 里中文转义的输入以下指令并执行即可

```bash
git config --global core.quotepath false
```

