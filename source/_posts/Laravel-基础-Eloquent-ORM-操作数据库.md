---
title: Laravel 基础 Eloquent ORM 操作数据库
categories:
  - php
tags:
  - 编程
  - 笔记
  - PHP
  - Laravel
abbrlink: 39513
date: 2019-10-08 09:52:56
---



> 数据库表通常相互关联。 例如，一篇博客文章可能有许多评论，或者一个订单对应一个下单用户。Eloquent 让这些关联的管理和使用变得简单，并支持多种类型的关联

**Eloquent ORM** 可以通过 **模型-数据库** 来交互，使用模型来查询、插入、更新、删除。同时，Eloquent 可以使用查询构造器的所有功能

[更多介绍](https://learnku.com/index.php/docs/laravel/5.7/eloquent-relationships/2295)

<!-- more -->



## 笔记归档

[Laravel 基础教程 笔记](https://mojuchen.github.io/tags/Laravel/)

## 模型建立

**模型**，也就是 `Model` ，位置在 *app\* 。使用 `Eloquent ORM` 模型的基本如下：

```php+HTML
<?php

namespace App;

// use 是必须的
use Illuminate\Database\Eloquent\Model;

class Student extends Model
{
    // 指定表名
    protected $table = 'student';
    
    // 指定 id
    protected $primaryKey = 'id';
}
```

如果不指定 `$table` 就会假定关联的表名是**模型名+s**，如此例的表名就是 `students`

## 查询

查询返回的类型都是**对象**。

常用的查询方法有： `all` / `find` / `findOrfail`

Eloquent 模型充当一个[查询构造器](https://learnku.com/docs/laravel/5.7/queries) ，所以也可以**添加查询条件**：

```php
$student = Student::where('id','>=','1005')->get();
```



{% tabs select %}

<!-- tab all -->

**all**

返回表中的所有数据：

```php
$student = Student::all();
```

<!-- endtab -->

<!-- tab find -->

**find**

根据**主键**查找：

```php
$student = Student::find(1003);
```

<!-- endtab -->

<!-- tab findOrFail -->

**findOrFail**

在 `find()` 的基础上，如果找不到就**返回404：**

```php
$student = Student::findOrFail(1010);
```

<!-- endtab -->



{% endtabs %}





## 创建

创建常用的方法有： `save` / `create` / `fill` / `firstOrCreate` / `firstOrNew` / `updateOrCreate`

### 基本使用

使用对象创建数据：

```php
$student = new Student();
$student->name = 'moreant';
$student->age = '18';
$result = $student->save();
```

用这个方法新增数据，Laravel 会自动在 `created_at` 和 `update_at` 中插入时间戳，并且维护。

### 批量赋值

首先需要去模型中指定 `fillable` 或 `guarded` 属性，因为所有的 Eloquent 模型在默认情况下都不能进行批量赋值。

```php
<?php

namespace App;

// use 是必须的
use Illuminate\Database\Eloquent\Model;

class Student extends Model
{
    // 可以批量赋值的属性
    protected $fillable = ['name','age'];

    // 不可以批量赋值的属性
    protected $guarded = ['id'];
}
```

注意，在使用上，只能是 `$fillable` 或 `$guarded` 二选一。

**将 $guarded 定义为空数组，则所有属性都可以被批量赋值**

之后，就可以使用 `create()` 插入新数据，并且返回刚刚保存的**实例**：

```php
$student =  Student::create(['name' => 'imooc', 'age' => 3]);
```

或者使用实例中的 `fill()` 来赋值

```php+HTML
$student->fill(['name' => 'imooc', 'age' => 3]);
```

### 时间戳

如果不需要，可以**关闭时间戳**，只需要在模型中将 `$timestamps` 属性设置为 `false`：

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Student extends Model
{
    // 是否维护时间戳
    public $timestamps = false;
}
```

设置为时间戳为**中国时间**：

在 *config\*`app.php` 中修改 `timezone` 属性，

```php
'timezone' => 'Asia/Shanghai'
```

**指定时间戳的格式：**

```php
protected $dateFormat = 'U';
```

**自定义时间戳字段名：**

```php
const CREATED_AT = 'creation_date';
const UPDATED_AT = 'last_update';
```

### 其他创建方法

有三种常用的创建方法 `firstOrCreate`/ `firstOrNew` / `updateOrCreate`

**firstOrCreate**

用给定的值查找数据库，如果不存在，则插入记录到数据库中。返回插入后的实例。

查询 `name` 为 `morea3` 的数据，查到返回对象，查不到用给定的参数创建：

```php
$student =  Student::firstOrCreate(['name' => 'morea3'],['age'=>29]);
```

**firstOrNew**

与 `firstOrCreat` 相似，只不过返回的是一个**未保存**的实例，需要使用 `save` 方法来保存：

```php
$student =  Student::firstOrNew(['name' => 'morea66'],['age'=>18]);
```

**updateOrCreate**

更新版的 **firstOrCreate**

```php
$student =  Student::firstOrCreate(['name' => 'morea'],['age'=>18]);
```

## 更新

基本使用：

```php
$student =  Student::find(10);
$student->name  = "immoc";
$status =  $student->save();
```

使用 `where` 方法查找对象，并且更新

```php
$student =  Student::where('name','=','moreant')->first();
$student->age  = 20;
$status =  $student->save();
```

## 删除

删除的常用方法有： `delete` / `destory` /

**通过模型**删除

```php
$student =  Student::find(13);
$result =  $student->delete();
```

**通过主键**删除

```php
Student::destory(1);

Student::destory(1,2);

Student::destory([1,2,3]);
```

**通过查询**删除

```php
$result = Student::where('id','>=','4')->delete();
```

