---
layout: wiki
title: 组合模式
cate1: design-pattern
cate2: 
description: 将对象组合成树形结构以表示“部分-整体”的层次结构
keywords: design-pattern
sorting: 14
---



**GoF定义：将对象组合成树形结构以表示“部分-整体”的层次结构，使得用户对单个对象和组合对象的使用具有一致性**

------

- 从来代码来看，完全就是一颗树的实现
- 所有的子节点和叶子节点都可以处理数据，但叶子节点为终点
- 你希望用户可以忽略组合对象与单个对象的不同，统一地使用组合结构中的所有对象时，就应该考虑使用组合模式
- 用户不用关心到底是处理一个叶节点还是处理一个组合组件，也就用不着为定义组合而写一些选择判断语句了
- 组合模式可以让客户一致性地使用组合结构和单个对象

------



GoF类图：

<img src="/images/wiki/algorithm/design-pattern-combination_step1.jpg"  />



PHP：

```php
<?php

/*
 * 抽象出来的组合节点声明，在适当情况下实现所有类的公共接口的缺省行为，是所有子节点的父类。
 */

abstract class Component
{
    protected $name;

    public function __construct($name){
        $this->name = $name;
    }

    abstract public function Operation(int $depth);

    abstract public function Add(Component $component);

    abstract public function Remove(Component $component);
}

class Composite extends Component
{
    private $componentList;

    public function Operation(int $depth)
    {
        echo str_repeat('-', $depth) . $this->name . PHP_EOL;
        foreach ($this->componentList as $component) {
            $component->Operation($depth + 2);
        }
    }

    public function Add(Component $component)
    {
        $this->componentList[] = $component;
    }

    public function Remove(Component $component)
    {
        $position = 0;
        foreach ($this->componentList as $child) {
            ++$position;
            if ($child == $component) {
                array_splice($this->componentList, ($position), 1);
            }
        }
    }

    public function GetChild(int $i)
    {
        return $this->componentList[$i];
    }
}

/*
 * 具体的节点实现类，保存下级节点的引用，定义实际的节点行为。
 */

class Leaf extends Component
{
    public function Add(Component $c)
    {
        echo 'Cannot add to a leaf' . PHP_EOL;
    }

    public function Remove(Component $c)
    {
        echo 'Cannot remove from a leaf' . PHP_EOL;
    }

    public function Operation(int $depth)
    {
        echo str_repeat('-', $depth) . $this->name . PHP_EOL;
    }
}


$root = new Composite("root");
$root->Add(new Leaf("Leaf A"));
$root->Add(new Leaf("Leaf B"));

$comp = new Composite("Composite X");
$comp->Add(new Leaf("Leaf XA"));
$comp->Add(new Leaf("Leaf XB"));

$root->Add($comp);

$comp2 = new Composite("Composite XY");
$comp2->Add(new Leaf("Leaf XYA"));
$comp2->Add(new Leaf("Leaf XYB"));

$comp->Add($comp2);

$root->Add(new Leaf("Leaf C"));

$leaf = new Leaf("Leaf D");
$root->Add($leaf);
$root->remove($leaf);

$root->Operation(1);
```

