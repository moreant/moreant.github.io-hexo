---
title: Docker 部署 LNMP 和 Laravel
categories:
  - oam
tags:
  - 运维
  - Docker
  - PHP
  - Laravel
abbrlink: 20398
date: 2019-10-15 21:37:26
---

本文使用 `laradock` 这个轮子去用 `Docker` 搭建 `LNMP` + `Laravel` 环境。

本文需要对 `Linux` 、 `Laravel` 有些许了解

环境是 阿里云的 `CentOS7.6` ，如果是云服务器，记得在防火墙里开启 22 、 80 、 3306 等端口

<!--more-->



## Q&A

**Q**: 为什么选择 `Docker`

**A**: docker 可以方便的部署 `LNMP` / `LAMP` 环境，只需要列出清单或者使用别人分享的配置就可以一键部署环境。

**Q**: 为什么不使用 `Apache` 提供 web 服务

**A**: `Nginx` 是大势所趋，或者说我想多学一个



## Docker

{% note info %}

建议直接参阅 **菜鸟教程** 的 [CentOS Docker 安装](https://www.runoob.com/docker/centos-docker-install.html) ，非常的简洁易懂

{% endnote %}

### 安装

安装支持工具

```shell
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```

添加阿里源

```shell
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

更新 yum 缓存

```shell
sudo yum makecache fast
```

安装 Docker

```shell
sudo yum -y install docker-ce
```

启动 Docker

```shell
sudo systemctl start docker
```

### 镜像加速

编辑（没有就新建） */etc/docker/daemon.json* ，加入

```json
{
  "registry-mirrors": ["http://hub-mirror.c.163.com"]
}
```

> [阿里云加速](https://promotion.aliyun.com/ntms/act/kubernetes.html)



## Laradock

{% note info %}

建议结合 laradock 的 [文档](https://laradock.io/) / [中文文档](https://laradock.linganmin.cn/) 进行阅读，预先了解 laradock 中关于 docker-compose 的相关指令

{% endnote %}

### 安装

安装 `Git` ，这里第一步是更新 Git 的源，因为 CentOS 默认的 Git 版本过低。安装过程一路 `y` 即可

```bash
yum install http://opensource.wandisco.com/centos/7/git/x86_64/wandisco-git-release-7-2.noarch.rpm
yum install git
```

从 `GitHub` 上拉取 `laradock`

```bash
git clone https://github.com/laradock/laradock.git /usr/local/laradock
```

我在这里指定下载到 */usr/local/laradock*

我打算将 web 服务放在 */web* ，因此需要将 `laradock` 复制到 */web* 中

在根目录创建 web 目录

```shell
mkdir /web
```

复制 `laradock` 到 */web/lardock*

```shell
cp -r /usr/local/laradock/ /web/laradock
```

进入复制好的 *laradock/*

```shell
cd /web/laradock
```

### 修改配置

复制 `.env` 文件

```shell
cp env-example .env
```

需要修改 .env 文件来**个性化安装**容器，下面是本文涉及到的配置

| 变量名                            | 作用             |
| :-------------------------------- | :--------------- |
| APP_CODE_PATH_HOST                | 项目位置         |
| CHANGE_SOURCE                     | 国内环境镜像加速 |
| WORKSPACE_NPM_REGISTRY            | NPM 源           |
| WORKSPACE_COMPOSER_REPO_PACKAGIST | COMPOSER 源      |
| WORKSPACE_INSTALL_XDEBUG          | Xdebug扩展       |
| PHP_FPM_INSTALL_XDEBUG            | Xdebug扩展       |

#### 项目位置

我的 `laravel` 项目想命名为 `yeek` 所以

```
APP_CODE_PATH_HOST=../
```

所以也要修改 `nginx` 的站点设置。在 **laradock/nginx/sites/**

先备份原先的 `default.conf`

```shell
mv default.conf default.conf.example
```

`laradock` 给我们准备了一个 laravel 站点配置的示例文件，直接拿来用就行了

```shell
mv laravel.conf.example default.conf
```

然后将 `root` 后面路径里的 `laravel` 改为  `yeek`



#### 换源

使用国内源

```
CHANGE_SOURCE=true
```

`Composer` 使用阿里源

```
WORKSPACE_COMPOSER_REPO_PACKAGIST=https://mirrors.aliyun.com/composer/
```

NPM 使用淘宝源

```
WORKSPACE_NPM_REGISTRY=https://registry.npm.taobao.org
```

#### MySQL版本

{% note danger %}

`MySQL` 建议使用 5.7 或者其他低版本，否则密码可能不兼容远程连接软件，

{% endnote %}

```
MYSQL_VERSION=5.7
```

MySQL项里还有其他配置，按需设置

```dockerfile
### MYSQL #################################################

MYSQL_VERSION=5.7
MYSQL_DATABASE=default
MYSQL_USER=default
MYSQL_PASSWORD=secret
MYSQL_PORT=3306
MYSQL_ROOT_PASSWORD=root
MYSQL_ENTRYPOINT_INITDB=./mysql/docker-entrypoint-initdb.d
```

#### 开启 Xdebug

```
WORKSPACE_INSTALL_XDEBUG=true
PHP_FPM_INSTALL_XDEBUG=true
```

#### 其他

`YARN` 的安装是在是太慢了，而且暂时用不上，所以关掉

```dockerfile
WORKSPACE_INSTALL_YARN=false
```

### Docker Compose

laradock需要 `docker-compose` 的支持，因此安装 Docker Compose

> 可以去官网看最新版本的下载链接：[在Linux系统上安装Compose](https://docs.docker.com/compose/install/)

```shell
sudo curl -L "https://github.com/docker/compose/releases/download/1.25.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

安装过程挺漫长的，漫长到我在第二次安装的时候，实在顶不住试了这个方法

> 编辑 `/etc/ssh/ssh_config`，找到 `GSSAPIAuthentication no` 这行，删掉前面的注释，然后保存退出
>
> –[解决阿里云ECS服务器 git clone 速度慢](https://blog.csdn.net/zwkkkk1/article/details/94476963)



然后写入权限

```shell
sudo chmod +x /usr/local/bin/docker-compose
```



### 安装 Nginx MySQL 容器

在 `laradock` 这个目录下启动 Nginx 和 MySQL 容器

```
docker-compose up -d nginx mysql
```

注意：

1. `docker-compose` 指令只能在有 `docker-compose.yml` 文件的目录中执行
2. 第一次安装时间比安装 `Docker Compose` 还要漫长
3. 期间看到红色的不要慌张，都是正常情况
4. 这里有特别多的[坑](https://mojuchen.github.io/post/16264.html#天坑)

[![红色不一定是错误](http://markdown.yeek.top/bolg/20191016/6BFgQCKfGjU7.jpg?imageslim)](http://markdown.yeek.top/bolg/20191016/6BFgQCKfGjU7.jpg?imageslim)

### 安装完成

看到这几个容器都 `done` 则说明 `Nginx` 和 `MySQL` 安装完成了

![](http://markdown.yeek.top/20191218022119.png)

此时访问服务器，返回的应该是 `404 Not Found nginx`

[![404](http://markdown.yeek.top/bolg/20191016/t69uSXfnsrXV.jpg?imageslim)](http://markdown.yeek.top/bolg/20191016/t69uSXfnsrXV.jpg?imageslim)

在 **/web/yeek/public** 下新建一个 `index.php` ，内容是

```php
<?php
    phpinfo();
```

新建完成后刷新页面，出现了 `phpinfo` ，可以检查安装的扩展。

然后删掉 **yeek** 目录，准备 `Laravel` 的安装。



## Laravel

### 安装

在之前的 *laradock* 目录进入 **工作区**

```
docker-compose exec workspace bash
```

使用 `Composer` 安装 `Laravel`

这里指定了 `Laravel` 的项目名是 `laravel` 版本是 `5.7.*` , 网站的文件夹是 `yeek`

```
composer create-project --prefer-dist laravel/laravel yeek 5.7.*
```

### 权限

安装完成后，回到服务器，给予缓存和日志权限

```shell
# 要记得先退出 workspace 容器
cd /web/yeek
sudo chmod -R 777 storage bootstrap/cache
```

访问服务器，页面出现 Laravel 的 **欢迎页面**

[![恭喜你安装成功](http://markdown.yeek.top/bolg/20191016/vvWgBqPpcBUw.png?imageslim)](http://markdown.yeek.top/bolg/20191016/vvWgBqPpcBUw.png?imageslim)

### 数据库

在 docker 容器中，容器间的连接需要使用 **容器名** 而不是ip 地址，因此在 `.env` 中需要将

```
DB_HOST=127.0.0.1
```

改为

```
DB_HOST=mysql
```

## 天坑

### 操作系统 / Docker 容器

**端口占用**

阿里云的 `CentOS` 可能会自带 `Apache(httpd)` / `MySQL` ，他们会占用 `80` / `3306` 端口，建议安装容器前先检查一下

**修改配置不生效**

需要重构

```
docker-compose build {container-name}
```

**安装容器慢**

超过5分钟建议 Ctrl+C 终止，然后重建

```dockerfile
docker-compose build {container-name}
```

### Laravel

**内存不足**

安装 `Laravel` 时出现

```
proc_open(): fork failed - Cannot allocate memory
```

使用添加内存三连：

```shell
dd if=/dev/zero of=/var/swap.1 bs=1M count=1024
mkswap /var/swap.1
swapon /var/swap.1
```

> 参考: [添加内存](https://segmentfault.com/a/1190000019193527) 和 [docker内无法添加](https://www.njphper.com/posts/f954164.html)

虽然在 docker 里无法添加内存，但是执行指令之后退出 workspace 再进入之后，就可以继续安装了

安装完 Laravel 后不是 **欢迎页面** 或者是

```
There is no existing directory at "/var/www/laravel/storage/logs" and its not buildable: Permission denied
```

在 `Laravel` 的根目录

```
sudo chmod -R 777 storage bootstrap/cache
```

### 数据库 / MySQL

**数据库连接失败**

```
SQLSTATE[HY000] [2002] Connection refused
```

去 .env 配置数据库信息

**更改数据库版本后容器启动失败**

```dockerfile
# 修改 .env 文件
MYSQL_VERSION=5.7 # 默认为 latest

#停止mysql容器
docker-compose stop mysql

# 删除旧数据库数据 
rm -rf ~/.laradock/data/mysql 

# ！注意重启docker应用，然后再构建新 mysql 
docker-compose build mysql 

# 重新创建容器 
docker-compose up -d nginx mysql 

# 查看现有 mysql 版本 
docker inspect laradock_mysql_1
```