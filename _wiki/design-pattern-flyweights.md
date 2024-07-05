---
layout: wiki
title: 享元模式
cate1: design-pattern
cate2: 
description: 运用共享技术有效地支持大量细粒度的对象
keywords: design-pattern
sorting: 13
---



**GoF定义：“享”就是共享，“元”就是元素，共享某些元素。运用共享技术有效地支持大量细粒度的对象**

------

- 当一个应用程序使用了大量非常相似的的对象，对象的大多数状都可变为外部状态时，很适合享元模式

- 这里的工厂是存储对象列表的，不是像工厂方法或者抽象工厂一样去创建对象的，虽说这里也进行了创建，但如果对象存在，则会直接返回，而且列表也是一直维护的
- 享元模式在现实中，大家多少一定用过，各种池技术就是它的典型应用：线程池、连接池等等，另外两个一样的字符串String类型在php或Java中都是可以===的，这也运用到了享元模式，它们连内存地址都是一样的，这不就是一种共享嘛
- 关于享元模式，有一个极其经典的例子，比我下面的例子要好的多，那就是关于围棋的棋盘。围棋只有黑白两色，所以两个对象就够了，接下来呢？改变他们的位置状态就好啦！有兴趣的朋友可以搜搜！
- Laravel中的IoC容器可以看作是一种享元模式的实现。它把对象保存在数组中，在需要的时候通过闭包机制进行取用，也有一些类有共享一些状态属性的内容。大家可以翻看代码了解了解。

------



GoF类图：

<img src="/images/wiki/algorithm/design-pattern-flyweights_step1.jpg"  />



PHP：

```php
<?php

/*
 * 定义共享接口以及它的实现
 * 注意这里有两个实现，ConcreteFlyweigh进行状态的共享，UnsharedConcreteFlyweight不共享或者说他的状态不需要去共享
 */

// 定义享元接口
interface Flyweight
{
    public function operation($extrinsicState) : void;
}

// 享元实现类，进行状态共享
class ConcreteFlyweight implements Flyweight
{
    private $intrinsicState = 101;
    function operation($extrinsicState) : void
    {
        echo '共享享元对象' . ($extrinsicState + $this->intrinsicState) . PHP_EOL;
    }
}
// 不共享
class UnsharedConcreteFlyweight implements Flyweight
{
    private $allState = 1000;
    public function operation($extrinsicState) : void
    {
        echo '非共享享元对象：' . ($extrinsicState + $this->allState) . PHP_EOL;
    }
}

/*
 * 保存那些需要共享的对象，做为一个工厂来创建需要的共享对象，保证相同的键值下只会有唯一的对象，节省相同对象创建的开销
 */

// 工厂创建需要的共享对象，保证相同的键值下只会有唯一的对象，节省相同对象创建的开销
class FlyweightFactory
{
    private $flyweights = [];

    public function getFlyweight($key) : Flyweight
    {
        if (!array_key_exists($key, $this->flyweights)) {
            $this->flyweights[$key] = new ConcreteFlyweight();
        }
        return $this->flyweights[$key];
    }
}

// 客户端的调用，让外部状态 $extrinsicState 能够在各个对象之间共享
$factory = new FlyweightFactory();

$extrinsicState = 100;
$flA = $factory->getFlyweight('a');
$flA->operation(--$extrinsicState);

$flB = $factory->getFlyweight('b');
$flB->operation(--$extrinsicState);

$flC = $factory->getFlyweight('c');
$flC->operation(--$extrinsicState);

$flD = new UnsharedConcreteFlyweight();
$flD->operation(--$extrinsicState);
```

