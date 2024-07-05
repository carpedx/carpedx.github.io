---
layout: wiki
title: 代理模式
cate1: design-pattern
cate2: 
description: 为其它对象提供一种代理以控制对这个对象的访问
keywords: design-pattern
sorting: 12
---



**GoF定义：为其它对象提供一种代理以控制对这个对象的访问**

------

正向代理、反向代理：

- 通常我们所说的科学上网，就是正向代理，由我们使用软件或者自己配置代理网关上网，实际就是我们把请求发送到指定的网关，再由这个网关代替我们去访问其他网站，这种由我们指定选择的代理就是正向代理
- 当我们访问一个网站的时候，某些路径或者域名并不一定是在这台服务器上，他们在服务器上直接代理到了其他的服务器甚至是别人家的站点。对于这个我们这些浏览网站的人是不知道的，这种我们不知道就被莫名其妙代理了的情况就是反向代理

------

装饰器、适配器、模板方法模式 和 代理模式区别：

- [适配器](https://carpedx.com/wiki/design-pattern-adapter/)，一般会改变方法行为，目的是保持接口的统一但得到不同的实现
- [装饰器](https://carpedx.com/wiki/design-pattern-decorator/)，一般是对对象进行装饰，其中的方法行为会有增加，以修饰对象为主
- [模板方法模式](https://carpedx.com/wiki/design-pattern-template/)，模板方法是在抽象类中去组合调用子类的方法

------



GoF类图：

<img src="/images/wiki/algorithm/design-pattern-proxy_step1.jpg"  />



PHP：

```php
<?php

interface Subject
{
    public function Request();
}

class RealSubject implements Subject
{
    function Request()
    {
        echo "真实的操作", PHP_EOL;
    }
}

class Proxy implements Subject
{
    private $realSubject;

    public function __construct()
    {
        $this->realSubject = new RealSubject();
    }

    public function Request()
    {
        echo "代理的操作", PHP_EOL;
        $this->realSubject->Request();
    }
}

$proxy = new Proxy();
$proxy->Request();
```

