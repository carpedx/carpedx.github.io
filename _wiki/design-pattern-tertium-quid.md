---
layout: wiki
title: 中介者模式
cate1: design-pattern
cate2: 
description: 用一个中介对象来封装一系列的对象交互
keywords: design-pattern
sorting: 15
---



**GoF定义：用一个中介对象来封装一系列的对象交互。中介者使各对象不需要显式地相互引用，从而使其耦合松散，而且可以独立地改变它们之间的交互**

------

- 聊天社交、sns、直播之类的都很适合这个模式，因为这个模式就是能让用户与用户之间解耦，不需要让一个用户去维护所有有关联的用户对象

- 因为不需要用户去维护关系，所以也就顺便解决了关系之间的多对多维护的问题，同时，也不需要去修改用户类来进行关系的变更，保持了用户类的良好封装
- 但是，中介者集中维护可能导致这个类过于复杂和庞大
- 所以，模式不是万能的，一定要弄清楚业务场景进行取舍地使用
- 中介者适用于一组对象以定义良好但是复杂的方式进行通信的场合，以及想定制一个分布在多个类中的行为，而又不想生成太多子类的场合

------



GoF类图：

<img src="/images/wiki/algorithm/design-pattern-tertium-quid_step1.jpg"  />



PHP：

```php
<?php

/*
 * 抽象出来的中介者和具体的实现
 * 在这里，我们假定有固定的两个同事类，让他们互相对话，所以进入的同事是1的时候，就去调用2的Notify方法，相当于是让2接收到了1发来的消息
 */

abstract class Mediator
{
    abstract public function Send(String $message, Colleague $colleague);
}

class ConcreteMediator extends Mediator
{
    public $colleague1;
    public $colleague2;

    public function Send(String $message, Colleague $colleague)
    {
        if ($colleague == $this->colleague1) {
            $this->colleague2->Notify($message);
        } else {
            $this->colleague1->Notify($message);
        }
    }
}

/*
 * 同事类及具体的实现
 * 这里我们要确认的一点就是，每一个同事类，只认识中介者，并不认识另外的同事类，这就是中介者的特点，双方不用认识。
 */

abstract class Colleague
{
    protected $mediator;

    public function __construct(Mediator $mediator)
    {
        $this->mediator = $mediator;
    }
}

class ConcreteColleague1 extends Colleague
{
    public function Send(String $message)
    {
        $this->mediator->Send($message, $this);
    }

    public function Notify(String $message)
    {
        echo "同事1得到信息：" . $message, PHP_EOL;
    }
}

class ConcreteColleague2 extends Colleague
{
    public function Send(String $message)
    {
        $this->mediator->Send($message, $this);
    }

    public function Notify(String $message)
    {
        echo "同事2得到信息：" . $message, PHP_EOL;
    }
}

$m = new ConcreteMediator();

$c1 = new ConcreteColleague1($m);
$c2 = new ConcreteColleague2($m);

$m->colleague1 = $c1;
$m->colleague2 = $c2;

$c1->Send("吃过饭了吗？");
$c2->Send("没有呢，你打算请客？");
```
