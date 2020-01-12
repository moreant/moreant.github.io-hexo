---
title: Laravel 基础 路由
categories:
  - php
tags:
  - 编程
  - 笔记
  - PHP
  - Laravel
abbrlink: 8294
date: 2019-09-23 09:36:39
---



考web前端证需要学习一个 `php` 框架，学校没有选择以前的 `tp5` 而是选择了 `Laravel`。真就还没学会 `tp5` 又要入门 `Laravel` 呗

- 集成环境 Wampserver3.1.7（PHP 7.2.14）
- Laravel 5.7.*

<!-- more -->

## 笔记归档

[Laravel 基础教程 笔记](https://mojuchen.github.io/tags/Laravel/)

## 安装

安装就不说了，考完证再补笔记

## 前言

初学者了解目录结构，有利于对框架的快速入门

[[ Laravel 5.8 文档 \] 快速入门 —— 目录结构](https://xueyuanjun.com/post/19426.html)

参考文档

> [Laravel 5.8 中文文档](https://learnku.com/docs/laravel/5.8/routing/3890#parameters-regular-expression-constraints)

### 索引

[Laravel 基础教程 笔记](https://mojuchen.github.io/tags/Laravel/)

在 `Laravel57` 中，所有 Laravel 路由都定义在位于 `routes` 目录下的路由文件中，有四个路由文件：

- `web.php` 支持 Session、CSRF 保护以及 Cookie 加密功能
- `api.php` 支持频率限制功能，这些路由是无状态的，所以请求通过这些路由进入应用需要通过 token 进行认证并且不能访问 Session 状态。
- `console.php` 定义了基于控制台的应用入口（路由）。
- `channels.php` 注册应用支持的所有事件广播频道。

## 基本路由

```php
//get
Route::get('base1',function(){
    return "Hello World";
});
//post
Route::post('base2',function(){
    return "Base2";
});
```

[![mark](http://markdown.yeek.top/bolg/20190924/hL9KC4unbONL.jpg?imageslim)](http://markdown.yeek.top/bolg/20190924/hL9KC4unbONL.jpg?imageslim)

## 重定向

```php
Route::redirect('/here', '/there', 301);
```

其中 `here` 表示原路由，`there` 表示重定向之后的路由，`301` 是一个 HTTP 状态码，用于标识重定向。

## 命名

可以方便的给指定路由**生成URL**或者**重定向**

链式调用 `name` 方法来指定名称：

```php
Route::get('user/profile', function () {
    //
})->name('profile');
```

然后就可以使用**全局辅助函数** `route` 来生成链接或者重定向：

```php
// 生成 URL...
$url = route('profile');
//url: http://localhost/user/profile

// 生成重定向...
return redirect()->route('profile');
```

## 视图

```php
Route::get('/welcome', function () {
    return view('welcome');
});
```

也可以直接使用 `view`

第一个参数是包含视图名称的**URL**，第二个参数是需要渲染的**视图名称**，第三个参数是传入的**数据**（可选）

```php
Route::view('/welcome','welcome');

Route::view('/welcome','welcome',['name' => 'moreant']);
```

## 多请求路由

HTTP请求动作有：

```php
Route::get($uri, $callback);
Route::post($uri, $callback);
Route::put($uri, $callback);
Route::patch($uri, $callback);
Route::delete($uri, $callback);
Route::options($uri, $callback);
```

- `match` 指定响应多种的请求动作

- `any` 响应所有请求动作

  ```php
  //只响应 get 和 post 
  Route::match(['get', 'post'], 'multy1', function() {
      return "multy1";
  });
  //响应一切请求动作
  Route::any('multy2', function() {
      return "multy2";
  });
  ```

[![mark](http://markdown.yeek.top/bolg/20190924/4JWpT7cVnHUl.jpg?imageslim)](http://markdown.yeek.top/bolg/20190924/4JWpT7cVnHUl.jpg?imageslim)

## 参数

**必选参数**：

```php
Route::get('user/{id}', function ($id) {
    return "User-id-" . $id;
});
```

**多个参数：**

```php
Route::get('posts/{post}/comments/{comment}', function ($postId, $commentId) {
    //
});
```

**可选参数：**

1. 设置默认值
2. 参数后加 `?`

```php
Route::get('user/{name?}', function ($name = "moreant") {
    return 'User-name-' . $name;
});
```

## 正则约束

链式调用 `where` 方法可以给路由添加正则约束

单个：

```php
Route::get('user/{name}', function ($name) {
    return 'User-name-' . $name;
})->where('name','[A-Za-z]+');
```

多个：

```php
Route::get('user/{id}/{name}', function ($id, $name) {
    //
})->where(['id' => '[0-9]+', 'name' => '[a-z]+']);
```

**全局约束**

在 *laravel\app\Providers\*`RouteServiceProvider.php` 的 `boot` 方法中可以定义全局约束

所有的id都只能为数字：

```php
public function boot()
{
    Route::pattern('id', '[0-9]+');

    parent::boot();
}
```

**可以在具体路由实例中覆盖全局约束**

## 群组

可以共享路由属性

**中间件**的群组，在 group 之前调用 `middleware` 方法：

```php
Route::middleware(['first', 'second'])->group(function () {
    Route::get('/', function () {
        // // 使用 first 和 second 中间件
    });

    Route::get('user/profile', function () {
        // // 使用 first 和 second 中间件
    });
});
```

**命名空间**的群组，使用 `namespace` 方法将相同的 PHP 命名空间分配给路由组的中所有的控制器：

```php
Route::namespace('Admin')->group(function () {
    // 在 "App\Http\Controllers\Admin" 命名空间下的控制器
});
```