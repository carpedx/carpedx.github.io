---
layout: post
title: Mockery简单使用
categories: php
description: Mockery简单使用
keywords: php
---



**Mockery 是一个PHP模拟对象框架，可用于PHPUnit、PHPSpec或其他测试框架的单元测试。**

PHP-CS-Fixer：[官方GitHub](https://github.com/PHP-CS-Fixer/PHP-CS-Fixer)，[官方文档](https://cs.symfony.com/)

------



## 安装

#### 使用composer安装Mockery

composer.json：

```
{
    "require-dev": {
        "mockery/mockery": "dev-master"
    }
}
```

安装命令：

```
php composer.phar update --no-dev
```

其他安装方法是直接从 Composer 命令行安装，如下：

```
php composer.phar require --dev mockery/mockery
```



## 使用

### 创建测试替身

#### Mock

创建的模拟对象

```
$mock = \Mockery::mock('foo');	// 创建名为foo的模拟对象
$mock = \Mockery::mock();	// 创建没有名称的模拟对象
$mock = \Mockery::mock('MyClass');	// 基于现有类创建模拟对象
$mock = \Mockery::mock('MyInterface');	// 基于现有接口创建模拟对象

// 这个模拟对象现在将是类型MyClass并实现MyInterface和OtherInterface接口
$mock = \Mockery::mock('MyClass, MyInterface, OtherInterface');	
// 也可以通过第二个参数来告诉所需的接口
$mock = \Mockery::mock('MyClass', 'MyInterface, OtherInterface');
```



#### Spies

```
// 创建Spies和创建Mock一样
$spy = \Mockery::spy('MyClass');
$spy = \Mockery::spy('MyClass, MyInterface, OtherInterface');
```

`Spies` 和 `Mock` 之间的主要区别在于，使用 `Spies` ，我们在事后验证调用是否发生。而使用 `Mock` ，我们在调用本身之前就设置了调用期望，并得到了我们期望它返回的返回结果。

**Spies 与 Mock 的区别案例：**

```
$mock = \Mockery::mock('MyClass');
$spy = \Mockery::spy('MyClass');

$mock->shouldReceive('foo')->andReturn(42);

$mockResult = $mock->foo();
$spyResult = $spy->foo();

$spy->shouldHaveReceived()->foo();

var_dump($mockResult); // int(42)
var_dump($spyResult); // null
```



#### Runtime partial test doubles

当涉及到"Runtime partial test doubles"（运行时部分测试替身）时，我们正在讨论创建一种测试代用对象（test double），然后要求它将自己设置为部分替身。对于那些尚未被告知允许或期望的方法调用，这个代用对象会像正常对象实例上的调用一样执行。

代码示例：

```
class Foo {
    function foo() { return 123; }
    function bar() { return $this->foo(); }
}

$foo = mock(Foo::class)->makePartial();
$foo->foo(); // int(123);
```

在这个示例中，我们创建了一个名为Foo的类。我们用`mock`函数创建了一个名为`$foo`的代用对象，并通过`makePartial`方法将其设置为部分替身。然后，我们调用了`$foo->foo()`，它会返回123，就像调用一个正常实例的方法一样。

之后，我们可以通过以下方式处理这个代用对象，就像处理任何其他Mockery代用对象一样：

```
$foo->shouldReceive('foo')->andReturn(456);
$foo->bar(); // int(456)
```

这里我们告诉代用对象，期望调用`foo`方法，并返回456。然后当我们调用`$foo->bar()`时，它会返回456，而不是之前的123。



#### Generated partial test doubles

我们可以创建的第二种部分替身类型被称为“生成的部分替身”。对于生成的部分替身，我们明确地告诉Mockery我们希望允许或期望调用哪些方法。所有其他方法将直接运行实际的代码，因此对这些方法的存根和期望将不起作用。

代码示例：

```
class Foo {
    function foo() { return 123; }
    function bar() { return $this->foo(); }
}

$foo = mock("Foo[foo]");

$foo->foo(); // 错误，没有设置期望

$foo->shouldReceive('foo')->andReturn(456);
$foo->foo(); // int(456)

// 对此设置期望没有效果
$foo->shouldReceive('bar')->andReturn(999);
$foo->bar(); // int(456)
```

在这个示例中，我们创建了一个名为Foo的类。我们使用字符串`"Foo[foo]"`创建了一个代用对象，指定了我们希望允许或期望调用`foo`方法。所有其他方法将直接运行实际的代码，所以在没有设置期望的情况下调用`$foo->foo()`会导致错误。然后，我们告诉代用对象，期望调用`foo`方法，并返回456。当我们调用`$foo->foo()`时，它会返回456。然而，对于`bar`方法的期望设置没有效果，因为`bar`方法没有在生成的部分替身中指定。

另外，我们还可以使用`!method`语法明确指定要直接运行的方法：

```
class Foo {
    function foo() { return 123; }
    function bar() { return $this->foo(); }
}

$foo = mock("Foo[!foo]");

$foo->foo(); // int(123)

$foo->bar(); // 错误，没有设置期望
```

在这个示例中，我们明确地告诉代用对象只有`foo`方法会直接运行，因此调用`$foo->foo()`会返回123，而对`bar`方法的调用将导致错误，因为我们没有为其设置期望。



#### Proxied partial test doubles

代理部分模拟是一种最后的选择。我们可能会遇到一个被标记为final的类，因此无法被模拟。同样地，我们可能会遇到一个具有被标记为final的方法的类。在这种情况下，我们不能简单地扩展这个类并重写方法来进行模拟 - 我们需要有创意的方法。

```
$mock = \Mockery::mock(new MyClass);
```

是的，新的模拟是一个代理。它拦截调用并将其重新路由到代理对象（我们构造并传递进去的对象）上，对于那些不受任何期望控制的方法。间接地说，这使我们能够模拟那些被标记为final的方法，因为代理不受这些限制。这种权衡是显而易见的 - 代理部分模拟会在类的类型提示检查中失败，因为它无法扩展那个类。



#### 类别名（Aliasing）

在类的名称前面加上“alias:”前缀将生成“alias mock”

```
$mock = \Mockery::mock('alias:MyClass');
```



#### 重载（Overloading）

在类的名称前面加上“overload:”，将生成一个与别名模拟一样，但是创建的这个类的新实例将会导入在原始模拟（$mock）上设置的任何期望。原始模拟永远不会被验证，因为它被用作新实例的期望存储。

换句话说，当创建一个被模拟类的新实例时，实例模拟将会“拦截”，然后代替真实的实例。这在模拟硬依赖关系时特别有用

```
$mock = \Mockery::mock('overload:MyClass');
```



#### 命名模拟（Named Mocks）

`namedMock()` 方法将根据第一个参数生成一个类

```
$mock = \Mockery::namedMock('MyClassName', 'DateTime');
```

这个示例会创建一个名为 `MyClassName` 的类，它继承自 `DateTime`。

命名模拟在某种程度上是一种边缘情况，但在代码依赖于魔术常量 `__CLASS__` 的情况下，或者当我们需要两个抽象类型的派生类实际上是不同的类时，它们是很有用的。



#### 构造函数参数

有时，模拟类需要构造函数参数。我们可以将它们作为索引数组传递给 Mockery，作为第二个参数：

```
$mock = \Mockery::mock('MyClass', [$constructorArg1, $constructorArg2]);
```

或者如果我们`MyClass`还需要实现一个接口，作为第三个参数：

```
$mock = \Mockery::mock('MyClass', 'MyInterface', [$constructorArg1, $constructorArg2]);
```

Mockery 现在知道将`$constructorArg1`和`$constructorArg2`作为参数传递给构造函数。



#### 行为修饰符（Behavior Modifiers）

在创建一个模拟对象时，我们可能希望使用一些在Mockery中不是默认行为的常见行为。

使用 `shouldIgnoreMissing()` 行为修饰符将会将这个模拟对象标记为一个被动模拟：

```
\Mockery::mock('MyClass')->shouldIgnoreMissing();
```

在这样的模拟对象中，对于没有被期望覆盖的方法调用，将返回null，而不是通常的错误。

我们还可以选择通过使用附加的修饰符来返回一个类型为 `\Mockery\Undefined`（即一个空对象）的对象

```
\Mockery::mock('MyClass')->shouldIgnoreMissing()->asUndefined();	// 返回的对象只是一个占位符
```

我们之前已经遇到过 `makePartial()` 方法，因为它是我们用来创建"Runtime partial test doubles"（运行时部分测试替身）的方法：

```
\Mockery::mock('MyClass')->makePartial();
```

这种类型的模拟对象会将所有没有被期望控制的方法延迟到模拟的父类，即`MyClass`。而之前的`shouldIgnoreMissing()` 返回null，这种行为只是简单地调用父类的匹配方法。
