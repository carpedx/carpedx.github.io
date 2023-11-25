---
layout: wiki
title: 工厂方法模式
cate1: design-pattern
cate2: 
description: 工厂方法模式对比简单工厂来说，最核心的一点，其实就是将实现推迟到子类。
keywords: design-pattern
sorting: 2
---



**工厂方法模式对比简单工厂来说，最核心的一点，其实就是将实现推迟到子类。**

**GoF：定义一个用于创建对象的接口，让子类决定实例化哪一个类。Factory Method使一个类的实例化推迟到其子类。**

------

PHP实现：

```php
<?php

// 商品接口
interface Product{
    function show() : void;
}

// 商品实现类A
class ProductA implements Product{
    public function show() : void{
        echo "I'm A.\n";
    }
}

// 创建者抽象类
abstract class Factory{

    // 抽象工厂方法
    abstract protected function FactoryMethod() : Product;

    // 操作方法
    public function AnOperation() : Product{
        return $this->FactoryMethod();
    }
}

// 创建者实现类A
class ProductAFactory extends Factory{
    // 实现操作方法
    protected function FactoryMethod() : Product{
        return new ProductA();
    }
}

$factory = new ProductAFactory();
$product = $factory->AnOperation();
$product->show();
```

