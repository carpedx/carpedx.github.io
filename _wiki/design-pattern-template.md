---
layout: wiki
title: 模板方法模式
cate1: design-pattern
cate2: 
description: 定义一个操作中的算法的骨架，而将一些步骤延迟到子类中
keywords: design-pattern
sorting: 20
---



**GoF定义：定义一个操作中的算法的骨架，而将一些步骤延迟到子类中。TemplateMethod使得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤**

------

- 模板方法模式相信只要是做过一点面向对象开发的朋友都会多多少少使用过。因为真的非常常见
- 一些框架中经常会有某些功能类有初始化的功能，在初始化的函数中都会调用很多内部的其他函数，这其实也是一种模板方法模式的应用
- 模板方法模式可以很方便的实现钩子函数。就像很多模板或者开源系统中给你准备好的钩子函数。比如某些博客开源程序会预留一些广告位或者特殊位置的钩子函数让使用者自己按需实现
- 模板方法模式适用于：一次性实现一个算法中不变的部分，并将可变的部分留给子类来实现；将子类中公共的行为提取出来并集中到父类中；控制子类的扩展；

------



GoF类图：

<img src="/images/wiki/algorithm/design-pattern-template_step1.jpg"  />



PHP：

```php
<?php

/*
 * 定义一个抽象类，有一个模板方法TemplateMethod()，这个方法中我们对算法操作方法进行调用。而这些算法抽象方法是在子类中去实现的。
 */

abstract class AbstractClass
{
    public function TemplateMethod()
    {
        $this->PrimitiveOperation1();
        $this->PrimitiveOperation2();
    }

    abstract public function PrimitiveOperation1();
    abstract public function PrimitiveOperation2();
}

/*
 * 具体的实现类，它们只需要去实现父类所定义的算法就可以了。
 */

class ConcreteClassA extends AbstractClass
{
    public function PrimitiveOperation1()
    {
        echo '具体类A实现方法1', PHP_EOL;
    }
    public function PrimitiveOperation2()
    {
        echo '具体类A实现方法2', PHP_EOL;
    }
}

class ConcreteClassB extends AbstractClass
{
    public function PrimitiveOperation1()
    {
        echo '具体类B实现方法1', PHP_EOL;
    }
    public function PrimitiveOperation2()
    {
        echo '具体类B实现方法2', PHP_EOL;
    }
}

$c = new ConcreteClassA();
$c->TemplateMethod();

$c = new ConcreteClassB();
$c->TemplateMethod();
```

