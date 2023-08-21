---
layout: post
title: Mockery中文文档
categories: php
description: Mockery中文文档
keywords: php
---



**Mockery 是一个PHP模拟对象框架，可用于PHPUnit、PHPSpec或其他测试框架的单元测试。**

Mockery：[官方GitHub](https://github.com/mockery/mockery)，[官方文档](http://docs.mockery.io/en/latest/)

------



## 安装

#### 使用composer安装Mockery

composer.json：

```xml
{
    "require-dev": {
        "mockery/mockery": "dev-master"
    }
}
```

安装命令：

```shell
php composer.phar update --no-dev
```

其他安装方法是直接从 Composer 命令行安装，如下：

```shell
php composer.phar require --dev mockery/mockery
```



## 使用

### 创建测试替身

#### Mock（模拟）

创建的模拟对象

```php
$mock = \Mockery::mock('foo');	// 创建名为foo的模拟对象
$mock = \Mockery::mock();	// 创建没有名称的模拟对象
$mock = \Mockery::mock('MyClass');	// 基于现有类创建模拟对象
$mock = \Mockery::mock('MyInterface');	// 基于现有接口创建模拟对象

// 这个模拟对象现在将是类型MyClass并实现MyInterface和OtherInterface接口
$mock = \Mockery::mock('MyClass, MyInterface, OtherInterface');	
// 也可以通过第二个参数来告诉所需的接口
$mock = \Mockery::mock('MyClass', 'MyInterface, OtherInterface');
```



#### Spies（间谍）

```php
// 创建Spies和创建Mock一样
$spy = \Mockery::spy('MyClass');
$spy = \Mockery::spy('MyClass, MyInterface, OtherInterface');
```

`Spies` 和 `Mock` 之间的主要区别在于，使用 `Spies` ，我们在事后验证调用是否发生。而使用 `Mock` ，我们是在调用之前就设置了调用期望，并得到了我们期望它返回的返回结果。

**Spies 与 Mock 的区别案例：**

```php
$mock = \Mockery::mock('MyClass');
$spy = \Mockery::spy('MyClass');

$mock->shouldReceive('foo')->andReturn(42);

$mockResult = $mock->foo();
$spyResult = $spy->foo();

$spy->shouldHaveReceived()->foo();

var_dump($mockResult); // int(42)
var_dump($spyResult); // null
```



#### Runtime partial test doubles（运行时部分测试替身）

对于那些尚未设置调用期望的方法的调用，这个代用对象会像正常对象实例一样执行。

代码示例：

```php
class Foo {
    function foo() { return 123; }
    function bar() { return $this->foo(); }
}

$foo = mock(Foo::class)->makePartial();
// 因为使用了makePartial方法将其设置为部分替身，所以这里没设置调用期望也不会报错
$foo->foo(); // int(123);
```

在这个示例中，我们用`mock`函数创建了一个名为`$foo`的代用对象，并通过`makePartial`方法将其设置为部分替身。然后，我们调用了`$foo->foo()`，它会返回123，就像调用一个正常实例的方法一样。

之后，我们可以通过以下方式处理这个代用对象，就像处理任何其他Mockery代用对象一样：

```php
$foo->shouldReceive('foo')->andReturn(456);	// 设置调用期望
$foo->bar(); // int(456)
```

这里我们告诉代用对象，期望调用`foo`方法，并返回456。然后当我们调用`$foo->bar()`时，它会返回456，而不是之前的123。



#### Generated partial test doubles（生成的部分替身）

对于已经设置了调用期望的方法可以正常调用，没设置调用期望的将报异常

代码示例：

```php
class Foo {
    function foo() { return 123; }
    function bar() { return $this->foo(); }
}

$foo = mock("Foo[foo]");
$foo->foo(); // 报异常，没有设置调用期望

$foo->shouldReceive('foo')->andReturn(456);
$foo->foo(); // int(456)

// 对此设置期望没有效果
$foo->shouldReceive('bar')->andReturn(999);
$foo->bar(); // int(456)
```

在这个示例中，我们使用字符串`"Foo[foo]"`创建了一个Foo类foo方法的代用对象。在没有设置期望的情况下调用`$foo->foo()`会导致错误。然后，我们告诉代用对象，期望调用`foo`方法，并返回456。当我们调用`$foo->foo()`时，它会返回456。然而，对于`bar`方法的期望设置没有效果，因为`bar`方法没有在生成的部分替身中指定。

另外，我们还可以使用`!method`语法明确指定要直接运行的方法：

```php
class Foo {
    function foo() { return 123; }
    function bar() { return $this->foo(); }
}

$foo = mock("Foo[!foo]");
$foo->foo(); // int(123)
$foo->bar(); // 报异常，没有设置调用期望
```

在这个示例中，我们明确地告诉代用对象只有`foo`方法会直接运行，因此调用`$foo->foo()`会返回123，而对`bar`方法的调用将导致错误，因为我们没有为其设置调用期望。



#### Proxied partial test doubles（代理部分模拟）

我们可能会遇到一个具有被标记为final的方法或类。在这种情况下，我们不能简单地扩展这个类并重写方法来进行模拟。我们需要有创意的方法。

```php
$mock = \Mockery::mock(new MyClass);
```

新的模拟是一个代理。它拦截调用并将其重新路由到代理对象（我们构造并传递进去的对象）上。这使我们能够模拟那些被标记为final的方法，因为代理不受这些限制



#### 别名

在类的有效名称（当前未加载）前面加上“alias:”前缀将生成“alias mock”。别名模拟使用 stdClass 的给定类名创建类别名，通常用于启用公共静态方法的模拟。对引用静态方法的新模拟对象设置的期望将由对该类的所有静态调用使用。

```php
$mock = \Mockery::mock('alias:MyClass');
```

> 尽管支持别名类，但我们不推荐它。



#### 重载

在类的有效名称（当前未加载）前面加上“overload:”

```php
$mock = \Mockery::mock('overload:MyClass');
```

该类创建的新实例将导入在原始模拟上设置的任何期望。原始模拟从未被验证，因为它使用了新实例的期望存储。

换句话说，当创建模拟类的新实例时，实例模拟将“拦截”，然后将使用该模拟。

>  在多个测试中使用别名/实例模拟将产生致命错误，因为我们不能有两个同名的类。为了避免这种情况，请在单独的 PHP 进程中运行此类测试



#### 命名模拟（Named Mocks）

`namedMock()` 方法将根据第一个参数生成一个类

```php
$mock = \Mockery::namedMock('MyClassName', 'DateTime');
```

这个示例会创建一个名为 `MyClassName` 的类，它继承自 `DateTime`。



#### 构造函数参数

有时，模拟类需要构造函数参数。我们可以将它们作为索引数组传递给 Mockery，作为第二个参数：

```php
$mock = \Mockery::mock('MyClass', [$constructorArg1, $constructorArg2]);
```

如果我们`MyClass`还需要实现一个接口，作为第三个参数：

```php
$mock = \Mockery::mock('MyClass', 'MyInterface', [$constructorArg1, $constructorArg2]);
```



#### 行为修饰符（Behavior Modifiers）

使用 `shouldIgnoreMissing()` 行为修饰符将会将这个模拟对象标记为一个被动模拟：

```php
\Mockery::mock('MyClass')->shouldIgnoreMissing();
```

在这样的模拟对象中，对于没有设置调用期望的方法调用，将返回null，而不是通常的异常错误。

我们还可以选择通过使用附加的修饰符来返回一个类型为 `\Mockery\Undefined`（即一个空对象）

```php
\Mockery::mock('MyClass')->shouldIgnoreMissing()->asUndefined();	// 返回的对象只是一个占位符
```

我们之前已经遇到过 `makePartial()` 方法，因为它是我们用来创建"Runtime partial test doubles"（运行时部分测试替身）的方法：

```php
\Mockery::mock('MyClass')->makePartial();
```

这种类型的模拟对象会将所有没有设置调用期望的方法延迟到模拟的父类，即`MyClass`。而之前的`shouldIgnoreMissing()` 返回null，这种行为只是简单地调用父类的匹配方法。



### 期望声明

在创建模拟对象时，我们通常会在开始就定义它应该如何被调用。这就是Mockery的期望声明发挥作用的地方。

#### 声明方法调用期望

要告诉我们对象期望调用的方法，我们使用`shouldReceive`方法：

```php
$mock = \Mockery::mock('MyClass');
$mock->shouldReceive('name_of_method');

// 我们可以声明多个
$mock->shouldReceive('name_of_method_1', 'name_of_method_2');
```

我们可以为方法调用声明期望，并设置它们的返回值：

```php
$mock = \Mockery::mock('MyClass');
$mock->shouldReceive([
    'name_of_method_1' => 'return value 1',
    'name_of_method_2' => 'return value 2',
]);
```

还有一种快捷的方法来设置方法调用的期望和返回值：

```php
$mock = \Mockery::mock('MyClass', ['name_of_method_1' => 'return value 1', 'name_of_method_2' => 'return value 2']);
```

我们可以声明对象不应该期望调用给定的方法：

```php
$mock = \Mockery::mock('MyClass');
$mock->shouldNotReceive('name_of_method');
```

这个方法是调用`shouldReceive()->never()`的一个便捷方法。



#### 声明方法参数期望

对于我们声明的方法期望，我们可以添加参数。使得定义的期望仅适用于匹配预期参数列表的方法调用：

```php
$mock = \Mockery::mock('MyClass');
$mock->shouldReceive('name_of_method')->with($arg1, $arg2, ...);
// 或者
$mock->shouldReceive('name_of_method')->withArgs([$arg1, $arg2, ...]);
```

我们可以使用内置的匹配器类为参数匹配添加更多的灵活性。例如，`\Mockery::any()`匹配传递给`with()`参数列表中的任何参数。Mockery还允许使用Hamcrest库的匹配器 - 例如，Hamcrest函数`anything()`等同于`\Mockery::any()`。

重要的是要注意，这意味着所有附加的参数期望仅适用于以这些确切参数调用方法的情况：

```php
$mock = \Mockery::mock('MyClass');

$mock->shouldReceive('foo')->with('Hello');

$mock->foo('Goodbye'); // 抛出NoMatchingExpectationException异常
```



#### 抛出异常

我们可以告诉模拟对象的方法抛出异常：

```php
$mock = \Mockery::mock('MyClass');
$mock->shouldReceive('name_of_method')->andThrow(new Exception);
```

当调用时，它将抛出给定的异常对象。

我们也可以传递异常类，消息或代码来设置抛出异常的方法：

```php
$mock = \Mockery::mock('MyClass');
$mock->shouldReceive('name_of_method')->andThrow('exception_name', 'message', 123456789);
```



#### 设置公共属性

与期望一起使用，以便当匹配的方法被调用时，我们可以使用`andSet()`或`set()`来将模拟对象的公共属性设置为指定的值：

```php
$mock = \Mockery::mock('MyClass');
$mock->shouldReceive('name_of_method')->andSet($property, $value);
// 或者
$mock->shouldReceive('name_of_method')->set($property, $value);
```



#### 调用真实方法

在某些情况下，我们希望调用被模拟类的真实方法并返回其结果：

```php
passthru()
```

`passthru()` 它允许在调用真实方法时应用期望匹配和调用计数验证，同时仍然使用预期参数调用真实类方法。



#### 声明调用计数期望

除了在方法调用的参数和返回值上设置期望外，我们还可以设置任何方法应该被调用多少次的期望。

当调用计数期望未满足时，将抛出一个`\Mockery\Expectation\InvalidCountException`。

我们可以声明预期的方法可能会被调用零次或多次：

```php
$mock = \Mockery::mock('MyClass');
$mock->shouldReceive('name_of_method')->zeroOrMoreTimes();
```

这是除非另有规定，否则所有方法的默认值。

要告诉Mockery期望方法被精确调用的次数，我们可以使用以下方法：

```php
$mock = \Mockery::mock('MyClass');
$mock->shouldReceive('name_of_method')->times($n);
```

这里，`$n` 是该方法应该被调用的次数。

还有一些常见情况的简写方法。

要声明预期方法只能被调用一次：

```php
$mock = \Mockery::mock('MyClass');
$mock->shouldReceive('name_of_method')->once();
```

要声明预期方法必须被调用两次：

```php
$mock = \Mockery::mock('MyClass');
$mock->shouldReceive('name_of_method')->twice();
```

要声明预期方法永远不会被调用：

```php
$mock = \Mockery::mock('MyClass');
$mock->shouldReceive('name_of_method')->never();
```



#### 调用计数修饰符

调用计数期望可以设置修饰符。

如果我们想要告诉Mockery方法至少应该被调用的最小次数，我们使用`atLeast()`：

```php
$mock = \Mockery::mock('MyClass');
$mock->shouldReceive('name_of_method')->atLeast()->times(3);
```

`atLeast()->times(3)` 表示该调用至少应该被调用三次，但永远不会少于三次。

类似地，我们可以使用`atMost()` 来告诉Mockery方法应该被调用的最大次数：

```php
$mock = \Mockery::mock('MyClass');
$mock->shouldReceive('name_of_method')->atMost()->times(3);
```

`atMost()->times(3)` 表示该调用最多只能被调用三次。如果方法根本没有被调用，期望仍然会被满足。

我们还可以使用`between()`来设置一系列调用次数的范围：

```php
$mock = \Mockery::mock('MyClass');
$mock->shouldReceive('name_of_method')->between($min, $max);
```

实际上，这与`atLeast()->times($min)->atMost()->times($max)`是相同的，但作为一种缩写提供。它后面可以跟随一个没有参数的`times()` 调用，以保持API的自然语言可读性。



#### 多次调用，不同期望

如果我们期望一个方法被多次调用，每次传递不同的参数或返回值，我们可以简单地重复期望。当然，如果我们期望多次调用不同的方法，同样也可以这样做。

```php
$mock = \Mockery::mock('MyClass');
// 第一次调用的期望
$mock->shouldReceive('name_of_method')
    ->once()
    ->with('arg1')
    ->andReturn($value1)

    // 第二次调用同一个方法
    ->shouldReceive('name_of_method')
    ->once()
    ->with('arg2')
    ->andReturn($value2)

    // 最后一次调用另一个方法
    ->shouldReceive('other_method')
    ->once()
    ->with('other')
    ->andReturn($value_other);
```



#### 期望声明工具

`ordered()`：声明此方法应按特定顺序与类似标记的方法相对应。

`ordered(group)`：将方法声明为属于一个顺序组。组内的方法可以按任何顺序调用，但外部组中有序调用。

`globally()`：如果在`ordered()`或`ordered(group)`之前调用，它会声明此排序适用于所有模拟对象（不仅仅是当前模拟对象）。

`byDefault()`：将期望标记为默认。除非创建了非默认期望，否则将应用默认期望。

`getMock()`：从期望链中返回当前模拟对象。在希望将模拟设置为单个语句的情况下非常有用。

使用案例：

```php
$mock = \Mockery::mock('foo')->shouldReceive('foo')->andReturn(1)->getMock();
```



### 参数验证

在设置期望时，传递给`with()`声明的参数确定了匹配方法和调用期望之间的条件。因此，我们可以为单个方法设置许多期望，每个期望根据预期的参数而有所不同。这种参数匹配是基于“最佳匹配”的原则进行的。这确保了显式匹配优先于一般匹配。

显式匹配是指预期参数和实际参数可以轻松等同（即使用===或==）。

一般匹配是指通过正则表达式和可用的通用匹配器实现。通用匹配器的目的是允许以非显式的方式定义参数，例如，传递给`with()`的`Mockery::any()`将匹配该位置上的任何参数。

Mockery的通用匹配器并不涵盖所有可能性，但提供了对Hamcrest匹配器库的可选支持。Hamcrest是同名Java库的PHP移植（也已移植到Python、Erlang等）。

以下示例展示了Mockery匹配器及其Hamcrest等。

最常见的匹配器是以下`with()`匹配器：

```php
$mock = \Mockery::mock('MyClass');
$mock->shouldReceive('foo')->with(1):
```

它告诉Mockery它应该接收一个带有整数1作为参数的foo方法的调用。在这种情况下，Mockery首先尝试使用===来匹配参数。如果参数是基本类型，并且如果不满足完全相等的比较，则Mockery会回退到==比较运算符。

在使用对象作为参数时，Mockery只进行严格的===比较，这意味着只有相同的$object才会匹配：

```php
$object = new stdClass();
$mock = \Mockery::mock('MyClass');
$mock->shouldReceive("foo")->with($object);

// Hamcrest equivalent
$mock->shouldReceive("foo")->with(identicalTo($object));
```

不同实例将不匹配

如果我们需要对对象进行松散比较，我们可以使用 Hamcrest 的 `equalTo`匹配器来做到这一点：

```php
$mock->shouldReceive("foo")->with(equalTo(new stdClass));
```

如果我们不关心参数的类型或值，只关心是否存在任何参数，可以使用`any()`：

```php
$mock = \Mockery::mock('MyClass');
$mock->shouldReceive("foo")->with(\Mockery::any());

// Hamcrest equivalent
$mock->shouldReceive("foo")->with(anything())
```

在此参数槽中传递的任何内容都不受约束。



#### 验证类型和资源

`type()`匹配器会根据指定类型形成有效的类型检查。

要匹配任何`int`类型数字，可以这样做：

```php
$mock = \Mockery::mock('MyClass');
$mock->shouldReceive("foo")->with(\Mockery::type('integer'));

// Hamcrest等效
$mock->shouldReceive("foo")->with(integerValue());
$mock->shouldReceive("foo")->with(typeOf('integer'));
```

`type()`匹配器还可以接受类名或接口名，用于进行实际参数的instanceof评估。Hamcrest使用`anInstanceOf()`。 

类型检查器的完整列表可以在[php.net](https://www.php.net/manual/en/ref.var.php)上找到，或者在[Hamcrest代码](https://github.com/hamcrest/hamcrest-php/blob/master/hamcrest/Hamcrest.php)中浏览Hamcrest函数列表。 



#### 复杂参数验证

如果要执行复杂的参数验证，`on()`匹配器非常有价值。它接受一个闭包（匿名函数），其中实际参数将被传递。 

```php
$mock = \Mockery::mock('MyClass');
$mock->shouldReceive("foo")->with(\Mockery::on(closure));
```

如果闭包评估为true，则假定参数已经匹配了期望。

```php
$mock = \Mockery::mock('MyClass');

$mock->shouldReceive('foo')
    ->with(\Mockery::on(function ($argument) {
        if ($argument % 2 == 0) {
            return true;
        }
        return false;
    }));

$mock->foo(4); // 匹配期望
$mock->foo(3); // 抛出NoMatchingExpectationException
```

> 注意：没有 Hamcrest 版本的`on()`匹配器。

我们还可以通过将闭包传递给`withArgs()`方法来执行参数验证。如果闭包评估为true，则假定参数列表已经匹配了期望方法的调用：

```php
$mock = \Mockery::mock('MyClass');
$mock->shouldReceive("foo")->withArgs(closure);
```

闭包还可以处理可选参数，因此如果在调用预期方法时缺少可选参数，并不一定意味着参数列表与预期不匹配。

```php
$closure = function ($odd, $even, $sum = null) {
    $result = ($odd % 2 != 0) && ($even % 2 == 0);
    if (!is_null($sum)) {
        return $result && ($odd + $even == $sum);
    }
    return $result;
};

$mock = \Mockery::mock('MyClass');
$mock->shouldReceive('foo')->withArgs($closure);

$mock->foo(1, 2); // It matches the expectation: the optional argument is not needed
$mock->foo(1, 2, 3); // It also matches the expectation: the optional argument pass the validation
$mock->foo(1, 2, 4); // It doesn't match the expectation: the optional doesn't pass the validation
```

如果我们想将参数与正则表达式进行匹配，我们可以使用`\Mockery::pattern()`：

```php
$mock = \Mockery::mock('MyClass');
$mock->shouldReceive('foo')->with(\Mockery::pattern('/^foo/'));

// Hamcrest equivalent
$mock->shouldReceive('foo')->with(matchesPattern('/^foo/'));
```

`ducktype()`匹配器是按类类型进行匹配的方法：

```php
$mock = \Mockery::mock('MyClass');
$mock->shouldReceive('foo')->with(\Mockery::ducktype('foo', 'bar'));	// 匹配任何包含`foo`和 `bar`方法的类对象作为参数
$mock->foo(new Foo());	// Foo()类下面如果包含foo和bar方法将匹配成功
```

它将匹配任何包含`foo`和 `bar`方法的类对象作为参数

> **注意：**`ducktype()`匹配器没有Hamcrest版本。



#### 捕获参数

如果要对单个参数执行多次验证，使用 `capture` 匹配器。它接受一个变量，实际参数将被赋给该变量。

```php
$mock = \Mockery::mock('MyClass');
$mock->shouldReceive("foo")->with(\Mockery::capture($bar));
```

这将传递给foo的任何参数分配给本地的$bar变量，然后使用断言执行其他验证。

> **注意：**`capture`匹配器始终评估为true。因此，应始终执行其他参数验证。



#### 其他参数匹配器

`not()`匹配器匹配任何不等于匹配器参数的参数：

```php
$mock = \Mockery::mock('MyClass');
$mock->shouldReceive('foo')->with(\Mockery::not(2));

// Hamcrest等效
$mock->shouldReceive('foo')->with(not(2));
```

`anyOf()`匹配任何等于给定参数之一的参数：

```php
$mock = \Mockery::mock('MyClass');
$mock->shouldReceive('foo')->with(\Mockery::anyOf(1, 2));

// Hamcrest等效
$mock->shouldReceive('foo')->with(anyOf(1,2));
```

`notAnyOf()`匹配任何不等于或不等于给定参数之一的参数：

```php
$mock = \Mockery::mock('MyClass');
$mock->shouldReceive('foo')->with(\Mockery::notAnyOf(1, 2));
```

> **注意：**`notAnyOf()`匹配器没有Hamcrest版本。

`subset()`匹配器匹配任何包含给定数组子集的数组：

```php
$mock = \Mockery::mock('MyClass');
$mock->shouldReceive('foo')->with(\Mockery::subset(array(0 => 'foo')));
```

它同时强制执行键和值的比较，即比较每个实际元素的键和值。

> **注意：**没有Hamcrest版本的此功能，尽管Hamcrest可以使用`hasEntry()`或`hasKeyValuePair()`来检查单个条目。

`contains()`匹配器匹配任何包含所列值的数组参数：

```php
$mock = \Mockery::mock('MyClass');
$mock->shouldReceive('foo')->with(\Mockery::contains(value1, value2));
```

键将被忽略。

`hasKey()`匹配器匹配任何包含给定键名的数组参数：

```php
$mock = \Mockery::mock('MyClass');
$mock->shouldReceive('foo')->with(\Mockery::hasKey(key));
```

`hasValue()`匹配器匹配任何包含给定值的数组参数：

```php
$mock = \Mockery::mock('MyClass');
$mock->shouldReceive('foo')->with(\Mockery::hasValue(value));
```



### 替代shouldReceive语法

从Mockery 1.0.0版本开始，我们支持以调用任何PHP方法相同的方式调用方法，而不是将方法名作为字符串参数传递给Mockery的shouldReceive方法。

实现这一点的两个Mockery方法是allows()和expects()。

#### allows()

当我们为返回预定义返回值的方法创建时，我们使用allows()

```php
$mock = \Mockery::mock('MyClass');
$mock->allows([
    'name_of_method_1' => 'return value',
    'name_of_method_2' => 'return value',
]);
```

这与以下shouldReceive语法等效：

```php
$mock = \Mockery::mock('MyClass');
$mock->shouldReceive([
    'name_of_method_1' => 'return value',
    'name_of_method_2' => 'return value',
]);
```

需要注意的是，在这种格式下，我们还告诉Mockery我们不关心存根方法的参数。

如果我们关心参数，我们会这样做：

```php
$mock = \Mockery::mock('MyClass');
$mock->allows()
    ->name_of_method_1($arg1)
    ->andReturn('return value');
```

这与以下shouldReceive语法等效：

```php
$mock = \Mockery::mock('MyClass');
$mock->shouldReceive('name_of_method_1')
    ->with($arg1)
    ->andReturn('return value');
```



#### expects()

当我们想要验证特定方法是否被调用时，我们使用expects()：

```php
$mock = \Mockery::mock('MyClass');
$mock->expects()
    ->name_of_method_1($arg1)
    ->andReturn('return value');
```

这与以下shouldReceive语法等效：

```php
$mock = \Mockery::mock('MyClass');
$mock->shouldReceive('name_of_method_1')
    ->once()
    ->with($arg1)
    ->andReturn('return value');
```

默认情况下，expects()设置了一个期望，即该方法应该仅被调用一次。如果我们期望该方法被调用多次，我们可以更改该期望：

```php
$mock = \Mockery::mock('MyClass');
$mock->expects()
    ->name_of_method_1($arg1)
    ->twice()
    ->andReturn('return value');
```



### 间谍（Spies）

间谍（Spies）是一种测试替身（test double），但与模拟对象（mocks）不同，间谍（Spies）记录与被测系统（System Under Test，SUT）之间的所有交互，并允许我们在事后对这些交互进行断言。

创建一个间谍（Spies）意味着我们不必为测试中可能接收的每个方法调用设置期望，其中一些对当前测试可能不相关。间谍（Spies）允许我们仅对当前测试关心的调用进行断言，降低了过度规范化的可能性，使我们的测试更加清晰。

间谍（Spies）还允许我们在测试中遵循更熟悉的排列-执行-断言（Arrange-Act-Assert）或给定-当-则（Given-When-Then）风格。对于模拟对象（mocks），我们必须遵循一个不太熟悉的风格，类似于排列-期望-执行-断言（Arrange-Expect-Act-Assert），在执行被测系统之前，我们必须告诉模拟对象（mocks）期望的调用，然后断言这些期望是否得到满足：

```php
// 安排
$mock = \Mockery::mock('MyDependency');
$sut = new MyClass($mock);

// 期望
$mock->shouldReceive('foo')
    ->once()
    ->with('bar');

// 执行
$sut->callFoo();

// 断言
\Mockery::close();
```

间谍允许我们跳过期望部分，将断言移到我们在被测系统上执行之后，通常使我们的测试更易读：

```php
// 安排
$spy = \Mockery::spy('MyDependency');
$sut = new MyClass($spy);

// 执行
$sut->callFoo();

// 断言
$spy->shouldHaveReceived()
    ->foo()
    ->with('bar');
```

然而，与模拟对象（Mocks）相比，间谍（Spies）的限制要少得多，这意味着测试通常不太精确。

虽然间谍（Spies）使我们的测试更能表达意图，但它们确往往更少地揭示被测系统的设计。如果我们不得不为模拟对象（Mocks）设置很多期望，并在很多不同的测试中都要这样做，那么我们的测试正在告诉我们一些东西 - 被测系统做得太多，可能需要进行重构。但我们在使用间谍（Spies）时不会遇到这个问题，它们只会忽略与它们不相关的调用。

使用间谍（Spies）的另一个缺点是调试。当模拟对象收到不期望的调用时，它会立即抛出异常（快速失败），给我们一个良好的堆栈跟踪，甚至可能调用我们的调试器。而对于间谍（Spies），我们只是在事后断言是否进行了调用，因此如果进行了错误的调用，我们没有与模拟对象（Mocks）相同的实时上下文。

最后，如果我们需要为测试替身定义返回值，我们不能在间谍（Spies）中做到这一点，只能在模拟对象（Mocks）中做到。



#### 参考

要验证是否在间谍上调用了某个方法，我们使用shouldHaveReceived()方法：

```php
$spy->shouldHaveReceived('foo');
```

要验证在间谍上未调用某个方法，我们使用shouldNotHaveReceived()方法：

```php
$spy->shouldNotHaveReceived('foo');
```

我们还可以在间谍上进行参数匹配：

```php
$spy->shouldHaveReceived('foo')->with('bar');
```

通过将参数数组传递，也可以进行参数匹配：

```php
$spy->shouldHaveReceived('foo', ['bar']);
```

在验证未调用方法时，只能通过将参数数组作为第二个参数传递给shouldNotHaveReceived()方法来进行参数匹配：

```php
$spy->shouldNotHaveReceived('foo', ['bar']);
```

最后，当期望收到调用时，我们还可以验证调用的次数：

```php
$spy->shouldHaveReceived('foo')
    ->with('bar')
    ->twice();
```



#### 替代shouldReceive语法

从Mockery 1.0.0版本开始，我们支持以调用任何PHP方法相同的方式调用方法，而不是将方法名作为字符串参数传递给Mockery的should*方法。

在使用间谍时，这仅适用于shouldHaveReceived()方法：

```php
$spy->shouldHaveReceived()
    ->foo('bar');
```

我们也可以设置对调用次数的期望：

```php
$spy->shouldHaveReceived()
    ->foo('bar')
    ->twice();
```

>  不幸的是，由于限制，我们无法为shouldNotHaveReceived()方法支持相同的语法。



### 创建部分模拟对象（Partial Mocks）

部分模拟对象在我们只需要模拟对象的几个方法，而其余方法需要正常响应调用时非常有用。Mockery实现了三种不同的部分模拟策略。每种策略都具有特定的优缺点，因此我们使用哪种策略将取决于我们自己的偏好和需要模拟的源代码。

#### Runtime partial test doubles（运行时部分测试替身）

运行时部分测试替身，也称为被动部分模拟，是模拟对象的一种默认状态。

```php
$mock = \Mockery::mock('MyClass')->makePartial();
```

在运行时部分测试替身中，我们假设所有方法都将简单地委托给父类（MyClass）的原始方法，除非方法调用与已知的期望匹配。如果我们没有为特定方法调用找到匹配的期望，那么该调用将委托给被模拟的类。由于模拟和未模拟调用之间的区分完全取决于我们定义的期望，因此无需预先定义要模拟的方法。



#### Generated Partial Test Doubles（生成的部分测试替身）

生成的部分测试替身，也称为传统部分模拟，预先定义了要模拟的类的哪些方法，以及要保留为未模拟的方法（即按照正常方式调用）。创建传统模拟的语法如下：

```php
$mock = \Mockery::mock('MyClass[foo,bar]');
```

在上面的示例中，MyClass的foo()和bar()方法将被模拟，但不会触及任何其他MyClass方法。我们需要为foo()和bar()方法定义期望，以指定它们的模拟行为。

我们可以传递构造函数参数，因为模拟的方法可能依赖于这些参数！

```php
$mock = \Mockery::mock('MyNamespace\MyClass[foo]', array($arg1, $arg2));
```



#### Proxied Partial Mock（代理部分模拟）

我们可能会遇到一个被标记为final的类，它根本无法被模拟，或者可能会发现一个具有final标记的方法的类。在这种情况下，我们不能简单地扩展该类并重写方法以进行模拟 - 我们需要创造性地解决问题。

```php
$mock = \Mockery::mock(new MyClass);
```

新的模拟是一个代理。它拦截调用并将其重新路由到代理的对象以处理未受任何期望约束的方法调用。间接地，这允许我们模拟具有final标记的方法，因为代理不受这些限制。不过代理部分模拟将无法通过类的类型提示检查，因为它无法扩展该类。



#### Special Internal Cases（特殊内部情况）

除了代理部分模拟之外的所有模拟对象，都允许我们使用passthru()期望调用底层真实类方法。这将从真实调用返回值。



### 模拟受保护的方法

默认情况下，Mockery不允许模拟受保护的方法。尽管我们不推荐模拟受保护的方法，但在某些情况下可能没有其他解决方案。

对于这些情况，我们有一个名为`shouldAllowMockingProtectedMethods()`的方法。它指示Mockery明确允许仅对一个类的受保护方法进行模拟：

```php
class MyClass
{
    protected function foo()
    {
    }
}

$mock = \Mockery::mock('MyClass')->shouldAllowMockingProtectedMethods();
$mock->shouldReceive('foo');
```

这样可以允许对`MyClass`类的受保护方法`foo()`进行模拟。



### 模拟公共属性

Mockery允许我们以多种方式模拟属性。一种方式是我们可以在任何模拟对象上设置公共属性及其值。第二种方式是，我们可以使用期望方法`set()`和`andSet()`来设置属性值。

关于设置公共属性，您可以阅读 **期望声明 -> 设置公共属性**

> 注意：一般来说，Mockery不支持模拟任何魔术方法，因为这些方法通常不被视为公共API。因此，请将虚拟属性（依赖于`__get()`和`__set()`）模拟为实际在类上声明的属性。



### 模拟公共静态方法

静态方法不会在实际对象上调用，因此普通的模拟对象无法模拟它们。Mockery支持类别名模拟，在系统测试中通过自动加载或`require`语句，并允许Mockery拦截静态方法调用并为其添加期望。

有关创建类别名模拟以模拟公共静态方法的更多信息，请参阅 **创建测试替身 -> 别名**



### 保留按引用传递的方法参数行为

在PHP类方法中，参数可以通过引用进行传递。在这种情况下，对参数所做的更改会反映在原始变量中。例如：

```php
class Foo
{
    public function bar(&$a)
    {
        $a++;
    }
}

$baz = 1;
$foo = new Foo;
$foo->bar($baz);

echo $baz; // 将输出整数 2
```

在上面的示例中，变量`$baz`通过引用传递给`Foo::bar()`（注意参数前面的`&`符号）。`bar()`对参数引用所做的任何更改都会反映在原始变量`$baz`中。

Mockery可以使用反射来分析参数（使用Reflection）并判断它是否通过引用传递的方法。我们可以使用闭包参数匹配器来操作它，即`\Mockery::on()` - 请参阅"**复杂参数验证**"一章。

对于内部PHP类，Mockery不能使用反射来分析方法参数（这是PHP的限制）。为了解决这个问题，我们可以使用`\Mockery\Configuration::setInternalClassMethodParamMap()`来显式声明内部类的方法参数。

以下是使用`MongoCollection::insert()`的示例。`MongoCollection`是来自PECL的mongo扩展的内部类。其`insert()`方法接受一个数据数组作为第一个参数，以及一个可选的选项数组作为第二个参数。原始数据数组会被更新（即`insert()`按引用传递参数），以包含一个新的`_id`字段。我们可以使用配置的参数映射（告诉Mockery期望传递引用参数）以及附加到预期方法参数的闭包来模拟这种行为。

以下是一个PHPUnit单元测试，验证保留按引用传递行为：

```php
public function testCanOverrideExpectedParametersOfInternalPHPClassesToPreserveRefs()
{
    \Mockery::getConfiguration()->setInternalClassMethodParamMap(
        'MongoCollection',
        'insert',
        array('&$data', '$options = array()')
    );
    $m = \Mockery::mock('MongoCollection');
    $m->shouldReceive('insert')->with(
        \Mockery::on(function(&$data) {
            if (!is_array($data)) return false;
            $data['_id'] = 123;
            return true;
        }),
        \Mockery::any()
    );

    $data = array('a'=>1,'b'=>2);
    $m->insert($data);

    $this->assertTrue(isset($data['_id']));
    $this->assertEquals(123, $data['_id']);

    \Mockery::resetContainer();
}

```

#### 受保护方法

在处理受保护的方法并尝试保留其按引用传递的行为时，需要采用不同的方法。

```php
class Model
{
    public function test(&$data)
    {
        return $this->doTest($data);
    }

    protected function doTest(&$data)
    {
        $data['something'] = 'wrong';
        return $this;
    }
}

class Test extends \PHPUnit\Framework\TestCase
{
    public function testModel()
    {
        $mock = \Mockery::mock('Model[test]')->shouldAllowMockingProtectedMethods();

        $mock->shouldReceive('test')
            ->with(\Mockery::on(function(&$data) {
                $data['something'] = 'wrong';
                return true;
            }));

        $data = array('foo' => 'bar');

        $mock->test($data);
        $this->assertTrue(isset($data['something']));
        $this->assertEquals('wrong', $data['something']);
    }
}
```

这是一个非常特殊的情况，因此我们需要稍微更改原始代码，通过创建一个公共方法来调用受保护的方法，然后模拟该方法，而不是受保护的方法。这个新的公共方法将充当受保护方法的代理。



### 模拟 Demeter 链和流畅接口

这两个术语都指的是调用类似于以下语句的做法：

```php
$object->foo()->bar()->zebra()->alpha()->selfDestruct();
```

长串的方法调用链不一定是一件坏事，假设每个方法调用都链接到调用类已知的本地对象。作为一个有趣的例子，Mockery的长链（在第一个`shouldReceive()`方法之后）都调用了`\Mockery\Expectation`的同一个实例。然而，有时候情况并非如此，这个链在不断地跨越对象边界。

无论哪种情况，模拟这样的链可能是一项艰巨的任务。为了使其更容易，Mockery支持Demeter链的模拟。基本上，我们通过链中的快捷方式，并从最后一个调用返回一个定义好的值。例如，假设`selfDestruct()`方法将字符串“Ten！”返回给`$object`（一个`CaptainsConsole`的实例）。以下是如何模拟它的示例。

```php
$mock = \Mockery::mock('CaptainsConsole');
$mock->shouldReceive('foo->bar->zebra->alpha->selfDestruct')->andReturn('Ten!');
```

上述期望可以遵循之前看到的任何格式或期望，唯一不同之处在于方法名称只是由->分隔的所有预期链调用的字符串。Mockery将自动设置预期调用链，使用其最终的返回值，而不管在真实实现中可能使用的任何中间对象。

在此过程中，所有链的成员（最后一个调用除外）的参数都会被忽略。



### 处理Final类/方法

在PHP中，模拟标记为final的类或方法是一项主要的限制。final关键字防止了这些被标记的方法在子类中被替换（子类是模拟对象可以继承被模拟的类或对象的类型的方式）。

最简单的解决方案是在final类中实现一个接口，并使用类型提示/模拟该接口。

然而，这在某些第三方库中可能不太可行。Mockery允许从标记为final的类或包含final方法的类创建“代理模拟”。这提供了所有常规模拟对象的好处，但生成的模拟不会继承被模拟对象的类类型，也就是说，它不会通过任何instanceof比较。标记为final的方法将被代理到原始方法，即无法模拟final方法。

我们可以通过将希望模拟的实例化对象传递给`\Mockery::mock()`来创建一个代理模拟，即Mockery将为真实对象生成一个代理，并选择性地拦截方法调用以设置和满足期望。

请参阅“**创建测试替身 -> Runtime partial test doubles（运行时部分测试替身）**”章节中关于代理部分测试替身的小节。



### PHP魔术方法

在PHP中，以双下划线前缀的魔术方法，例如`__set()`，在模拟和单元测试中会带来特殊的问题。强烈建议单元测试和模拟对象不直接引用魔术方法。相反，只引用这些魔术方法模拟的虚拟方法和属性。

遵循这个建议将确保我们正在测试类的真实API，并且还确保不会发生冲突，因为Mockery必然会覆盖这些魔术方法，以便支持它在拦截方法调用和属性方面的作用。



### PHPUnit集成

Mockery被设计为一个简单易用的独立模拟对象框架，所以它与任何测试框架的集成是完全可选的。为了集成Mockery，我们需要为测试定义一个`tearDown()`方法，其中包含以下内容（我们可以使用较短的\Mockery命名空间别名）：

```php
public function tearDown() {
    \Mockery::close();
}
```

这个静态调用会清理当前测试使用的Mockery容器，并执行任何需要的验证任务，以满足我们的期望。

为了在使用Mockery时更加简洁，我们还可以使用显式的Mockery命名空间，并使用一个更短的别名。例如：

```php
use \Mockery as m;

class SimpleTest extends \PHPUnit\Framework\TestCase
{
    public function testSimpleMock() {
        $mock = m::mock('simplemock');
        $mock->shouldReceive('foo')->with(5, m::any())->once()->andReturn(10);

        $this->assertEquals(10, $mock->foo(5));
    }

    public function tearDown() {
        m::close();
    }
}
```

Mockery附带了一个自动加载器，所以我们不需要在测试中添加`require_once()`调用。要使用它，请确保 Mockery 在我们的 测试套件或文件`include_path`中，并将以下内容添加到我们的测试套件`Bootstrap.php` 或`TestHelper.php`文件中：

```php
require_once 'Mockery/Loader.php';
require_once 'Hamcrest/Hamcrest.php';

$loader = new \Mockery\Loader;
$loader->register();
```

如果我们使用Composer，可以简化为包括Composer生成的自动加载器文件：

```php
require __DIR__ . '/../vendor/autoload.php'; // 假设vendor在上一级目录
```

> 警告：在 Hamcrest 1.0.0 之前，`Hamcrest.php`文件名有一个小“h”（即`hamcrest.php`）。如果将 Hamcrest 升级到 1.0.0，请记住检查所有项目的文件名是否已更新。）

为了将Mockery集成到PHPUnit中，并避免调用`close`方法以及让Mockery从代码覆盖报告中移除，让你的测试用例继承`\Mockery\Adapter\Phpunit\MockeryTestCase`：

```php
class MyTest extends \Mockery\Adapter\Phpunit\MockeryTestCase
{
}
```

或者使用提供的trait：

```php
class MyTest extends \PHPUnit\Framework\TestCase
{
    use \Mockery\Adapter\Phpunit\MockeryPHPUnitIntegration;
}
```

从Mockery 1.0.0开始，继承`MockeryTestCase`或使用`MockeryPHPUnitIntegration` trait是将Mockery与PHPUnit集成的推荐方法。



#### PHPUnit监听器

在1.0.0版本之前，Mockery提供了一个PHPUnit监听器，会在测试结束时调用`Mockery::close()`。

现在，Mockery提供了一个PHPUnit监听器，如果没有调用`Mockery::close()`，测试将会失败。它可以帮助识别我们是否忘记包含trait或继承MockeryTestCase的测试。

如果我们使用PHPUnit的XML配置方法，可以包含以下内容来加载TestListener：

```php
<listeners>
    <listener class="\Mockery\Adapter\Phpunit\TestListener"></listener>
</listeners>
```

确保Composer的或Mockery的自动加载器存在于引导文件中，否则我们还需要定义一个指向TestListener类文件的“file”属性。

如果我们以编程方式创建测试套件，可以这样添加监听器：

```php
// 创建测试套件。
$suite = new PHPUnit\Framework\TestSuite();

// 创建监听器并将其添加到套件中。
$result = new PHPUnit\Framework\TestResult();
$result->addListener(new \Mockery\Adapter\Phpunit\TestListener());

// 运行测试。
$suite->run($result);
```

> 警告：PHPUnit提供了一个功能，允许测试在单独的进程中运行，以确保更好的隔离。Mockery通过`Mockery::close()`方法来验证模拟对象的期望，并提供了一个PHPUnit监听器，可以在每次测试后自动调用该方法。
>
> 然而，当使用PHPUnit的进程隔离时，这个监听器在正确的进程中不会被调用，导致可能不会遵守期望，但没有引发任何Mockery异常。为了避免这种情况，我们不能依赖于提供的Mockery PHPUnit TestListener，而是需要显式调用`Mockery::close`。最简单的解决方法是在`tearDown()`方法中包含此调用，如前面所述。
