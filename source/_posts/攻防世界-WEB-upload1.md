---
title: 攻防世界 WEB upload1
categories:
  - ctf
tags:
  - 信息安全
  - ctf-web-入门
abbrlink: 17040
date: 2020-01-17 15:16:49
---







# 0x00 - 题目描述

![](http://markdown.yeek.top/20200117152226.png)



<!-- MORE -->

# 0x01 - 解题过程

打开页面，只有一个上传模块

![](http://markdown.yeek.top/20200117152444.png)



既然题目是 **uplaod1** 那就可以试一下上传一句话木马。

下面就是一句话木马的内容，只要上传到服务器后，就可以用工具连接控制服务器了。

![](http://markdown.yeek.top/20200117161249.png)

选择完木马文件之后果然被阻止了，上传按钮不可用了。

![上传提示](http://markdown.yeek.top/20200117152545.png)

![](http://markdown.yeek.top/20200117155007.png)



分析 js 代码，是把按钮给 `disable` 了。

![](http://markdown.yeek.top/20200117155400.png)





那很简单，在 `html` 里吧 `disable` 删掉就可以继续上传了

![](http://markdown.yeek.top/20200117155937.png)



![](http://markdown.yeek.top/20200117160702.png)



得到上传的地址。访问地址没有404，说明正常打开了。

![](http://markdown.yeek.top/20200117160924.png)



使用中国菜刀连接。

![](http://markdown.yeek.top/20200117161215.png)

能访问服务器文件。开始找flag

![](http://markdown.yeek.top/20200117161357.png)



一般不是在服务器的根目录就是在网站的根目录，这道题是后者

![](http://markdown.yeek.top/20200117161513.png)







# 0x02 - 技术总结

- 注意看网页的源码