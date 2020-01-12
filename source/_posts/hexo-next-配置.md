---
title: hexo-next-配置
categories:
  - blog
tags:
  - 博客
  - hexo
  - next
abbrlink: 32411
date: 2020-01-12 15:33:01
---



简单的介绍一下我是如何配置本博客的

<!-- more -->

# 博客设置

是{% label danger@根目录 %}下的 `_config.yml` 文件



## 站点信息

```yml
title: moreant's Blog
subtitle: 学习即输入输出
description: 别教了，我学不动了
keywords: 前端 后端 运维
author: 莫居尘 Moreant
language: zh-CN
timezone: "Asia/Shanghai"
```



## 永久链接

参见 [URL的美化](/post/52295.html)



## 上传至 GitHub

```yml
deploy: 
  type: git
  repo: git@github.com:moreant/moreant.github.io.git
  branch: master
```





# 主题设置

我使用的是 [next](https://github.com/theme-next/hexo-theme-next) 主题，注意别下载到旧版了。

详细配置里面的注释很详细，请善用 Google 翻译

