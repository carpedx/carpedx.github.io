---
layout: wiki
title: 责任链模式
cate1: design-pattern
cate2: 
description: 使多个对象都有机会处理请求，从而避免请求的发送者和接收者之间的耦合关系
keywords: design-pattern
sorting: 11
---



**GoF定义：使多个对象都有机会处理请求，从而避免请求的发送者和接收者之间的耦合关系。将这些对象连成一条链，并沿着这条链传递该请求，直到有一个对象处理它为止**

------

- 责任链非常适合的一种场景，就是对请求参数进行逐层过滤，就像我们工作时使用钉钉之类的办公软件。当需要提加班或者休假申请时，那一层层的审批流程就是对这个模式最完美的解释
- 我们可以拦截请求，直接返回，也可以对请求内容进行完善修改交给下一个类来进行处理，但至少有一个类是要返回结果的。
- 请求不一定都会被处理，也有可能完全不处理就返回或者传递给下一个处理类来进行处理

- 责任链的运用真的非常广泛，在各种工作流软件及中间件组件中都可以看到，同时配合Linux下的管道思想，可以把这个模式的优势发挥到极致
- Laravel的中间件，有兴趣的朋友翻翻源码，典型的责任链模式的应用哦

------



GoF类图：

<img src="/images/wiki/algorithm/design-pattern-chain-of-responsiblity_step1.jpg"  />



PHP：

```php
<?php

/*
 * 定义抽象责任链类，使用$success保存后继链条
 */

abstract class Handler
{
    protected $success;
    public function setNext($success)
    {
        $this->success = $success;
    }
    abstract public function HandleRequst($request);
}

/*
 * 三个责任链条的具体实现，主要功能是判断传入的数据类型，如果是数字由第一个类处理，如果是字符串，则第二个类处理。如果是其他类型，第三个类统一处理
 */

class ConcreteHandler1 extends Handler
{
    public function HandleRequst($request)
    {
        if (is_numeric($request)) {
            return '请求参数是数字：' . $request;
        } else {
            return $this->success->HandleRequst($request);
        }
    }
}
class ConcreteHandler2 extends Handler
{
    public function HandleRequst($request)
    {
        if (is_string($request)) {
            return '请求参数是字符串：' . $request;
        } else {
            return $this->success->HandleRequst($request);
        }
    }
}
class ConcreteHandler3 extends Handler
{
    public function HandleRequst($request)
    {
        return '我也不知道请求参数是啥了，你猜猜？' . gettype($request);
    }
}

// 实例化三个责任链实例，并指定链条成员
$handle1 = new ConcreteHandler1();
$handle2 = new ConcreteHandler2();
$handle3 = new ConcreteHandler3();

$handle1->setNext($handle2);
$handle2->setNext($handle3);

// 创建请求参数
$requests = [22, 'aaa', 55, 'cc', [1, 2, 3], null, new stdClass];

// 通过责任链来进行结果判断
foreach ($requests as $request) {
    echo $handle1->HandleRequst($request) . PHP_EOL;
}
```

