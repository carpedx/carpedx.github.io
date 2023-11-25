---
layout: wiki
title: 抽象工厂模式
cate1: design-pattern
cate2: 
description: 与工厂方法模式不同的是抽象工厂里面不仅仅只返回一个对象，而是返回一堆。
keywords: design-pattern
sorting: 3
---

**与工厂方法模式不同的是抽象工厂里面不仅仅只返回一个对象，而是返回一堆。**

***GoF定义：提供一个创建一系列相关或相互依赖对象的接口，而无需指定它们具体的类。***

------

PHP实现：

```php
<?php

// 商品接口A
interface Product{
    function show() : void;
}

class ProductA implements Product{
    public function show() : void{
        echo "I'm Product A.\n";
    }
}

// 商品接口B
interface Goods{
    function show() : void;
}

class GoodsA implements Goods{
    public function show() : void{
        echo "I'm Goods A.\n";
    }
}

// 创建工厂类
interface Factory{
    public function createProduct();
    public function createGoods();
}

// 创建实现类
class AFactory implements Factory{
    public function createProduct(){
        return new ProductA();
    }
    public function createGoods(){
        return new GoodsA();
    }
}

$factory = new AFactory();
$product = $factory->createProduct();
$goods = $factory->createGoods();
$product->show();
$goods->show();
```

