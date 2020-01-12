---
title: Laravel 基础 视图
categories:
  - php
tags:
  - 编程
  - 笔记
  - PHP
  - Laravel
abbrlink: 45228
date: 2019-10-05 09:45:28
---

[**慕课网教程**](https://www.imooc.com/video/12497)里的视图讲的比之前控制器就更少了，详细的内容以后读完 [**Laravel China 社区里的文档**](https://learnku.com/docs/laravel/5.7/views/2259)再补



<!-- more -->

## 笔记归档

[Laravel 基础教程 笔记](https://mojuchen.github.io/tags/Laravel/)

## 基本使用

视图目录： *resources\views\*

在视图目录中新建一个 `member-info.php`

```
Hello
```

在**控制器**中输出视图

```php
<?php

namespace App\Http\Controllers;

class MemberController extends Controller
{
    public function info($id)
    {
        return view('member-info');
    }
}
```

**路由**中直接输出

```php
Route::view('member','member-info');
```

更多请参考 [路由](https://mojuchen.github.io/post/37002.html#视图)

**传递参数**

首先文件名是 `*.blade.php` 表示是模板文件才可以传递参数， Blade 模板的更多信息在 [这里](https://learnku.com/docs/laravel/5.7/blade)

控制器中

```php
public function info($name=null)
    {
        return view('member/info', [  
            'name' => $name
        ]);
    }
```

层级关系用 `.` 也是可以的

```php
return view('member.info', [
           'name' => $name
       ]);
```

路由中

```php
Route::get('test/', function () {
    return view('test', ['name' => 'moreant']);
});
```

或者直接使用 `view` 方法

```php
Route::view('/test2','test',['name' => 'moreant-test']);
```