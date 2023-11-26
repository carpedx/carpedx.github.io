---
layout: wiki
title: 原型模式
cate1: design-pattern
cate2: 
description: 用原型实例指定创建对象的种类，并且通过拷贝这些原型创建新的对象
keywords: design-pattern
sorting: 8
---



**GoF定义：用原型实例指定创建对象的种类，并且通过拷贝这些原型创建新的对象**

------

- 原型模式看似就是复制了一个相同的对象，但是请注意，复制的时候，`__construct()` 方法并没有被调用。这也就带出了原型模式最大的一个特点：**减少创建对象时的开销**。

- 复制出来的对象中如果都是值类型的属性，我们可以任意修改，不会对原型产生影响。而如果有引用类型的变量，则需要在 `__clone()` 方法进行一些处理，否则修改了复制对象的引用变量中的内容，会对原型对象中的内容有影响。
- PHP 中的 `Cloneable` 接口用于在类中实现克隆功能。当一个类实现了 `Cloneable` 接口时，它可以使用`clone` 关键字来创建类的新实例，而无需调用构造函数。

------



PHP：

```php
<?php

// 定义原型
abstract class Prototype
{
    public $v = 'clone' . PHP_EOL;

    public function __construct()
    {
        echo 'create' . PHP_EOL;
    }

    // 模拟了__clone()这个方法
    // 其实这是PHP自带的一个魔术方法，根本是不需要我们去进行定义的，只需要在原型类中进行实现就可以了
    abstract public function __clone();
}

// 具体实现的原型1
class ConcretePrototype1 extends Prototype
{
    public function __clone()
    {
    }
}

// 具体实现的原型2
class ConcretePrototype2 extends Prototype
{
    public function __clone()
    {
    }
}

// 客户端
class Client
{
    public function operation()
    {
        $p1 = new ConcretePrototype1();
        $p2 = clone $p1;

        echo $p1->v;
        echo $p2->v;
        echo $p1 == $p2 ? "true" : "false", PHP_EOL;
        echo $p1 === $p2 ? "true" : "false", PHP_EOL;
    }
}

$c = new Client();
$c->operation();
