---
title: Laravel 基础 控制器
categories:
  - php
tags:
  - 编程
  - 笔记
  - PHP
  - Laravel
abbrlink: 30761
date: 2019-10-04 09:40:20
---



`Laravel` 框架第二篇——控制器，控制器的介绍在慕课网的**视频教程**中说的非常少（6分钟），而且大部分都是跟路由的连接。 Laravel China 社区的**文档**就非常详尽

<!-- more -->

## 笔记归档

[Laravel 基础教程 笔记](https://mojuchen.github.io/tags/Laravel/)

## 基础使用

### 定义控制器

控制器目录： *App\Http\Controllers*

例如 `MemberController.php`

- 文件名： 名字+Controller
- 命名空间 App\Http\Controllers
- 继承 Controller

```php
<?php

namespace App\Http\Controllers;

class MemberController extends Controller
{
    public function info($id)
    {
        return 'member-info-'.$id;
    }
}
```

### 关联路由

```php
Route::get('member/{id}','MemberController@info');
```

### 传递参数