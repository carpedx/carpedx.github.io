---
layout: wiki
title: 状态模式
cate1: design-pattern
cate2: 
description: 允许一个对象在其内部状态改变时改变它的行为
keywords: design-pattern
sorting: 22
---



**GoF定义：允许一个对象在其内部状态改变时改变它的行为。对象看起来似乎修改了它的类**

------

- 这里把状态的变化给封装到外部的实现类去了，并不是这个上下文或者目标类内部来进行状态的切换了
- 这个默认类图的例子过于简单，其实状态模式的真正目的是为了解决复杂的if嵌套问题的，把复杂的if嵌套条件放到一个个的外部状态类中去判断
- 适用于：一个对象的行为取决于它的状态，并且它的必须在运行时刻根据状态改变自己的行为；一个操作中含有大量的多分支条件语句，且这些分支依赖于该对象的状态；
- 状态模式的特点是：它将与特定状态相关的行为局部化；它使得状态转换显式化；State对象可以被共享；
- 常见于订单系统、会员系统、OA系统中，也就是流程中会出现各种状态变化的情况，都可以使用状态模式来进行整体的设计与架构

------



GoF类图：

<img src="/images/wiki/algorithm/design-pattern-state_step1.jpg"  />



PHP：

```php
<?php

/*
 * 一个上下文类，也可以看作是目标类，它的内部有一个状态对象。当调用Request()的时候，去调用状态类的Handle()方法。目的是当前上下文类状态的变化都由外部的这个状态类来进行操纵。
 */

class Context
{
    private $state;
    public function SetState(State $state): void
    {
        $this->state = $state;
    }
    public function Request(): void
    {
        $this->state = $this->state->Handle();
    }
}

/*
 * 抽象状态接口及两个具体实现。这两个具体实现实际上是在相互调用。实现的效果就是上下文类每调用一次Request()方法，内部的状态类就变成别一个状态。就像一个开关，在打开与关闭中来回切换一样。
 */

interface State
{
    public function Handle(): State;
}

class ConcreteStateA implements State
{
    public function Handle(): State
    {
        echo '当前是A状态', PHP_EOL;
        return new ConcreteStateB();
    }
}

class ConcreteStateB implements State
{
    public function Handle(): State
    {
        echo '当前是B状态', PHP_EOL;
        return new ConcreteStateA();
    }
}


$c = new Context();
$stateA = new ConcreteStateA();
$c->SetState($stateA);
$c->Request();
$c->Request();
$c->Request();
$c->Request();
```

