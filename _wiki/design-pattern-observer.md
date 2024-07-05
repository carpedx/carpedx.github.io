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

- 观察者，其实就是自身做了一个更新（update），而Subject，可以批量的执行观察者，请注意，我们不需要去修改目标类中的任何代码，只需要从外部添加就可以了，所以就让目标和观察者解耦互相之间不用关心对方的情况了
- 观察者可以记录目标的状态，也可以不用记录，比如我们发完短信后的数据库更新或者插入操作，只有短信接口发送成功后我们再修改短信数据的状态就可以了，不一定完全需要将目标的发送状态传送给观察者
- 当一个类在发生改变时，不知道可能会对其他多少类产生影响，这个时候观察者非常有用
- 观察者模式中还是存在着耦合，那就是目标类中有一个观察者对象列表，如果观察者没有实现update()方法，那么就会出现问题

- PHP 的 SPL 扩展中已经为我们准备好了一套观察者接口，使用原生支持的观察者模式能省不少事

------



GoF类图：

<img src="/images/wiki/algorithm/design-pattern-observer_step1.jpg"  />



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



