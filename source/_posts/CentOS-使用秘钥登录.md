---
stitle: CentOS 使用秘钥登录
categories:
  - oam
tags:
  - 运维
  - Linux
abbrlink: 1528
date: 2020-02-09 12:44:31
---



使用秘钥连接之后终端可以直接快速连接，而且使用  VS Code 的时候也可以免输密码

![](https://markdown.yeek.top/20200209201052.png)



<!-- more -->

<br>

{% note info %}

这里建议使用 VS Code 来当进行 ssh 连接。

VS Code 不仅可以连接终端，还可以直接对服务器上的文件进行编辑。

> [使用 VS Code 远程连接服务器](/post/19479.html)

{% endnote %}



## 生成和保存秘钥

生成秘钥，执行后直接回车三下即可

```bash
ssh-keygen
```

![](https://markdown.yeek.top/20200209184309.png)



进入 `/root/.ssh` 目录，执行以下指令

```bash
# 创建授权秘钥
touch authorized_keys
# 将公钥写进授权秘钥中
cat id_rsa.pub >>  authorized_keys
# 为了安全，修改授权秘钥的权限
chmod 600 authorized_keys
```



下载私钥文件 `id_rsa` 到本地，当然也可以使用 ftp 或者 sz 

![](https://markdown.yeek.top/20200209185252.png)



建议保存到用户名下的 `.ssh` 目录下中，以方便之后的远程连接

![](https://markdown.yeek.top/20200209201512.png)





## 开启秘钥登录

打开 /etc/ssh/sshd_config ，将 `RSAAuthentication` 和 `PubkeyAuthentication` 前面的注释去掉

![](https://markdown.yeek.top/20200209200107.png)



接着重启 sshd 服务，执行

```bash
systemctl restart sshd
```





## 配置连接信息

编辑 `.ssh` 文件夹下的 `config` 文件夹，添加主机的信息。

```bash
# ip地址被我用#和谐了
Host Mjc_root
  HostName 101.###.###.11
  User root
  IdentityFile ~/.ssh/id_rsa_mjc
```



接着就可以使用各种终端连接了

![](https://markdown.yeek.top/20200209201052.png)



