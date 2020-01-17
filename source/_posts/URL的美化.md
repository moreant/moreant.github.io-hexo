---
title: URL的美化
categories:
  - mix
tags:
  - 博客
  - hexo
abbrlink: 52295
date: 2019-08-30 09:26:59
---



本站使用的博客框架是 [**Hexo**](https://hexo.io/zh-cn/) ，其中默认的文章链接设置是这样的 `:year/:month/:day/:title/` 具体到本片文章是这样的 `2019/08/30/URL%e7%9a%84%e7%be%8e%e5%8c%96` 。

这个URL有 3 层结构，而且中文经过了 **URL编码** ，很长的 URL 一方面不利于传播分享，另一方面不利于搜索引擎的抓取(虽然国内搜索引擎不会抓*GitHub*的内容)。

<!-- more -->

## v1.0

在 Hexo 的官方文档中就有介绍 URL链接 的设置：

> 您可以在 `_config.yml` 配置中调整网站的永久链接或者在每篇文章的 Front-matter 中指定。
>
> by: Hexo 官方文档——[永久链接](https://hexo.io/zh-cn/docs/permalinks)（Permalinks）

我使用了这篇文档中示例的：

```yaml
:category/:title.html
```

也就是 **类型/标题**

然后在文章的的 Front-matter 中指定 permalink：

```yaml
permalink: URLBeautification
```

这样，URL就变成了：

```yaml
mix/URLBeautification.html
```

简介，望文生义。但也存在问题：

- 需要制定 permalink ，否则中文标题还是会被 URL编码。

- 需要去找到对应的 permalink 标题，一般要去 Google翻译。

- 遇到两个类型的文章，URL就存在冲突

  比如在 [foreach的遍历效率高吗？](https://moreant.github.io/post/18324.html) 中，一开始的类型是 `java` 和 `php` 。URL链接选择了 `java` ，链接是：`java/forOrForeach.html` ，但是这篇文章主要的内容还是 `php` ，URL 反而会导致对文章的误导。

## v2.0

然后去百度搜索了一下，找到现在这个解决方案：

**使用 hexo-abbrlink**

实现的方案，是：

对`标题`+`时间`进行 `crc` 然后用`十进制`或者`十六进制`表示，保存在`front-matter`中。

> [hexo-abbrlink介绍](https://post.zz173.com/detail/hexo-abbrlink.html)

只需要安装 `hexo-addrlink`   in [GitHub](https://github.com/rozbo/hexo-abbrlink)

```
npm install hexo-abbrlink --save
```

然后在`config.yml`，修改`permalink`

```yaml
permalink: post/:abbrlink.html
abbrlink:
  alg: crc16  # 算法：crc16(default) and crc32
  rep: dec    # 进制：dec(default) and hex
```

这个是使用了各个算法、进制的效果

| 算法  | 进制 | 结果                                           |
| :---- | :--- | :--------------------------------------------- |
| crc16 | hex  | `https://post.zz173.com/posts/3ab2.html`       |
| crc16 | dec  | `https://post.zz173.com/posts/12345.html`      |
| crc32 | hex  | `https://post.zz173.com/posts/9a8b6c4d.html`   |
| crc32 | dec  | `https://post.zz173.com/posts/1690090958.html` |

> by: [abbrlink更新2.0.4说明](https://post.zz173.com/detail/hexo-abbrlink-2.0.4.html)

所以最终的链接是：

```
https://moreant.github.io/post/8375.html
```

虽然不能从链接中得到关于文章的信息了，但是实现了：

- 自动生成唯一连接
- 短小精悍

是目前URL链接的最优解了



