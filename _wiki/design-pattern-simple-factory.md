---
layout: wiki
title: 简单工厂模式
cate1: design-pattern
cate2: 
description: 简单工厂，也称静态工厂，不属于GoF23种设计模式。但是可以说是所有的设计模式中最容易理解的。
keywords: design-pattern
sorting: 1
---



**简单工厂，也称静态工厂，不属于GoF23种设计模式。但是可以说是所有的设计模式中最容易理解的。**

------

PHP实现：

```php
<?php

// Products
interface Product
{
    public function show();
}

class ProductA implements Product
{
    public function show()
    {
        echo 'Show ProductA';
    }
}

class ProductB implements Product
{
    public function show()
    {
        echo 'Show ProductB';
    }
}

// Factory
class Factory
{
    public static function createProduct(string $type) : Product
    {
        $product = null;
        switch ($type) {
            case 'A':
                $product = new ProductA();
                break;
            case 'B':
                $product = new ProductB();
                break;
        }
        return $product;
    }
}

$productA = Factory::createProduct('A');
$productB = Factory::createProduct('B');
$productA->show();
$productB->show();
```

