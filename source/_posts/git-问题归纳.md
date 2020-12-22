---
title: git 问题归纳
categories:
  - oam
tags:
  - 笔记
  - 运维
  - Git
abbrlink: 19251
date: 2019-11-06 11:28:59
---



收集 `git` 使用中遇到的各种奇奇怪怪的问题以及解决方案，请善用 <kbd>Ctrl+F</kbd> 搜索报错信息来查找。



<!-- more -->



## git 基础

**git pull 冲突**

报错信息

```
error: Your local changes to the following files would be overwritten by merge
```



1. 保留本地的方式修改 

 通过git stash将工作区恢复到上次提交的内容，同时备份本地所做的修改，之后就可以正常git pull了，git pull完成后，执行git stash pop将之前本地做的修改应用到当前工作区。 

```
git stash
git pull
git stash pop
```



2. 远程强制覆盖本地

```
git fetch --all //只是下载代码到本地，不进行合并操作
git reset --hard origin/master  //把HEAD指向最新下载的版本
```





## 服务器上的 git



**裸仓库中不允许混合重置**

报错内容如下

```
mixed reset is not allowed in a bare repository
```

解决方案

回到上一次

```
git update-ref HEAD HEAD^
```

回到某一次

```
git update-ref refs/heads/branch-name branch-name^
```

使用 sha1

```
git update-ref refs/heads/branch-name a12d48e2
```



更多请参阅[git-update-ref](http://schacon.github.com/git/git-update-ref.html)命令的文档。 或者腾讯云的[中文文档](https://cloud.tencent.com/developer/section/1138785)

方案来源地址 [栈越](https://stackoverflow.com/questions/4624881/how-can-i-uncommit-the-last-commit-in-a-git-bare-repository)

