---
layout: wiki
title: 策略模式
cate1: design-pattern
cate2: 
description: 定义一系列的算法，把它们一个个封装起来，并且使它们可以相互替换。本模式使得算法可独立于使用它的客户而变化
keywords: design-pattern
sorting: 10
---



**GoF定义：定义一系列的算法，把它们一个个封装起来，并且使它们可以相互替换。本模式使得算法可独立于使用它的客户而变化**

> 相关链接：[简单工厂模式](https://carpedx.com/wiki/design-pattern-simple-factory/)

------



- 工厂相关的模式属于创建型模式，顾名思义，这种模式是用来创建对象的。而策略模式属性行为型模式，通过执行上下文，将要调用的函数方法封装了起来，客户端只需要调用执行上下文的方法就可以了
- 策略模式可以优化单元测试，因为每个算法都有自己的类，所以可以通过自己的接口单独测试

------



PHP：

```php
<?php

// 定义算法抽象及实现
interface Strategy{
    function AlgorithmInterface();
}
class ConcreteStrategyA implements Strategy{
    function AlgorithmInterface(){
        echo "算法A";
    }
}
class ConcreteStrategyB implements Strategy{
    function AlgorithmInterface(){
        echo "算法B";
    }
}

// 定义执行环境上下文
class Context{
    private $strategy;
    function __construct(Strategy $s){
        $this->strategy = $s;
    }
    function ContextInterface(){
        $this->strategy->AlgorithmInterface();
    }
}

$strategyA = new ConcreteStrategyA();
$context = new Context($strategyA);
$context->ContextInterface();

$strategyB = new ConcreteStrategyB();
$context = new Context($strategyB);
$context->ContextInterface();
```

