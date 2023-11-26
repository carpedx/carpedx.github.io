---
layout: wiki
title: 命令模式
cate1: design-pattern
cate2: 
description: 将一个请求封装为一个对象，从而使你可用不同的请求对客户进行参数化；对请求排队或记录请求日志，以及支持可撤消的操作
keywords: design-pattern
sorting: 9
---



**GoF定义：将一个请求封装为一个对象，从而使你可用不同的请求对客户进行参数化；对请求排队或记录请求日志，以及支持可撤消的操作**

------



- Laravel框架的事件调度机制中，除了观察者模式外，也很明显的能看出命令模式的影子

------



PHP：

```php
<?php

// 命令调用者
class Invoker
{
    public $command;
    
    public function __construct($command)
    {
        $this->command = $command;
    }

    public function exec()
    {
        $this->command->execute();
    }
}

// 命令接收者
class Receiver
{
    public $name;

    public function __construct($name)
    {
        $this->name = $name;
    }

    public function action()
    {
        echo $this->name . '命令执行了！', PHP_EOL;
    }
}

// 定义命令
abstract class Command
{
    protected $receiver;

    public function __construct(Receiver $receiver)
    {
        $this->receiver = $receiver;
    }

    abstract public function execute();
}
class ConcreteCommand extends Command
{
    public function execute()
    {
        $this->receiver->action();
    }
}

//接收命令
$receiverA = new Receiver('A');
$command = new ConcreteCommand($receiverA);
// 调用命令
$invoker = new Invoker($command);
$invoker->exec();
```

