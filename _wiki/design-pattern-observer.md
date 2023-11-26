---
layout: wiki
title: 观察者模式
cate1: design-pattern
cate2: 
description: 定义对象间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都得到通知并被自动更新
keywords: design-pattern
sorting: 6
---



**GoF定义：定义对象间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都得到通知并被自动更新**

------



- PHP 的 SPL 扩展中已经为我们准备好了一套观察者接口，使用原生支持的观察者模式能省不少事

------



PHP：

```php
<?php

// 观察者接口
interface Observer
{
    public function update($obj);
}

// 消息观察者
class Message implements Observer
{
    function update($obj)
    {
        echo '发送新订单短信(' . $obj->mobile . ')通知给商家！';
    }
}
// 商品观察者
class Goods implements Observer
{
    public function update($obj)
    {
        echo '修改商品' . $obj->goodsId . '的库存！';
    }
}

// 订单类
class Order
{
    private $observers = [];
    // 添加观察者
    public function attach($ob)
    {
        $this->observers[] = $ob;
    }
    // 删除观察者
    public function detach($ob)
    {
        $position = 0;
        foreach ($this->observers as $ob) {
            if ($ob == $observer) {
                array_splice($this->observers, ($position), 1);
            }
            ++$position;
        }
    }
    // 通知观察者
    public function notify($obj)
    {
        foreach ($this->observers as $ob) {
            $ob->update($obj);
        }
    }
    // 订单商品卖出
    public function sale()
    {
        $obj = new stdClass();
        $obj->mobile = '13888888888';
        $obj->goodsId = 'Order11111111';
        // 通知观察者
        $this->notify($obj);
    }
}

// 创建订单
$order = new Order();

// 添加观察者
$message = new Message();
$order->attach($message);
$goods = new Goods();
$order->attach($goods);

// 订单商品卖出
$order->sale();
```



