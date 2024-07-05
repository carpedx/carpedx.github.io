---
layout: wiki
title: 门面模式
cate1: design-pattern
cate2: 
description: 为子系统中的一组接口提供一个一致的界面
keywords: design-pattern
sorting: 19
---



**GoF定义：为子系统中的一组接口提供一个一致的界面，Facade模式定义了一个高层接口，这个接口使得这一子系统更加容易使用**

------

- 门面模式就是这么的简单，而且只要是真实的在项目中做过开发的朋友一定在不知不觉中就已经使用过这个模式了
- 当你需要为一个复杂子系统提供一个简单的接口时，门面模式就非常适用。同时，如果客户程序与抽象类的实现部分之间存在着很大的依赖性时，也可以引入门面模式来进行解耦，提高子系统的独立性和可维护性。另外就是你需要构建一个层次结构的子系统时，门面可以充当每层子系统的入口点
- Laravel中的门面系统相信使用过框架的人一定都用过，比如：Cache::put()。在Laravel中，门面的实现使用了一个魔术方法__callStatic()。然后让对象的方法可以实现直接使用静态方法来进行调用。是不是很神奇。有兴趣的朋友可以翻翻源码，就在/Illuminate/Support/Facades/Facade.php中。
- 划重点：三层结构或者MVC也是门面模式的体现哦。上面说了，门面模式适合分层子系统的维护。而三层结构、MVC、MVP、MVVM这些货，本质上都是为了分层，而分层的目的，就是为了降低系统的复杂性。

------



GoF类图：

<img src="/images/wiki/algorithm/design-pattern-facade_step1.jpg"  />



PHP：

```php
<?php

/*
 * 定义四个或者N多个子系统，这个没什么好说的吧，可以想象是很多子系统，而且他们之间并不一定和这四个子系统一样的相似，有可能是千差万别的。
 */

class SubSystemOne
{
    public function MethodOne()
    {
        echo '子系统方法一', PHP_EOL;
    }
}
class SubSystemTwo
{
    public function MethodTwo()
    {
        echo '子系统方法二', PHP_EOL;
    }
}
class SubSystemThree
{
    public function MethodThree()
    {
        echo '子系统方法三', PHP_EOL;
    }
}
class SubSystemFour
{
    public function MethodFour()
    {
        echo '子系统方法四', PHP_EOL;
    }
}

/*
 * 通过门面类将这些子系统包装起来，对外提供的只是门面新定义的方法。
 */

class Facade
{

    private $subStytemOne;
    private $subStytemTwo;
    private $subStytemThree;
    private $subStytemFour;
    public function __construct()
    {
        $this->subSystemOne = new SubSystemOne();
        $this->subSystemTwo = new SubSystemTwo();
        $this->subSystemThree = new SubSystemThree();
        $this->subSystemFour = new SubSystemFour();
    }

    public function MethodA()
    {
        $this->subSystemOne->MethodOne();
        $this->subSystemTwo->MethodTwo();
    }
    public function MethodB()
    {
        $this->subSystemOne->MethodOne();
        $this->subSystemTwo->MethodTwo();
        $this->subSystemThree->MethodThree();
        $this->subSystemFour->MethodFour();
    }
}

/*
 * 客户端调用
 */

$facade = new Facade();
$facade->MethodA();
$facade->MethodB();
```

