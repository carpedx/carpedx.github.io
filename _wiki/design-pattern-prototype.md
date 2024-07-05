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

- 基于上述特点，我们可以快速的复制大量相同的对象，比如要给一个数组中塞入大量相同的对象时。

- 复制出来的对象中如果都是值类型的属性，我们可以任意修改，不会对原型产生影响。而如果有引用类型的变量，则需要在 `__clone()` 方法进行一些处理，否则修改了复制对象的引用变量中的内容，会对原型对象中的内容有影响。
- PHP 中的 `Cloneable` 接口用于在类中实现克隆功能。当一个类实现了 `Cloneable` 接口时，它可以使用`clone` 关键字来创建类的新实例，而无需调用构造函数。

------



GoF类图：

<img src="/images/wiki/algorithm/design-pattern-prototype_step1.jpg"  />



PHP：

```php
<?php

/*
 * 首先我们通过模拟的方式定义了一个原型，这里主要是模拟了__clone()这个方法。其实这是PHP自带的一个魔术方法，根本是不需要我们去进行定义的，只需要在原型类中进行实现就可以了。当外部使用clone关键字进行对象克隆时，直接就会进入这个魔术方法中。在这个魔术方法里面我们可以对属性进行处理，特别是针对引用属性进行一些独特的处理。在这个例子中，我们只使用了一个值类型的变量。无法体现出引用类型的问题，我们将在后面的实例中演示对引用类型变量的处理。
 */

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

/*
 * 模拟的具体实现的原型，其实就是主要去具体的实现__clone()方法。后面我们看具体的例子时再说明。
 */

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
