---
layout: wiki
title: 访问者模式
cate1: design-pattern
cate2: 
description: 访问者模式
keywords: design-pattern
sorting: 23
---



**GoF定义：表示一个作用于某对象结构中的各元素的操作。它使你可以在不改变各元素的类的前提下定义作用于这些元素的新操作**

------

- 让访问者调用指定的元素。这里需要注意的，访问者调用元素的行为一般是固定的，很少会改变的。也就是VisitConcreteElementA()、VisitConcreteElementB()这两个方法。也就是定义对象结构的类很少改变，但经常需要在此结构上定义新的操作时，会使用访问者模式
- 需要对一个对象结构中的对象进行很多不同的并且不相关的操作，而你想避免让这些操作“污染”这些对象的类时，适用于访问者模式
- 访问者模式适合数据结构不变化的情况。所以，它是一种平常你用不上，但一旦需要的时候就只能用这种模式的模式。GoF：“大多时候你并不需要访问者模式，但当一旦你需要访问者模式时，那就是真的需要它了”。因为很少有数据结构不发生变化的情况
- 访问者模式的一些优缺点：易于增加新的操作；集中相关的操作而分离无关的操作；增加新的ConcreteElement类很困难；通过类层次进行访问；累积状态；破坏封装

------



GoF类图：

<img src="/images/wiki/algorithm/design-pattern-visitor_step1.jpg"  />



PHP：

```php
<?php

/*
 * 抽象的访问者接口及两个具体实现。可以看作是一家小两口来我们家作客咯！
 */

interface Visitor
{
    public function VisitConcreteElementA(ConcreteElementA $a);
    function VisitConcreteElementB(ConcreteElementB $b);
}

class ConcreteVisitor1 implements Visitor
{
    public function VisitConcreteElementA(ConcreteElementA $a)
    {
        echo get_class($a) . "被" . get_class($this) . "访问", PHP_EOL;
    }
    public function VisitConcreteElementB(ConcreteElementB $b)
    {
        echo get_class($b) . "被" . get_class($this) . "访问", PHP_EOL;
    }
}

class ConcreteVisitor2 implements Visitor
{
    public function VisitConcreteElementA(ConcreteElementA $a)
    {
        echo get_class($a) . "被" . get_class($this) . "访问", PHP_EOL;
    }
    public function VisitConcreteElementB(ConcreteElementB $b)
    {
        echo get_class($b) . "被" . get_class($this) . "访问", PHP_EOL;
    }
}

/*
 * 元素抽象及实现，也可以看作是要访问的实体。当然就是我和我媳妇啦。
 */

interface Element
{
    public function Accept(Visitor $v);
}

class ConcreteElementA implements Element
{
    public function Accept(Visitor $v)
    {
        $v->VisitConcreteElementA($this);
    }
    public function OperationA()
    {

    }
}

class ConcreteElementB implements Element
{
    public function Accept(Visitor $v)
    {
        $v->VisitConcreteElementB($this);
    }
    public function OperationB()
    {

    }
}

/*
 * 这是一个对象结构，用于保存元素实体并进行访问调用。大家在客厅里见面，互相寒暄嘛，这里就是个客厅
 */

class ObjectStructure
{
    private $elements = [];

    public function Attach(Element $element)
    {
        $this->elements[] = $element;
    }

    public function Detach(Element $element)
    {
        $position = 0;
        foreach ($this->elements as $e) {
            if ($e == $element) {
                unset($this->elements[$position]);
                break;
            }
            $position++;
        }
    }

    public function Accept(Visitor $visitor)
    {
        foreach ($this->elements as $e) {
            $e->Accept($visitor);
        }
    }

}

$o = new ObjectStructure();
$o->Attach(new ConcreteElementA());
$o->Attach(new ConcreteElementB());

$v1 = new ConcreteVisitor1();
$v2 = new ConcreteVisitor2();

$o->Accept($v1);
$o->Accept($v2);
```

