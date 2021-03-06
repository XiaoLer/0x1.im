---
tags:
- php
date: "2016-02-28T12:00:00Z"
title: PHP 代码规范简洁之道
---

## 1. 统一的编码规范

编码规范往简单说其实就是三个方面：

1. 换行
2. 空格
3. 变量命名

放在 PHP 里面，还有一些附加的地方，比如关键字大小写，语法糖的使用（`array()` 与 `[]` 等）的问题。之前整理过 [PSR](https://github.com/XiaoLer/php-develop-standards/blob/master/php-basic-coding-standard.md) 的标准，也找过 [php-cs-fixer](http://0x1.im/blog/php/php-cs-fixer.html) 这样的工具。这些都是规范代码的重要手段。有统一的标准，配合上工具的检查，形成统一的编码约束不是什么难题。

没有规范，不同的人甚至同一个人在空格、换行和命名上都有可能是十分随意的。代码一长，回使整个文件看起来无比杂乱。

典型的例子比如：`if` 和 `else` 的组合，可以写出无数种风格，比如：

```php
<?php
# 单语句不写大括号
if (true) doSomething();
# else 大括号换行 十分占篇幅
if (true)
{
    doSomething();
}
else {
    doElseThings();
}
# 此外还有关键字后不带空格，随意缩进等等
# ...
```

再比如变量函数命名的问题上，各种混搭风格层出不穷：

``` php
<?php
# 全小写
$someparam1 = null;
# 首字母下环线
$_some_param_1 = null;
# 某些库的类，下划线和大小写混用
class Abstract_ClassA
{

}
```

这里并不探讨各种写法的优缺点，只是风格需要保持统一，不要混用。

## 2. 良好的编码习惯

如果你仔细看过 PSR 的标准，可能也会注意到某些地方其实是标准无法涉及到的。比如一个超长的表达式在什么时候换行，如何缩进等。

这里涉及到的就是编码习惯的约束问题。

比如方法的链式的调用的问题，比如某些数据库查询的封装：

```php
<?php
# 不换行的情况下句子会很长
$result = $this->db->select('id')->where('a', 1)
    ->groupBy('a')->orderBy('id', 'DESC')->result();

# 这种情况下我建议是一个条件一行，保持缩进
$result = $this->db->select('id')
    ->where('a', 1)
    ->groupBy('a')
    ->orderBy('id', 'DESC')
    ->result();
```

还有数组定义，某些数组成员字符串很长的情况下的写法：

```php
<?php
$array = ['abcdefg', 'acbdfeg', 'bcadgfe', 'cdadgef'];

# 如果成员太长，我建议拆解，这样
$array = [
    'abcdefg',
    'acbdfeg',
    'bcadgfe',
    'cdadgef',
];
```

## 3. 寻找最优的写法

写代码的过程中，最优的写法和编码习惯又不是同一件事情。这里要说的指的是如何遵循 PHP 的语言特性或者框架的特征，充分发挥语言和框架的能力来减少冗余。

比如在获取前端传入的参数的时候，很多时候能见到这样的代码：

```php
<?php
$param = isset($_POST['param']) ? $_POST['param'] : '';
```

更有甚者一些框架将前端传入的参数封装了一层，比如 `$this->request->data['param']`，如果再去用 `isset` 或者 `array_key_exists` 判断，整个获取参数的语句就会变得很长。

某些情况下使用三元运算符的时候需要注意 `?:` 是可以[合并使用](http://0x1.im/blog/php/add-a-operator-to-php7.html)的。

其实我们应该去尝试封装这种写法，防止同样的变量出现在一个语句中多次的情况。对于默认值的赋予，可以调研框架是否有封装提供，或者通过强制类型转换的方式进行。

还有一种情况是条件和循环嵌套的时候。比如从数组里抽取某一个字段，或者对某一个字段的值进行处理的时候，善用 `array_map` 以及引用（`&`）往往能节省不少工作量。但是使用的时候也要注意数组指针最后的位置。

在根据条件返回结果时，需要善用 `return`。还有就是合理的抽象与封装。

## 4. review 自己的代码

除了以上提到的在日常开发中需要注意的一些问题。还有就是事后的工作。

我相信很多人在看自己以前的代码的时候都有一种其实有更好的写法的感觉。随着时间的推移自己总会有更丰富的阅历，更多的想法。偶尔 review 自己的代码也是对过去的总结，也许会有新的感知。

## 5. 推广给你的队友

团队项目中，队友的配合对整个代码的规范起着决定性的作用。一个团队中有一个人不遵守规范，而又要到处修改代码的时候，很快就能破坏到所有的约束。

有统一的标准，有良好的执行力才能完成这件工作。
