---
layout: wiki
title: 组合模式
cate1: design-pattern
cate2: 
description: 组合模式
keywords: design-pattern
sorting: 14
---



**GoF定义：将对象组合成树形结构以表示“部分-整体”的层次结构，使得用户对单个对象和组合对象的使用具有一致性**

------



- 组合模式最大的特点就是可以让叶子节点或者子节点无限的组合和延伸。能够形成各种不同的组合方式，但又能保证万变不离其宗。让整个递归是在可控的范围内进行

------



PHP：

```php
<?php

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

