---
layout: wiki
title: 备忘录模式
cate1: design-pattern
cate2: 
description: 在不破坏封装性的前提下，捕获一个对象的内部状态，并在该对象之外保存这个状态
keywords: design-pattern
sorting: 17
---



**GoF定义：在不破坏封装性的前提下，捕获一个对象的内部状态，并在该对象之外保存这个状态。这样以后就可将该对象恢复到原先保存的状态**

------

- 备忘录模式说白了就是让一个外部类B来保存A的内部状态，然后在适当的时候可以方便的还原这个状态。
- 备忘录模式的应用场景其实非常多，浏览器的回退、数据库的备份还原、操作系统的备份还原、文档的撤销重做、棋牌游戏的悔棋等等
- 这个模式能够保持对原发器的封装，也就是这些状态需要对外部的对象隐藏，所以只能交给一个备忘录对象来记录
- 状态在原发器和备忘录之间的拷贝可能带来性能问题，特别是大型对象的复杂繁多的内部状态，而且也会带来一些编码方面的漏洞，比如漏掉某些状态

------



GoF类图：

<img src="/images/wiki/algorithm/design-pattern-memorandum_step1.jpg"  />



PHP：

```php
<?php

/*
 * 原发器，也可以叫做发起人。它有一个内部状态（state），这个状态可以在不同的情况下进行改变。当某一个事件发生时，需要将这个状态恢复到原先的状态。在这里，我们有一个CreateMemento()用于创建一个备忘录（存档），有一个SetMeneto()用于还原状态（读档）。
 */

class Originator
{
    private $state;
    public function SetMeneto(Memento $m)
    {
        $this->state = $m->GetState();
    }
    public function CreateMemento()
    {
        $m = new Memento();
        $m->SetState($this->state);
        return $m;
    }

    public function SetState($state)
    {
        $this->state = $state;
    }

    public function ShowState()
    {
        echo $this->state, PHP_EOL;
    }
}

/*
 * 备忘录，非常简单，就是用于记录状态。将这个状态以对象的形式保存，就可以让原发器非常方便地创建很多存档用于记录各种不同的状态。
 */

class Memento
{
    private $state;
    public function SetState($state)
    {
        $this->state = $state;
    }
    public function GetState()
    {
        return $this->state;
    }
}

/*
 * 负责人，也叫做管理者类，保存备忘录，当需要的时候从这里取出备忘录。它只负责保存，不能修改备忘录。在复杂的应用中，可以将这里做成列表，就像游戏中可以选择性的展现多条存档记录供玩家选择。
 */

class Caretaker
{
    private $memento;
    public function SetMemento($memento)
    {
        $this->memento = $memento;
    }
    public function GetMemento()
    {
        return $this->memento;
    }
}

$o = new Originator();
$o->SetState('状态1');
$o->ShowState();

// 保存状态
$c = new Caretaker();
$c->SetMemento($o->CreateMemento());

$o->SetState('状态2');
$o->ShowState();

// 还原状态
$o->SetMeneto($c->GetMemento());
$o->ShowState();
```

