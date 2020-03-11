---
title: Web前端中级证4道操作题模拟真题解析
categories:
  - mix
tags:
  - 编程
  - 考证
abbrlink: 54367
date: 2019-12-19 01:50:46
---



<mark>2月更新</mark>

害，理论 77；实操 93 。这真的是中级？这么简单

==12月21日更新==

考完了，发现这题目比模拟题简单一百万倍。我实操题就空了一个空。

==原文==

这个证的含金量是真的高，高到现在的我不配考到这个证，没有文档真做不出来大部分的。

不过代码的质量、可读性都很高，没有注释也可以看得懂，不像我，写了注释也读不懂

{% note danger %}

**注意！**不保证与真实的题目有关系。

{% endnote %}

<!-- more -->

<br>

本文随缘更新，题目来源是 2019-12-18 早上的  {% label info @模拟考试 %}

{% note info%}

四道题目源码+题目截图的打包的 [**下载**](http://download.yeek.top/webExamFourQuestions.zip) 


{% endnote %}



## 第一题

### 题目

![](https://markdown.yeek.top/20191219015217.png)

### 源码

目录如下图：

![](https://markdown.yeek.top/20191219022040.png)

**index.html :** 

```html
<html>
<head>
    <meta charset="utf-8">
    <title>TODO List</title>
    <style>
        #todoList {
            list-style-type: none;
        }
    </style>
</head>
<body>
<ul id="todoList"></ul>
<script>
(() => {
    const todoList = document.querySelector('#todoList');
    fetch('todo.php').then((res) => {
        if (res.(1) === 200) {   <!-- 此行中请填写第（1）空 -->
            res.(2)().then((list) => {   <!-- 此行中请填写第（2）空 -->
                list.forEach((item) => {
                    let li = document.createElement('li');
                    let checkbox = document.createElement('input');
                    checkbox.type = 'checkbox';
                    checkbox.id = "todo_" + item.id;
                    if (item.completed) {
                        checkbox.checked = true; /* 当完成时将checkbox勾选上 */
                    }
                    li.appendChild(checkbox);
                    let label = document.createElement('label');
                    label.innerText = item.text;
                    label.htmlFor = "todo_" + item.id;
                    li.appendChild(label);
                    todoList.appendChild(li);
                    checkbox.addEventListener("click", function (e) {
                        e.preventDefault();
                        let newStatus = this.checked ? 1 : 0;
                        let data = new (3)();   <!-- 此行中请填写第（3）空 -->
                        data.append('(4)', (5));//设置提交的参数     <!-- 此行中请填写第（4）和第（5）空 -->
                        fetch('todo.php(6)' + item.id, {   <!-- 此行中请填写第（6）空 -->
                            '(7)': data,    <!-- 此行中请填写第（7）空 -->
                            '(8)': '(9)'    <!-- 此行中请填写第（8）和第（9）空 -->
                        }).then((res) => {
                            if (res.status === 200) {
                                res.json().then((json) => {
                                    if (json.(10)) {   <!-- 此行中请填写第（10）空 -->
                                        alert(json.error)
                                    } else {
                                        checkbox.checked = !!newStatus;
                                    }
                                })
                            } else {
                                alert('操作异常')
                            }
                        });
                    })
                })
            })
        }
    })
})();
</script>
</body>
</html>

```



**list.json :**

```json
[{"text":"hello","completed":true,"id":1},{"text":"world","completed":false,"id":2}]
```



**todo.php :** 

```php
<?php
header('Content-Type:application/json');
$dataFile = __DIR__ . '/list.json';
switch ($_SERVER['REQUEST_METHOD']) {
    case 'GET':
        echo file_get_contents($dataFile);
        break;
    case 'POST':
        if (!isset($_GET['id'])) {
            echo json_encode(['errno' => 10001, 'error' => 'no id']);
            return;
        }
        $id = intval($_GET['id']);
        $list = json_decode(file_get_contents($dataFile), true);
        foreach ($list as &$item) {
            if ($item['id'] === $id) {
                if (!isset($_POST['status'])){
                    echo json_encode(['errno' => 10002, 'error' => 'no status']);
                    return;
                }
                $item['completed'] = boolval($_POST['status']);
                file_put_contents($dataFile, json_encode($list));
                echo json_encode(['errno' => 0, 'error' => 'ok']);
                return;
            }
        }
        unset($item);
        echo json_encode(['errno' => 10003, 'error' => 'not found']);
        break;
    default:
        echo json_encode(['errno' => 20001, 'error' => 'unsupported method']);
}

```



### 参考链接

> [JavaScript fetch 快速入门](https://blog.csdn.net/zyj362633491/article/details/84997208)
>
> [ES6中fetch的post的前后端node传参的问题](https://blog.csdn.net/dylan_zeng92/article/details/76559922)
>
> [FormData使用方法详解](https://www.jianshu.com/p/e438fb2238cf)



### 答案及解析

我是真没想到考证的 `ajax` 用的既不是 `XMLHTTPRequest` 也不是 `JQuery` 的 `$.ajax` 而是ES6中的 `Ferch`



#### 第 1、2 空

可以从42、43行中找到答案

![](https://markdown.yeek.top/20191219022618.png)



![](https://markdown.yeek.top/20191219022655.png)



直接就是点击复选框里面的事件了，先注释掉这个点击事件，看看1、2空有没有填对。

![](https://markdown.yeek.top/20191219022950.png)



可以从 **todo.php** 里请求到json数据，页面显示也正常。

![](https://markdown.yeek.top/20191219023020.png)



#### 第 6 空

回到**3空**，不知道要 `new` 什么，后面更不知道要填什么，总之可以看出是跟后台有**数据交互**就对了，那就去 **todo.php** 里找答案。

![](https://markdown.yeek.top/20191219024139.png)

通过分析可知，修改列表需要用 `POST` 类型，同时还要用 `GET` 类型传入 **`id`** 这个值

![](https://markdown.yeek.top/20191219023856.png)



用 `GET` 传入的参数肯定是在 `URL` 里的，并且**6空**后面还有 `item.id` 来拼接，所以**6空**的答案是 **?id=**

![](https://markdown.yeek.top/20191219024101.png)

![](https://markdown.yeek.top/20191219024732.png)



#### 第 7、8、9 空

前面也说了，点击事件里的 `fetch` 是 `POST类型` ，根据参考资料需要用传入  `method: 'POST'`  来指定是POST 类型

![](https://markdown.yeek.top/20191219025054.png)

显而易见不可能是7空，因为后面有 `data`，所以只能填入**8、9空**。你问我为什么参考（其他地方）的 `method` 没有用单引号括起来，题目里的却括起来了？咱不知道，也不敢问。

![](https://markdown.yeek.top/20191219025207.png)

那么**7空**毫无疑问就是 **body** 了

![](https://markdown.yeek.top/20191219030147.png)





#### 第 4、5 空

在 **index.html** 里又没有思路了，接着看 **todo.php** ，传入 `id` 之后接着干了什么。

分析可知，POST传入的参数名是 `status`

![](https://markdown.yeek.top/20191219030515.png)

回到 **index.html** 里，第37行里题目给的注释是：**设置提交的参数**。并且看到第35行将**选中情况**赋值给了常量 `newStatus`。

![](https://markdown.yeek.top/20191219030741.png)

可以大胆猜测4、5空是 **status** 和 **newStatus**，如果不信，可以接着往下看。

![](https://markdown.yeek.top/20191219031027.png)



#### 第 3 空

到现在还是不知道**3空**是什么，总之是用 `append方法` 添加数据的。实在不行，搜百度符合条件的就只有 `FormData` 了（具体用法见第三条参考链接）

![](https://markdown.yeek.top/20191219032447.png)

所以**3空**应该填的是 **FormData**

![](https://markdown.yeek.top/20191219032525.png)

![](https://markdown.yeek.top/20191219032541.png)



#### 第 10 空

10空根据下面那行可以判断出填的应该是 **error**  ，如果有 `error(错误信息)` 就用 `alert方法` 弹出错误信息。

![](https://markdown.yeek.top/20191219034348.png)

![](https://markdown.yeek.top/20191219034518.png)



至此，第一题解析完



## 第二题

想查看题目和源码建议到本文顶部的下载中下载查看。

当时忘记保存数据库文件了，所以复原不了环境。但是问题不大此题与数据库没半毛钱关系。

或者是有其他问题吧

![](https://markdown.yeek.top/20191219095951.png)



### 参考链接

>1 空 [通过 Composer 创建项目](<https://learnku.com/docs/laravel/5.7/installation/2242#52027b>)
>
>2、3、4 空 [Model](https://learnku.com/docs/laravel/5.7/eloquent/2294#be7dce)
>
>5、6 [路由](https://learnku.com/docs/laravel/5.7/eloquent/2294#be7dce)
>
>7、8  [表单验证](https://learnku.com/docs/laravel/5.7/eloquent/2294#be7dce) 
>
>9、10 空 [Blade 模板](https://learnku.com/docs/laravel/5.7/blade/2265)





### 答案及解析

考的都是记忆力，就不多解析了。

#### 第 1 空

**create-project**

```
【问题1】基于Laravel框架，回答下列问题，填写（1）至（4）。

1、包管理工具
安装Laravel框架，需要使用Composer 工具：通过命令“composer （1） laravel/laravel --prefer-dist message”创建message工程。
```





#### 第 2、3、4 空

**make:model** 、 **messages** 、 **table**

![](https://markdown.yeek.top/20191219090330.png)



#### 第 5、6 空

第 5 空是因为有前缀，并且里面还有个‘add‘，第 6 空是因为直接打开不是 post 方法

**E** 、 **F**



```
【问题2】分析项目路由，回答下列问题，填写（5）至（6）。

在浏览器中输入网址，访问留言板页面，用户填写留言内容后，点击“提交”按钮，通过POST方式提交表单，在路由文件web.php中，路由配置如下：
<?php
//留言板首页
Route::get('/', "IndexController@index");
//提交留言
Route::prefix('message')->group(function ($app){
    $app->post('add','IndexController@add');
});
 
项目发布在本机上，本机地址为localhost，根据web.php文件中定义的路由信息，当输入下列访问地址时，分析路由情况，选择相应A至F内容，填写（5）至（6）。
```



![](https://markdown.yeek.top/20191219091052.png)



#### 第 7、8 空

**vaildata** 、**max**

![](https://markdown.yeek.top/20191219093507.png)

IndexController.php：

```php
<?php
namespace App\Http\Controllers;
use App\Message;
use Illuminate\Http\Request;

class IndexController extends Controller
{

    public function index()
    {
        $list = Message::all();
        return view('index', ['list' => $list]);
    }

    public function add(Request $request)
    {
        $request->(7)([     /* 在此行中填写第（7）空 */
            'name'=>'required|(8):16',    /* 在此行中填写第（8）空 */
            'content'=>'required'
        ]);
        $content = $request->get('content','');
        $name = $request->get('name','');
        $todo = new Message();
        $todo->fill(['name'=>$name,'content'=>$content]);
        $todo->save();
        return back();
    }
}

```



#### 第 9、10空

**@yield** 、**$loop->last** 

![](https://markdown.yeek.top/20191219093936.png)

layout.balde.php:

```php
<html>
<head>
    <title>Message</title>
    <style>
        time {color:#666}
        .author {color: #333}
        fieldset {margin-top:2em}
    </style>
</head>
<body>
<header>
    <h1>Message board</h1>
</header>
<div class="container">
    (9)('content')    /* 在此行中填写第（9）空 */
</div>
</body>
</html>

```



index.balde.php:

```php
@extends("layout")

@section("content")
    @foreach($list as $item)
        <div>
            <time>{{$item['created_at']}}</time> <span class="author">{{$item['name']}}</span><br>
            <article>{{$item['content']}}</article>
        </div>
        <!-- 每条消息后面加一条分隔线，最后一条除外 -->
        @if(!(10)) <hr> @endif    /* 在此行中填写第（10）空 */
    @endforeach

    <fieldset>
        <legend>New message</legend>
        <form method="POST" action="/message/add">
            @csrf
            <p><input type="text" placeholder="Name" name="name" required></p>
            <p><textarea type="text" placeholder="Content" name="content" required></textarea></p>
            <p><button type="submit">Submit</button></p>
        </form>
    </fieldset>
@endsection

```









## 第三题

### 答案及解析

#### 第 1 空

可以在 meta 的补全里看到答案

![](https://markdown.yeek.top/20191219101159.png)

![](https://markdown.yeek.top/20191219101252.png)





#### 第 2 空

**flex-md-column**

![](https://markdown.yeek.top/20191219101738.png)



#### 第 3 、4 空

很明显是模态框的按钮链接

**modal** 、 **data-target**

![](https://markdown.yeek.top/20191219103351.png)



#### 第 5 空 

**实在不知道** ，百度都搜不出来，看老师给的答案是

**modal-dialog-centered**

![](https://markdown.yeek.top/20191219104203.png)

话外题，百度是真的菜，搜出来的都是3的，或者自己diy的。我用Google搜，第一个就是正确答案

![](https://markdown.yeek.top/20191219215108.png)

![](https://markdown.yeek.top/20191219220040.png)





#### 第 6、7、8

很明显是用了 flex 布局，根据参考链接可以知道是

**flex-direction、column、flex-grow**

![](https://markdown.yeek.top/20191219110913.png)



#### 第 9 空

**linear-gradient**

![](https://markdown.yeek.top/20191219104948.png)



#### 第 10 空

看 `todoForm` ，是 form标签 ，因此可知是处理提交事件

**submit**

![](https://markdown.yeek.top/20191219111516.png)

![](https://markdown.yeek.top/20191219220533.png)





#### 第 11、12 空

**val 、 trim**

![](https://markdown.yeek.top/20191219220613.png)



#### 第 13 空

**append**

![](https://markdown.yeek.top/20191219220633.png)



#### 第 14 空

**preventDefault**

![](https://markdown.yeek.top/20191219114433.png)



#### 第 15 空

`closest` 用的很有灵性

**:checked**

![](https://markdown.yeek.top/20191219114342.png)





## 第四题

#### 第 1、2 空

![](https://markdown.yeek.top/20191220010309.png)

id肯定是主键和自增，这个可以不用背。直接到 phpmyadmin 里新建表，然后预览SQL语句就可以了

**PRIMARY KEY、AUTO_INCREMENT**

![](https://markdown.yeek.top/20191219231942.png)

![](https://markdown.yeek.top/20191219232417.png)



#### 第 3、4 空

![](https://markdown.yeek.top/20191220010328.png)

mysqli是个连接数据库的对象

> [mysqli::__construct](https://www.php.net/manual/zh/mysqli.construct.php)

**new、password**

![](https://markdown.yeek.top/20191219232858.png)







#### 第 5、6 空

![](https://markdown.yeek.top/20191220010531.png)

这两空就是白送分

**session_start()、$query**

![](https://markdown.yeek.top/20191219234117.png)



#### 第 7、8、9 空

![](https://markdown.yeek.top/20191220010602.png)

第 7 空是填标识符，因为默认是 `MYSQLI_BOTH` 而题目让你填一个，并且结合短标签里指定了 `id` 还好 `HbuildX` 里有补全提示，只要记住前面的 `MYSQLI` 就行了

**MYSQLI_ASSOC**

| 标识符 | 返回结果 |
| :----------- | -------- |
| MYSQLI_ASSOC | 关联数组 |
| MYSQLI_NUM   | 数字数组 |
| MYSQLI_BOTH  | 都返回(默认) |

第 11 行的编号后面的期刊名，和第 1、2 空创建**表的字段名**，第 8 空是

**$row[‘title’]**

![](https://markdown.yeek.top/20191220004635.png)



最后要关闭数据库连接

**close()**

![](https://markdown.yeek.top/20191220004926.png)





#### 第 10、11 空

![](https://markdown.yeek.top/20191220010414.png)

两个送分题，`ACTION` 指定表单提交的地址， `onsubmit` 指定提交时处理的JavaScript方法，而这个页面就一个JavaScript方法

**postAdd.php、checkpost()**

![](https://markdown.yeek.top/20191220005801.png)





#### 第 12、13、14 空

![](https://markdown.yeek.top/20191220010628.png)

因为在第 5 行用了之前在 **conn.php** 创建的 `$mysqli` 所以是第 12 空是

**include 'conn.php'** 不唯一，使用 `include_once` / `requrire` / `requrire_once` 都可以

需要验证是否有 `$_POST` 参数，所以第 13 空是

**isset**

第 14 空明显是MySQL插入数据，所以是

**INSERT INTO **

![](https://markdown.yeek.top/20191220010841.png)



#### 第 15、16 空

![](https://markdown.yeek.top/20191220011642.png)

在 **list.php** 里用 `get方法` 传入了 `id`

![](https://markdown.yeek.top/20191220012239.png)

所以第 15 空填入

**$_GET[‘id’]**

第 16 空根据 `name` 可以知道是与 `id` 有关，参照下面的短连接，所以填

**<?=$rs[‘id’]>**

![](https://markdown.yeek.top/20191220011710.png)





#### 第 17、18 空

![](https://markdown.yeek.top/20191220020049.png)

第 17 空明显是更新数据，所以是

**UPDATA journal SET **

当第 4 行的 `query` 执行成功，会返回数据给 `$result` 因此第 18 空就是来判这个的

**$result**

![](https://markdown.yeek.top/20191220020212.png)





#### 第 19 空

送分

**$id**

![](https://markdown.yeek.top/20191220015933.png)



#### 第 20 空

![](https://markdown.yeek.top/20191220020623.png)

又是一道送分题

**$url**

![](https://markdown.yeek.top/20191220020710.png)





## 总结

怎么说呢，可能是没有接触过 `ES6` 标准的 JavaScript，总体来说四道题第一道题是最难的，越往后面做题目越简单。

虽然有些题目的空没有见过是真的填不出来的，但是也有几个空是送分的，有的是需要结合上下文推断出来的。



对于我本人来说，这次考证非常悬，因为第一场理论的知识点我根本没有背过，就真的纯靠蒙了。唯一擅长的操作题又不给文档，也有大几率遇到不会的、没想到的方法。总而言之就是：

**备不足，尽人事、听天命。**



我的直觉是，明年再见！