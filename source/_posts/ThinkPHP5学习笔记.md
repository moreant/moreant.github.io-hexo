---
title: ThinkPHP5学习笔记
categories:
  - mix
tags:
  - 编程
  - 笔记
  - PHP
  - ThinkPHP
abbrlink: 7661
date: 2019-08-11 09:14:08
---



本文是 慕课网 [**快速入门ThinkPHP 5.0基础篇**](https://www.imooc.com/learn/832) 的一个笔记。笔者使用的是WampServer环境，编辑器是VScode和Typora

<!-- more -->



## Readme

### 信息

author: Moreant

create: 8/11/2019

email: [552191481@qq.com](mailto:552191481@qq.com)

website: [http://yeek.top](http://yeek.top/)

### 规范

目录除非特殊说明，默认以tp5为根目录

章间三换行，节间二换行，小节间一换行

**Markdown语言规范**

h2 章节

h3 节

h4 小节

代码 `单行代码，路径`

斜体 *文件名*

粗体 **重点**

[Markdown语法入门](https://blog.csdn.net/u014061630/article/details/81359144)

## 安装与配置

### 扩展配置

**方便将一类的配置信息集合在同一个php文件中**

一般放在 `app\conf\extra\` 中

例如 *email.php*:

```
<?php
    return[
        'host' => 'smtp@qq.com',
        'name' => 'moreant@qq.com'
    ];
//在配置信息中就会显示为
'email' => 
    array (size=2)
      'app_email' => string '552191481qq.com' (length=15)
      'app_author' => string 'Moreant' (length=7)
```

### config类和函数

**config类的使用**

```php
//1:在类前面指定根命名空间
$res = \think\Config::get();
dump($res);

//2:在namespace下加上
use \think\Config;
```

**常用方法**

| config类                      | config助手函数                   | 作用                   |
| :---------------------------- | :------------------------------- | :--------------------- |
| get()                         | config()                         | 返回所有的配置信息     |
| get(“database”)               | config(“database”)               | 返回database的配置信息 |
| set(‘user’,‘moreant’)         | config(‘user’,‘moreant’)         | 设置user为moreant      |
| set(‘user’,‘moreant’,‘index’) | config(‘user’,‘moreant’,‘index’) | 指定是index的作用域    |
| has(‘user’)                   | config(‘?user’)                  | 返回布尔值，存在为true |

### 环境变量

`$_ENV` 所有的环境变量， `$_ENV['PHP_EMAIL']` PHP_EMAIL这项的配置信息

#### 常见问题

**dump($_ENV) 数组为空**

需要去 `php.ini` 文件下在把 `variables_order = “GPCS”` 在G的前面加上E，变成EGPCS

[参考链接（待补）](https://mojuchen.github.io/post/7661.html)

**不加载.env文件的内容**

修改框架下的 `thinkphp\base.php`

在 `putenv("$name=$val");` 下面加上两行

```
$_ENV[$name] = $val;
$_SERVER[$name] =$val;
```

**分组的的配置不加载**

修改框架下的 `thinkphp\base.php`

在 `putenv("$item=$v");` 下面加上一行

```
$_ENV[$item]=$v;
```

#### 前缀与大写

默认添加 `PHP_` 作为前缀，并变为大写，例如：

.env中是 `email=moreant@qq.com` ，在$_ENV中的key就为PHP_EMAIL。

#### 分组

```php
[database]
hostname=localhost
username=root
password=root
等价于：
database_hostname=localhost
database_username=root
database_password=root
```

#### Env类的使用

```php
//或缺email的值，如果不存在email则返回NULL，不会产生错误信息
Env::get('email');
//或者
$_ENV['PHP_EMAIL'];

//返回组内的某个值
 Env::get('database.username');
//或者
Env::get('database_username');

//可以传入第二个参数来指定默认值
Env::get('email','morean@qq.com');
```

## URL和路由

### 入口文件

应用入口文件一般指的是 `public\index.php` 这个文件

```php
//默认是
// 定义应用目录
define('APP_PATH', __DIR__ . '/../application/');

// 加载框架引导文件
require __DIR__ . '/../thinkphp/start.php';

----------------------
    
//一般设置为
// 定义应用目录
define('APP_PATH', __DIR__ . '/../app/');

// 定义配置文件目录
define('CONF_PATH',__DIR__.'/../conf/');

// 加载框架引导文件
require __DIR__ . '/../thinkphp/start.php';
```

更多 **define \** 配置可以参考 thinkphp\base.php 的12-31行的 \**defined**

除非有bug，否则**不要修改thinkphp文件夹下的文件**

#### 隐藏入口文件

隐藏入口文件让url看起来更好看 对seo优化有好处

开启后支持将`index.php/admin/index/index`改写为`admin/index/index`

**操作步骤**

1. 启动 **rewrite** 模块：

   打开 **apache** 的 *httpd.conf* ，搜索：

   `LoadModule rewrite_module modules/mod_rewrite.so`

   将前的 `#` 去掉。

2. [启动读取重写信息](https://baike.baidu.com/item/allowoverride/4460060?fr=aladdin)：

   打开 **apache** 的 *httpd-vhosts.conf* ，把站点配置信息中的 `AllowOverride None`

   改为

   `AllowOverride All`

3. 重启 *apache*

ps:我的 WampServer 默认已经启动了上面的模块。

### 入口文件的绑定

在入口文件（*index.php*）中添加

```php
define('BIND_MODULE','admin/index/conf');
```

打开`public\index.php`就会直接进入admin模块下的index文件的conf方法

#### 自动绑定

打开自动绑定模块，在配置中添加

```php+HTML
auto_bind_module => ture
```

会自动访问与php文件名相同的模块，例如 **api.php** 会自动访问 **api** 模块

### 路由

路由的作用是简化URL访问地址，并根据定义的路由类型做出正确的解析。

#### 路由配置

```php
// 是否开启路由
'url_route_on'           => true,
// 路由配置文件（支持配置多个）
'route_config_file'      => ['route'],
// 路由使用完整匹配
'route_complete_match'   => false,
// 是否强制使用路由
'url_route_must'         => false,
```

在 `conf/` 下创建 *route.php* 文件，内容如下

```php+HTML
<?php
    return[
    'news/' => 'index/index/index'
    'news/:id' => 'index/index/info'
];
```

访问：

```
http://localhost/news
```

会自动路由到

```
http://localhost/index/index/index
```

访问：

```
http://localhost/news/4
```

会自动路由到

```
http://localhost/index/index/info/5
```

**且禁止直接访问**

## 请求和响应

接受和处理所有的请求

### 对象获取

有三个方法可以获得请求对象：

**助手函数**

```
$request = request();
```

**Request类**

```php
//先 ues Request 类
use think\Request;

$request = Request::instance();
```

**传参**

```php
//先 ues Request 类
use think\Request;
public function index (Request $request){
    
}
```

### 参数获取

例如文件

```php
app\index\controller\index.php
```

访问URL

```php
http://localhost/index/index/index/type/5.html?id=10
<?php

namespace app\index\controller;

use think\Request;
    
public function index(Request $request)
    {
        //获取URL的值
        //域名  http://localhost
        dump($request->domain());
        //路径  index/index/index/type/5.html
        dump($request->pathinfo());
        //不带html的路径
        dump($request->path());
        //请求类型 GET
        dump($request->method());
    
        //望文生义 返回类型为Boolean
        dump($request->isGet());
        dump($request->isPOST());
        dump($request->isAjax());
        
        //5.0之后分path中的值不属于get
        //get的值  10
        dump($request->get());
        //get和path的值 10,5
    	dump($request->param());
    
        //望文生义 返回类型为array
        dump($request->post());
        dump($request->session());
        dump($request->cookie());
    
        //直接获得某项的值
        dump($request->param('id'));
        dump($request->cookie('email'));
        
        //望文生义
        dump($request->module());
        dump($request->controller());
        //操作  index
        dump($request->action());
    
    }
```

### input函数

### 响应对象