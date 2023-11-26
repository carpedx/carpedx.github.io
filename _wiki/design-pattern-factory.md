---
layout: wiki
title: 工厂方法模式
cate1: design-pattern
cate2: 
description: 工厂方法模式对比简单工厂来说，最核心的一点，其实就是将实现推迟到子类。
keywords: design-pattern
sorting: 2
---



**工厂方法模式对比简单工厂来说，最核心的一点，其实就是将实现推迟到子类。**

**GoF定义：定义一个用于创建对象的接口，让子类决定实例化哪一个类。Factory Method使一个类的实例化推迟到其子类**

> 相关链接：[简单工厂模式](https://carpedx.com/wiki/design-pattern-simple-factory/)、[抽象工厂模式](https://carpedx.com/wiki/design-pattern-abstract-factory/)

------



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

// 工厂抽象类
abstract class MessageFactory{
    abstract protected function factoryMethod();
    public function createMessage(){
        return $this->factoryMethod();
    }
}

// 阿里云工厂类
class AliYunFactory extends MessageFactory{
    protected function factoryMethod(){
        return new AliYunMessage();
    }
}

// 百度云工厂类
class BaiduYunFactory extends MessageFactory{
    protected function factoryMethod(){
        return new BaiduYunMessage();
    }
}

$factory = new BaiduYunFactory();
$message = $factory->createMessage();
echo $message->send('您有新的短消息，请查收');
```

