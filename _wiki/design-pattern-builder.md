---
layout: wiki
title: 建造者模式
cate1: design-pattern
cate2: 
description: 将一个复杂对象的构建与它的表示分离
keywords: design-pattern
sorting: 16
---



**GoF定义：将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示**

------

- 其实这个模式要解决的最主要问题就是一个类可能有非常多的配置、属性，这些配置、属性也并不全是必须要配置的，一次性的实例化去配置这些东西非常麻烦。这时就可以考虑让这些配置、属性变成一个一个可随时添加的部分。通过不同的属性组合拿到不同的对象。
- 上面那一条，在GoF那里的原文是：它使你改变一个产品的内部表示；它将构造代码和表示代码分开；它使你可以对构造过程进行更精细的控制。
- 再说得简单一点，对象太复杂，我们可以一部分一部分的拼装它！
- 了解过一点Android开发的一定不会陌生，创建对话框对象AlterDialog.builder
- Laravel中，数据库组件也使用了建造者模式，你可以翻看下源码中Database\Eloquent和Database\Query目录中是否都有一个Builder.php

------



GoF类图：

<img src="/images/wiki/algorithm/design-pattern-builder_step1.jpg"  />



PHP：

```php
<?php

/*
 * 产品类，你可以把它想象成我们要建造的房子。这时的房子还没有任何内容，我们需要给它添砖加瓦。
 */

class Product
{
    private $parts = [];

    public function Add(String $part): void
    {
        $this->parts[] = $part;
    }

    public function Show(): void
    {
        echo PHP_EOL . '产品创建 ----', PHP_EOL;
        foreach ($this->parts as $part) {
            echo $part, PHP_EOL;
        }
    }
}

/*
 * 建造者抽象及其实现。不同的开发商总会选用不同的品牌材料，这里我们有了两个不同的开发商，但他们的目的一致，都是为了去盖房子（Product）。
 */

interface Builder
{
    public function BuildPartA(): void;
    public function BuildPartB(): void;
    public function GetResult(): Product;
}

class ConcreteBuilder1 implements Builder
{
    private $product;

    public function __construct()
    {
        $this->product = new Product();
    }

    public function BuildPartA(): void
    {
        $this->product->Add('部件A');
    }
    public function BuildPartB(): void
    {
        $this->product->Add('部件B');
    }
    public function GetResult(): Product
    {
        return $this->product;
    }
}

class ConcreteBuilder2 implements Builder
{
    private $product;

    public function __construct()
    {
        $this->product = new Product();
    }

    public function BuildPartA(): void
    {
        $this->product->Add('部件X');
    }
    public function BuildPartB(): void
    {
        $this->product->Add('部件Y');
    }
    public function GetResult(): Product
    {
        return $this->product;
    }
}

/*
 * 构造器，用来调用建造者进行生产。没错，就是我们的工程队。它来选取材料并进行建造。同样的工程队，可以接不同的单，但盖出来的都是房子。只是这个房子的材料和外观不同，大体上的手艺还是共通的。
 */

class Director
{
    public function Construct(Builder $builder)
    {
        $builder->BuildPartA();
        $builder->BuildPartB();
    }
}

$director = new Director();
$b1 = new ConcreteBuilder1();
$b2 = new ConcreteBuilder2();

$director->Construct($b1);
$p1 = $b1->getResult();
$p1->Show();

$director->Construct($b2);
$p2 = $b2->getResult();
$p2->Show();
```

