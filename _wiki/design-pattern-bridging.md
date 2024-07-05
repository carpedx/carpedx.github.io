---
layout: wiki
title: 桥接模式
cate1: design-pattern
cate2: 
description: 将抽象部分与它的实现部分分离，使它们都可以独立地变化
keywords: design-pattern
sorting: 18
---



**GoF定义：将抽象部分与它的实现部分分离，使它们都可以独立地变化**

------

- 在源码解释中，我们会发现，这个模式和适配器模式非常相似。但是，适配器的目的是为了帮助两个不太相关的类，让它们能够协同工作，实现中间转换工作。而桥接则是为了让方法的行为解除继承耦合，方便地添加、修改，动态调用行为，让抽象接口和实现部分可以独立进行改变
- 让抽象接口和实现部分可以独立进行改变的意思是，只要维护了实现接口的引用，我们的实现接口的具体实现类可以是完全不同的类，里面有不同的功能，并且可以任意改变。让实现来自己决定它自己是什么。
- 桥接模式的优点：分享接口及其实现部分、提高可扩充性、实现细节对客户透明
- 桥接模式最主要解决的问题就是继承的不断增长而带来的紧耦合问题
- 组合与聚合：聚合是弱关系，A可以包含B，但B不是A的一部分；组合是强关系，A包含B，B也是A的一部分，整体和部分的关系

------



GoF类图：

<img src="/images/wiki/algorithm/design-pattern-bridging_step1.jpg"  />



PHP：

```php
<?php

/*
 * 我们先来定义实现接口以及它们具体的实现，也就是真正要执行的功能。就像是适配器模式中的Adaptee。
 */

interface Implementor
{
    public function OperationImp();
}

class ConcreteImplementorA implements Implementor
{
    public function OperationImp()
    {
        echo '具体实现A', PHP_EOL;
    }
}

class ConcreteImplementorB implements Implementor
{
    public function OperationImp()
    {
        echo '具体实现B', PHP_EOL;
    }
}

/*
 * 定义抽象类的接口，并维护一个对实现的引用。具体的抽象类的实现方法中，我们直接调用实现接口的真实操作方法。类似于适配器中的Adapter。
 */

abstract class Abstraction
{
    protected $imp;

    public function SetImplementor(Implementor $imp)
    {
        $this->imp = $imp;
    }
    abstract public function Operation();
}

class RefinedAbstraction extends Abstraction
{
    public function Operation()
    {
        $this->imp->OperationImp();
    }
}

$impA = new ConcreteImplementorA();
$impB = new ConcreteImplementorB();

$ra = new RefinedAbstraction();

$ra->SetImplementor($impA);
$ra->Operation();

$ra->SetImplementor($impB);
$ra->Operation();
```

