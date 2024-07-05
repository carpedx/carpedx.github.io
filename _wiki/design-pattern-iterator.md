---
layout: wiki
title: 迭代器模式
cate1: design-pattern
cate2: 
description: 提供一种方法顺序访问一个聚合对象中各个元素，而又不需暴露该对象的内部表示
keywords: design-pattern
sorting: 7
---



**GoF定义：提供一种方法顺序访问一个聚合对象中各个元素，而又不需暴露该对象的内部表示**

------

- PHP 中的 `Iterator` 接口已经为我们准备好了一套标准的 `Iterator` 模式的实现，而且实现这个接口的类可以用 `foreach` 来遍历

------



GoF类图：

<img src="/images/wiki/algorithm/design-pattern-iterator_step1.jpg"  />



PHP：

```php
<?php

// 迭代器接口
interface MsgIterator
{
    public function First();		// 第一个元素
    public function Next();			// 下一个元素
    public function IsDone();		// 是否遍历完
    public function CurrentItem();	// 当前元素信息
}

// 正向迭代器
class MsgIteratorAsc implements MsgIterator
{
    private $list;
    private $index;
    public function __construct($list)
    {
        $this->list = $list;
        $this->index = 0;
    }
    public function First()
    {
        $this->index = 0;
    }
    public function Next()
    {
        $this->index++;
    }
    public function IsDone()
    {
        return $this->index >= count($this->list);
    }
    public function CurrentItem()
    {
        return $this->list[$this->index];
    }
}

// 反向迭代器
class MsgIteratorDesc implements MsgIterator
{
    private $list;
    private $index;
    public function __construct($list)
    {
        // 反转数组
        $this->list = array_reverse($list);
        $this->index = 0;
    }
    public function First()
    {
        $this->index = 0;
    }
    public function Next()
    {
        $this->index++;
    }
    public function IsDone()
    {
        return $this->index >= count($this->list);
    }
    public function CurrentItem()
    {
        return $this->list[$this->index];
    }
}

// 聚合对象 用来创建迭代器
interface Message
{
    public function CreateIterator($list);
}
class MessageAsc implements Message
{
    public function CreateIterator($list)
    {
        return new MsgIteratorAsc($list);
    }
}
class MessageDesc implements Message
{
    public function CreateIterator($list)
    {
        return new MsgIteratorDesc($list);
    }
}

// 要排序的号码列表
$mobileList = [
    '13111111111',
    '13111111112',
    '13111111113',
    '13111111114',
    '13111111115',
    '13111111116',
    '13111111117',
    '13111111118',
];

// 正向
$serverA = new MessageAsc();
$iteratorA = $serverA->CreateIterator($mobileList);

while (!$iteratorA->IsDone()) {
    echo $iteratorA->CurrentItem(), PHP_EOL;
    $iteratorA->Next();
}

// 反向
$serverB = new MessageDesc();
$iteratorB = $serverB->CreateIterator($mobileList);

while (!$iteratorB->IsDone()) {
    echo $iteratorB->CurrentItem(), PHP_EOL;
    $iteratorB->Next();
}
```

