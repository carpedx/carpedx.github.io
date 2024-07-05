---
layout: wiki
title: 简单工厂模式
cate1: design-pattern
cate2: 
description: 简单工厂，也称静态工厂，不属于GoF23种设计模式。但是可以说是所有的设计模式中最容易理解的。
keywords: design-pattern
sorting: 1
---



**简单工厂，也称静态工厂，不属于GoF23种设计模式。但是可以说是所有的设计模式中最容易理解的。**

> 相关链接：[工厂方法模式](https://carpedx.com/wiki/design-pattern-factory/)、[抽象工厂模式](https://carpedx.com/wiki/design-pattern-abstract-factory/)、[策略模式](https://carpedx.com/wiki/design-pattern-strategy/)

------



GoF类图：

<img src="/images/wiki/algorithm/design-pattern-simple-factory_step1.jpg"  />



PHP方法类图：

<img src="/images/wiki/algorithm/design-pattern-simple-factory_step2.jpg"  />



PHP：

```php
<?php

// 发送短信接口
interface Message {
    public function send(string $msg);
}

// 阿里云发送短信
class AliYunMessage implements Message{
    public function send(string $msg){
        return '阿里云短信（原阿里大鱼）发送成功！短信内容：' . $msg;
    }
}

// 百度云发送短信
class BaiduYunMessage implements Message{
    public function send(string $msg){
        return '百度SMS短信发送成功！短信内容：' . $msg;
    }
}

// 工厂类
Class MessageFactory {
    public static function createMessage($type){
        switch($type){
            case 'Ali':
                return new AliYunMessage();
            case 'BD':
                return new BaiduYunMessage();
            default:
                return null;
        }
    }
}

$message = MessageFactory::createMessage('Ali');
echo $message->send('您有新的短消息，请查收');
```

