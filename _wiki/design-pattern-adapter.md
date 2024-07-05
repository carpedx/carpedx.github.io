---
layout: wiki
title: 适配器模式
cate1: design-pattern
cate2: 
description: 将一个类的接口转换成客户希望的另外一个接口。Adapter模式使得原本由于接口不兼容而不能一起工作的那些类可以一起工作
keywords: design-pattern
sorting: 5
---



**GoF定义：将一个类的接口转换成客户希望的另外一个接口。Adapter模式使得原本由于接口不兼容而不能一起工作的那些类可以一起工作**

------

- 继承形式的在GoF书中是以C++为示例的，因为C++可以实现多重继承，但现在流行的大部分语言是以接口为形式的，也可以实现，但使用这种形式的适配器不多

- 其实还是面向接口编程的一种思维，类似于装饰器对旧功能的包装，我们这里就是直接去进行了替换，但对外的调用还是保持不变

------



GoF类图（继承式）：

<img src="/images/wiki/algorithm/design-pattern-adapter_step1.jpg"  />



GoF类图（组合式）：

<img src="/images/wiki/algorithm/design-pattern-adapter_step2.jpg"  />



PHP代码类图：

<img src="/images/wiki/algorithm/design-pattern-adapter_step3.jpg"  />



PHP：

```php
<?php

// 旧的短信发送、消息推送
class Message{
    public function send(){
        echo "阿里云发送短信！" . PHP_EOL;
    }
    public function push(){
        echo "阿里云发送推送！" . PHP_EOL;
    }
}

// 极光短信发送、消息推送 适配器
class JiguangSDKAdapter extends Message{
    private $message;

    public function __construct($message){
        $this->message = $message;
    }

    public function send(){
        $this->message->send_out_msg();
    }
    public function push(){
        $this->message->push_msg();
    }
}

// 极光短信发送、消息推送
class JiguangMessage{
    public function send_out_msg(){
        echo "极光发送短信！" . PHP_EOL;
    }
    public function push_msg(){
        echo "极光发送推送！" . PHP_EOL;
    }
}

// 百度云短信发送、消息推送 适配器
class BaiduYunSDKAdapter extends Message{
    private $message;

    public function __construct($message){
        $this->message = $message;
    }

    public function send(){
        $this->message->transmission_msg();
    }
    public function push(){
        $this->message->transmission_push();
    }
}

// 百度云短信发送、消息推送
class BaiduYunMessage{
    public function transmission_msg(){
        echo "百度云发送短信！" . PHP_EOL;
    }
    public function transmission_push(){
        echo "百度云发送推送！" . PHP_EOL;
    }
}

// 原来的老系统发短信，使用阿里云
$message = new Message();
$message->send();
$message->push();

// 部分模块用极光发吧
$jiguangMessage = new JiguangMessage();
$jgAdatper = new JiguangSDKAdapter($jiguangMessage);
$jgAdatper->send();
$jgAdatper->push();

// 部分模块用百度云发吧
$baiduYunMessage = new BaiduYunMessage();
$bdAatper = new BaiduYunSDKAdapter($baiduYunMessage);
$bdAatper->send();
$bdAatper->push();
```



