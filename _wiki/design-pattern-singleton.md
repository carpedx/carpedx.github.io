---
layout: wiki
title: 单例模式
cate1: design-pattern
cate2: 
description: 保证一个类仅有一个实例，并提供一个访问它的全局访问点
keywords: design-pattern
sorting: 21
---



**GoF定义：保证一个类仅有一个实例，并提供一个访问它的全局访问点**

------

- 单例最大的用途就是让我们的对象全局唯一。
- 那么全局唯一有什么好处呢？有些类的创建开销很大，而且并不需要我们每次都使用新的对象，完全可以一个对象进行复用，它们并没有需要变化的属性或状态，只是提供一些公共服务。比如数据库操作类、网络请求类、日志操作类、配置管理服务等等
- 曾经有过面试官问过，单例在PHP中到底是不是唯一的？如果在一个进程下，也就是一个fpm下，当然是唯一的。nginx同步拉起的多个fpm中那肯定就不是唯一的！
- 单例模式的优点：对唯一实例的受控访问；缩小命名空间；允许对操作和表示的精化；允许可变数目的实例；比类操作更灵活。
- Laravel中在IoC容器部分使用了单例模式。关于容器部分的内容我们会在将来的Laravel系列文章中讲解。我们可以在Illuminate\Container\Container类中找到singleton方法。它调用了bind方法中的getClosure方法。继续追踪会发现他们最终会调用Container的make或build方法来进行实例化类，不管是make还是build方法，他们都会有单例的判断，也就是判断类是否被实例化过或者在容器中已存在。build中的if (!$reflector->isInstantiable())。

------



GoF类图：

<img src="/images/wiki/algorithm/design-pattern-singleton_step1"  />



PHP：

```php
<?php

/*
 * 让静态变量保存实例化后的自己。当需要这个对象的时候，调用GetInstance()方法获得全局唯一的一个对象。
 */

class Singleton
{
    private static $uniqueInstance;
    private $singletonData = '单例类内部数据';

    private function __construct()
    {
        // 构造方法私有化，外部不能直接实例化这个类
    }

    public static function GetInstance()
    {
        if (self::$uniqueInstance == null) {
            self::$uniqueInstance = new Singleton();
        }
        return self::$uniqueInstance;
    }

    public function SingletonOperation(){
        $this->singletonData = '修改单例类内部数据';
    }

    public function GetSigletonData()
    {
        return $this->singletonData;
    }

}

$singletonA = Singleton::GetInstance();
echo $singletonA->GetSigletonData(), PHP_EOL;

$singletonB = Singleton::GetInstance();

if ($singletonA === $singletonB) {
    echo '相同的对象', PHP_EOL;
}
$singletonA->SingletonOperation(); // 这里修改的是A
echo $singletonB->GetSigletonData(), PHP_EOL;
```

