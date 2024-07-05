---
layout: wiki
title: 命令模式
cate1: design-pattern
cate2: 
description: 也称为动作或者事务模式
keywords: design-pattern
sorting: 9
---



**GoF定义：也称为动作或者事务模式。将一个请求封装为一个对象，从而使你可用不同的请求对客户进行参数化；对请求排队或记录请求日志，以及支持可撤消的操作**

------

命令模式，也称为动作或者事务模式，很多教材会用饭馆来举例。作为顾客的我们是命令的下达者，服务员是这个命令的接收者，菜单是这个实际的命令，而厨师是这个命令的执行者。那么，这个模式解决了什么呢？当你要修改菜单的时候，只需要和服务员说就好了，她会转达给厨师，也就是说，我们实现了顾客和厨师的解耦。也就是调用者与实现者的解耦。当然，很多设计模式可以做到这一点，但是命令模式能够做到的是让一个命令接收者实现多个命令（服务员下单、拿酒水、上菜），或者把一条命令转达给多个实现者（热菜厨师、凉菜厨师、主食师傅）。这才是命令模式真正发挥的地方！！

------

- 这种多命令多执行者的实现，有点像**组合模式**的实现

- 在这种情况下，增加新的命令，即不会影响执行者，也不会影响客户。当有新的客户需要新的命令时，只需要增加命令和请求者即可。即使有修改的需求，也只是修改请求者。
- Laravel框架的事件调度机制中，除了观察者模式外，也很明显的能看出命令模式的影子

------



GoF类图：

<img src="/images/wiki/algorithm/design-pattern-command_step1.jpg"  />



PHP案例：

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



PHP案例优化：

- 解决了多个订单、多位厨师的问题，并且还顺便解决了如果下错命令了，进行撤销的问题

```php
<?php

class Invoker
{
    private $command = [];

    public function setCommand(Command $command)
    {
        $this->command[] = $command;
    }

    public function exec()
    {
        if(count($this->command) > 0){
            foreach ($this->command as $command) {
                $command->execute();
            }
        }
    }

    public function undo()
    {
        if(count($this->command) > 0){
            foreach ($this->command as $command) {
                $command->undo();
            }
        }
    }
}

abstract class Command
{
    protected $receiver;
    protected $state;
    protected $name;

    public function __construct(Receiver $receiver, $name)
    {
        $this->receiver = $receiver;
        $this->name = $name;
    }

    abstract public function execute();
}

class ConcreteCommand extends Command
{
    public function execute()
    {
        if (!$this->state || $this->state == 2) {
            $this->receiver->action();
            $this->state = 1;
        } else {
            echo $this->name . '命令正在执行，无法再次执行了！', PHP_EOL;
        }

    }
    
    public function undo()
    {
        if ($this->state == 1) {
            $this->receiver->undo();
            $this->state = 2;
        } else {
            echo $this->name . '命令未执行，无法撤销了！', PHP_EOL;
        }
    }
}

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
    public function undo()
    {
        echo $this->name . '命令撤销了！', PHP_EOL;
    }
}

// 准备执行者
$receiverA = new Receiver('A');
$receiverB = new Receiver('B');
$receiverC = new Receiver('C');

// 准备命令
$commandOne = new ConcreteCommand($receiverA, 'A');
$commandTwo = new ConcreteCommand($receiverA, 'B');
$commandThree = new ConcreteCommand($receiverA, 'C');

// 请求者
$invoker = new Invoker();
$invoker->setCommand($commandOne);
$invoker->setCommand($commandTwo);
$invoker->setCommand($commandThree);
$invoker->exec();
$invoker->undo();

// 新加一个单独的执行者，只执行一个命令
$invokerA = new Invoker();
$invokerA->setCommand($commandOne);
$invokerA->exec();

// 命令A已经执行了，再次执行全部的命令执行者，A命令的state判断无法生效
$invoker->exec();
```

