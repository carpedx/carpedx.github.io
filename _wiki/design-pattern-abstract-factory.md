---
layout: wiki
title: 抽象工厂模式
cate1: design-pattern
cate2: 
description: 与工厂方法模式不同的是抽象工厂里面不仅仅只返回一个对象，而是返回一堆
keywords: design-pattern
sorting: 3
---



**与工厂方法模式不同的是抽象工厂里面不仅仅只返回一个对象，而是返回一堆。**

***GoF定义：提供一个创建一系列相关或相互依赖对象的接口，而无需指定它们具体的类***

> 相关链接：[简单工厂模式](https://carpedx.com/wiki/design-pattern-simple-factory/)、[工厂方法模式](https://carpedx.com/wiki/design-pattern-factory/)

------



GoF类图：

<img src="/images/wiki/algorithm/design-pattern-abstract-factory_step1.jpg"  />



PHP代码类图：

<img src="/images/wiki/algorithm/design-pattern-abstract-factory_step2.jpg"  />



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

// 推送消息接口
interface Push {
    public function send(string $msg);
}

// 阿里云推送消息
class AliYunPush implements Push{
    public function send(string $msg){
        return '阿里云Android&iOS推送发送成功！推送内容：' . $msg;
    }
}

// 百度云推送消息
class BaiduYunPush implements Push{
    public function send(string $msg){
        return '百度Android&iOS云推送发送成功！推送内容：' . $msg;
    }
}

// 工厂接口类
interface MessageFactory{
    public function createMessage();
    public function createPush();
}

// 阿里云工厂类
class AliYunFactory implements MessageFactory{
    public function createMessage(){
        return new AliYunMessage();
    }
    public function createPush(){
        return new AliYunPush();
    }
}

// 百度云工厂类
class BaiduYunFactory implements MessageFactory{
    public function createMessage(){
        return new BaiduYunMessage();
    }
    public function createPush(){
        return new BaiduYunPush();
    }
}

class JiguangFactory implements MessageFactory{
    public function createMessage(){
        return new JiguangMessage();
    }
    public function createPush(){
        return new JiguangPush();
    }
}

$factory = new AliYunFactory();
$message = $factory->createMessage();
$push = $factory->createPush();
echo $message->send('您已经很久没有登录过系统了，记得回来哦！');
echo $push->send('您有新的红包已到帐，请查收！');
```

