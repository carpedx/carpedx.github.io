---
layout: wiki
title: 装饰器模式
cate1: design-pattern
cate2: 
description: 动态地给一个对象添加一些额外的职责
keywords: design-pattern
sorting: 4
---



**GoF定义：动态地给一个对象添加一些额外的职责，就增加功能来说，Decorator模式相比生成子类更为灵活**

------



- Java 的 I/O 系列接口是使用的这种设计模式：`FileInputStream`、`LineNumberInputStream`、`BufferInputStream` 等
- Laravel 框架中的中间件管道其中也应用到了装饰器模式：Laravel HTTP `Pipeline` 中间件
- 另外在 Laravel 中，日志处理这里也是对 `Monolog` 进行了装饰

------



GoF类图：

<img src="/images/wiki/algorithm/design-pattern-decorator_step1.jpg"  />



PHP代码类图：

<img src="/images/wiki/algorithm/design-pattern-decorator_step2.jpg"  />



PHP：

```php
<?php

// 短信模板接口
interface MessageTemplate
{
    public function message();
}

// 优惠券发送的短信模板
class CouponMessageTemplate implements MessageTemplate
{
    public function message()
    {
        return '优惠券信息：我们是全国第一牛X的产品哦，送您十张优惠券！';
    }
}

// 短信模板 装饰抽象类
abstract class DecoratorMessageTemplate implements MessageTemplate
{
    public $template;
    public function __construct($template)
    {
        $this->template = $template;
    }
}

// 装饰类1 过滤新广告法中不允许出现的词汇
class AdFilterDecoratorMessage extends DecoratorMessageTemplate
{
    public function message()
    {
        return str_replace('全国第一', '', $this->template->message());
    }
}

// 装饰类2 使用我们的大数据部门同事自动生成的新词库来替换敏感词汇
class SensitiveFilterDecoratorMessage extends DecoratorMessageTemplate
{
    public $bigDataFilterWords = ['牛X'];
    public $bigDataReplaceWords = ['好用'];
    public function message()
    {
        return str_replace($this->bigDataFilterWords, $this->bigDataReplaceWords, $this->template->message());
    }
}

// 客户端，发送接口
class Message
{
    public $msgType = 'old';
    public function send(MessageTemplate $mt)
    {
        if ($this->msgType == 'old') {
            echo '面向内网用户发送' . $mt->message() . PHP_EOL;
        } else if ($this->msgType == 'new') {
            echo '面向全网用户发送' . $mt->message() . PHP_EOL;
        }

    }
}

// 老系统
$message = new Message();
$template = new CouponMessageTemplate();
$message->send($template);

// 新系统
$message->msgType = 'new';
$template = new AdFilterDecoratorMessage($template);
$template = new SensitiveFilterDecoratorMessage($template);

// 发送
$message->send($template);
```

