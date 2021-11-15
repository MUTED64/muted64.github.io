---
title: PHP基础补完
date: 2020-06-16 20:51:21
tags:
  - PHP
  - Web
categories:
  - 网络安全
---

PHP基础太薄弱了。最近几天做个补完，记点笔记。

<!-- more -->

## PHP基础

PHP 脚本可以放在文档中的任何位置，以` <?php `开始，以 `?>` 结束，每个代码行都必须以分号结束。

`//` 是单行注释，`/**/`是多行注释。

## PHP变量

### 变量声明

变量以 $ 开始，大小写敏感。没有声明变量的命令，在声明变量在首次给变量赋值的同时完成。

### 作用域

变量的作用域是脚本中变量可被引用/使用的部分。

在所有函数外部定义的变量，拥有全局作用域。除了函数外，全局变量可以被脚本中的任何部分访问，**要在一个函数中访问一个全局变量，需要使用 global 关键字**。在 PHP 函数内部声明的变量是局部变量，仅能在函数内部访问。

**PHP中函数的花括号构成新的作用域。**if和for的花括号并不构成新的作用域。

PHP 有四种不同的变量作用域：

- local：局部变量
- global：全局变量
- static：静态变量
- parameter：函数参数

#### global

global 关键字用于**函数内访问全局变量**。要在函数内定义global变量需要先写global再声明。实例：

```php
<?php
$x=5;
$y=10;
 
function myTest()
{
    global $x,$y,$a;
    $y=$x+$y;
    $a=1;
}
 
myTest();
echo $y; // 输出 15
echo $a; // 输出 1
?>
```

PHP 将所有全局变量存储在一个名为 $GLOBALS[*index*] 的数组中。 *index* 保存变量的名称。这个数组可以在函数内部访问，也可以直接用来更新全局变量。

上面的实例可以写成这样：

```php
<?php
$x=5;
$y=10;
 
function myTest()
{
    $GLOBALS['y']=$GLOBALS['x']+$GLOBALS['y'];
} 
 
myTest();
echo $y;
?>
```

PHP的执行是以一个.php脚本为单位，在一个.php脚本的执行过程中，可以include和require其他PHP脚本进来执行。执行的.php脚本与include/require进来的脚本共享一个全局域(global scope)。global关键字无论在哪层，所引用的都是全局域的变量。

#### Static

每次调用该函数时，该变量将会保留着函数前一次被调用时的值。该变量仍然是函数的局部变量，但不会像普通的局部变量一样因为函数完成而被删除。

#### Parameter

参数是通过调用代码将值传递给函数的局部变量。参数是在参数列表中声明的，作为函数声明的一部分。实例：

```php
<?php
function myTest($x)
{
    echo $x;
}
myTest(5);
?>
```

### 预定义变量

PHP 提供了大量的预定义变量。由于许多变量依赖于运行的服务器的版本和设置，及其它因素，所以并没有详细的说明文档。一些预定义变量在 PHP 以[命令行](https://www.php.net/manual/zh/features.commandline.php)形式运行时并不生效。有关这些变量的详细列表，请参阅[预定义变量](https://www.php.net/manual/zh/reserved.variables.php)一章。

如果有可用的 PHP 预定义变量那最好用，如[超全局数组](https://www.php.net/manual/zh/language.variables.superglobals.php)。

超级全局变量不能被用作函数或类方法中的[可变变量](https://www.php.net/manual/zh/language.variables.variable.php)。

- [超全局变量](https://www.php.net/manual/zh/language.variables.superglobals.php) — 超全局变量是在全部作用域中始终可用的内置变量
- [$GLOBALS](https://www.php.net/manual/zh/reserved.variables.globals.php) — 引用全局作用域中可用的全部变量
- [$_SERVER](https://www.php.net/manual/zh/reserved.variables.server.php) — 服务器和执行环境信息
- [$_GET](https://www.php.net/manual/zh/reserved.variables.get.php) — HTTP GET 变量
- [$_POST](https://www.php.net/manual/zh/reserved.variables.post.php) — HTTP POST 变量
- [$_FILES](https://www.php.net/manual/zh/reserved.variables.files.php) — HTTP 文件上传变量
- [$_REQUEST](https://www.php.net/manual/zh/reserved.variables.request.php) — HTTP Request 变量
- [$_SESSION](https://www.php.net/manual/zh/reserved.variables.session.php) — Session 变量
- [$_ENV](https://www.php.net/manual/zh/reserved.variables.environment.php) — 环境变量
- [$_COOKIE](https://www.php.net/manual/zh/reserved.variables.cookies.php) — HTTP Cookies
- [$php_errormsg](https://www.php.net/manual/zh/reserved.variables.phperrormsg.php) — 前一个错误信息
- [$HTTP_RAW_POST_DATA](https://www.php.net/manual/zh/reserved.variables.httprawpostdata.php) — 原生POST数据
- [$http_response_header](https://www.php.net/manual/zh/reserved.variables.httpresponseheader.php) — HTTP 响应头
- [$argc](https://www.php.net/manual/zh/reserved.variables.argc.php) — 传递给脚本的参数数目
- [$argv](https://www.php.net/manual/zh/reserved.variables.argv.php) — 传递给脚本的参数数组

### 可变变量

在 PHP 的函数和类的方法中，[超全局变量](https://www.php.net/manual/zh/language.variables.superglobals.php)不能用作可变变量。*$this* 变量也是一个特殊变量，不能被动态引用。

要将可变变量用于数组，必须解决一个模棱两可的问题。这就是当写下 $$a[1] 时，解析器需要知道是想要 $a[1] 作为一个变量呢，还是想要 $$a 作为一个变量并取出该变量中索引为 [1] 的值。解决此问题的语法是，对第一种情况用 ${$a[1]}，对第二种情况用 ${$a}[1]。

### PHP之外的变量

通常，PHP 不会改变传递给脚本中的变量名。然而应该注意到点（句号）不是 PHP 变量名中的合法字符。至于原因，看看：

```php
<?php
$varname.ext;  /* 非法变量名 */
?>
```

这时，解析器看到是一个名为 $varname 的变量，后面跟着一个字符串连接运算符，后面跟着一个裸字符串（即没有加引号的字符串，且不匹配任何已知的健名或保留字）'ext'。很明显这不是想要的结果。

出于此原因，要注意 PHP 将会自动将外部变量名中的点替换成下划线。

因为 PHP 会判断变量类型并在需要时进行转换（通常情况下），因此在某一时刻给定的变量是何种类型并不明显。PHP 包括几个函数可以判断变量的类型，例如：[gettype()](https://www.php.net/manual/zh/function.gettype.php)，[is_array()](https://www.php.net/manual/zh/function.is-array.php)，[is_float()](https://www.php.net/manual/zh/function.is-float.php)，[is_int()](https://www.php.net/manual/zh/function.is-int.php)，[is_object()](https://www.php.net/manual/zh/function.is-object.php) 和 [is_string()](https://www.php.net/manual/zh/function.is-string.php)。参见[类型](https://www.php.net/manual/zh/language.types.php)一章。

## 输出语句

### echo和print

- echo - 可以输出一个或多个字符串
- print - 只允许输出一个字符串，返回值总为 1

**提示：**echo 输出的速度比 print 快， echo 没有返回值，print有返回值1。

### echo

使用的时候可以不用加括号，也可以加上括号： echo 或 echo()。输出的字符串可以带有html标签，多个串用逗号隔开。

实例：

```php
<?php
$txt1="学习 PHP";
$txt2="RUNOOB.COM";
$cars=array("Volvo","BMW","Toyota");
 
echo $txt1;
echo "<br>";
echo "在 $txt2 学习 PHP ";
echo "<br>";
echo "我车的品牌是 {$cars[0]}";
?>
```

### print

可以使用括号，也可以不使用括号： print 或 print()。使用与echo基本相同。

## 数据类型

### String

放在单引号双引号中均可。

单引号除了单引号本身和反斜线以外其它均不转义。

当字符串用双引号或 heredoc 结构定义时，其中的[变量](https://www.php.net/manual/zh/language.variables.php)将会被解析。

PHP 的字符串在内部是字节组成的数组。因此用花括号访问或修改字符串对多字节字符集很不安全。

#### heredoc

*<<<xxx*。

要注意的是结束标识符这行除了*可能*有一个分号（*;*）外，绝对不能包含其它字符。这意味着标识符*不能缩进*，分号的前后也不能有任何空白或制表符。更重要的是结束标识符的前面必须是个被本地操作系统认可的换行，比如在 UNIX 和 Mac OS X 系统中是 *\n*，而结束定界符（可能其后有个分号）之后也必须紧跟一个换行。

```php
<?php
class foo {
    public $bar = <<<EOT
bar
    EOT;  //非法！！！
}
?>
```

#### nowdoc

*<<<'xxx'*。

与heredoc结构相似，但不对特殊字符转义。（>php5.3.0）

#### 变量解析

* 简单语法

  遇 `$` 尝试解析。

* 复杂语法

  花括号括起。里面可以写复杂的表达式。只有 *$* 紧挨着 *{* 时才会被识别。

#### 存取和修改字符

用方括号或花括号访问。

### Int

不能包含逗号和空格，无小数点，可正可负，可以是十进制， 十六进制（ 以 0x 为前缀）或八进制（前缀为 0）。

### Float

带小数的或是指数形式的。例如： 1.2，2.4e3，8E-5。

### Bool

true或false。

### Array

有序映射。映射是一种把 *values* 关联到 *keys* 的类型。由于数组元素的值也可以是另一个数组，树形结构和多维数组也是允许的。

#### 定义数组

可以用 [array()](https://www.php.net/manual/zh/function.array.php) 语言结构来新建一个数组。它接受任意数量用逗号分隔的 *键（key） => 值（value）*对。

自 5.4 起可以使用短数组定义语法，用 *[]* 替代 *array()*。

```php
<?php
$array = array(
    "foo" => "bar",
    "bar" => "foo",
);

// 自 PHP 5.4 起
$array = [
    "foo" => "bar",
    "bar" => "foo",
];
?>
```

key 可以是 [integer](https://www.php.net/manual/zh/language.types.integer.php) 或者 [string](https://www.php.net/manual/zh/language.types.string.php)。value 可以是任意类型。

- 包含有合法整型值的字符串会被转换为整型。例如键名 *"8"* 实际会被储存为 *8*。但是 *"08"* 则不会强制转换，因为其不是一个合法的十进制数值。
- 浮点数也会被转换为整型，意味着其小数部分会被舍去。例如键名 *8.7* 实际会被储存为 *8*。
- 布尔值也会被转换成整型。即键名 *true* 实际会被储存为 *1* 而键名 *false* 会被储存为 *0*。
- [Null](https://www.php.net/manual/zh/language.types.null.php) 会被转换为空字符串，即键名 *null* 实际会被储存为 *""*。
- 数组和对象*不能*被用为键名。坚持这么做会导致警告：*Illegal offset type*。

**如果在数组定义中多个单元都使用了同一个键名，则只使用了最后一个，之前的都被覆盖了。**

PHP 数组可以同时含有 [integer](https://www.php.net/manual/zh/language.types.integer.php) 和 [string](https://www.php.net/manual/zh/language.types.string.php) 类型的键名，因为 PHP 实际并不区分索引数组和关联数组。

如果未指定key，PHP 将自动使用之前用过的最大 [integer](https://www.php.net/manual/zh/language.types.integer.php) 键名加上 1 作为新的键名，之前没用过integer键名的取key值为0（注意这里所使用的最大整数键名*不一定*当前就在数组中。它只要在上次数组重新生成索引后曾经存在过就行了。）：

```php
<?php
$array = array(
    "foo" => "bar",
    "bar" => "foo",
    100   => -100,
    -100  => 100,
);
$array[] = "a";
var_dump($array);
    
/*
array(5) {
  ["foo"]=>
  string(3) "bar"
  ["bar"]=>
  string(3) "foo"
  [100]=>
  int(-100)
  [-100]=>
  int(100)
  [101]=>
  string(1) "a"
}
*/
```

还可以只对某些单元指定键名而对其它的空置：

```php
<?php
$array = array(
         "a",
         "b",
    6 => "c",
         "d",
);
var_dump($array);

/*
array(4) {
  [0]=>
  string(1) "a"
  [1]=>
  string(1) "b"
  [6]=>
  string(1) "c"
  [7]=>
  string(1) "d"
}
*/
```

#### 访问数组单元

```php
<?php
$array = array(
    "foo" => "bar",
    42    => 24,
    "multi" => array(
         "dimensional" => array(
             "array" => "foo"
         )
    )
);

var_dump($array["foo"]);
var_dump($array[42]);
var_dump($array["multi"]["dimensional"]["array"]);
?>
```

> **Note**:
>
> 方括号和花括号可以互换使用来访问数组单元（例如 $array[42] 和 $array{42} 在上例中效果相同）。

#### 新建、修改、删除值

要修改某个值，通过其键名给该单元赋一个新值。要删除某键值对，对其调用 [unset()](https://www.php.net/manual/zh/function.unset.php) 函数。

```php
<?php
$arr = array(5 => 1, 12 => 2);

$arr[] = 56;    // This is the same as $arr[13] = 56;
                // at this point of the script

$arr["x"] = 42; // This adds a new element to
                // the array with key "x"
                
unset($arr[5]); // This removes the element from the array

unset($arr);    // This deletes the whole array
?>
```

#### 数组解引用

自 PHP 5.4 起可以用直接对函数或方法调用的结果进行数组解引用，在此之前只能通过一个临时变量。

自 PHP 5.5 起可以直接对一个数组原型进行数组解引用。

```php
<?php
function getArray() {
    return array(1, 2, 3);
}

// on PHP 5.4
$secondElement = getArray()[1];

// previously
$tmp = getArray();
$secondElement = $tmp[1];

// or
list(, $secondElement) = getArray();
?>
```

#### 数组实用函数

[unset()](https://www.php.net/manual/zh/function.unset.php) 函数允许删除数组中的某个键。但要注意数组将*不会*重建索引。如果需要删除后重建索引，可以用 [array_values()](https://www.php.net/manual/zh/function.array-values.php) 函数。

```php
<?php
$a = array(1 => 'one', 2 => 'two', 3 => 'three');
unset($a[2]);
/* will produce an array that would have been defined as
   $a = array(1 => 'one', 3 => 'three');
   and NOT
   $a = array(1 => 'one', 2 =>'three');
*/

$b = array_values($a);
// Now $b is array(0 => 'one', 1 =>'three')
?>
```

此外，foreach函数专门用于数组。（简单遍历）

#### 注意

应该始终在用字符串表示的数组索引上加上引号。而键名为常量和变量的不需要加引号。

可用函数返回值做数组索引。也可用已知常量。

#### 转换为数组

对于任意 [integer](https://www.php.net/manual/zh/language.types.integer.php)，[float](https://www.php.net/manual/zh/language.types.float.php)，[string](https://www.php.net/manual/zh/language.types.string.php)，[boolean](https://www.php.net/manual/zh/language.types.boolean.php) 和 [resource](https://www.php.net/manual/zh/language.types.resource.php) 类型，如果将一个值转换为数组，将得到一个仅有一个元素的数组，其下标为 0，该元素即为此标量的值。换句话说，*(array)$scalarValue* 与 *array($scalarValue)* 完全一样。

如果一个 [object](https://www.php.net/manual/zh/language.types.object.php) 类型转换为 [array](https://www.php.net/manual/zh/language.types.array.php)，则结果为一个数组，其单元为该对象的属性。键名将为成员变量名，不过有几点例外：整数属性不可访问；私有变量前会加上类名作前缀；保护变量前会加上一个 '*' 做前缀。这些前缀的前后都各有一个 NULL 字符。这会导致一些不可预知的行为：

```php
<?php

class A {
    private $A; // This will become '\0A\0A'
}

class B extends A {
    private $A; // This will become '\0B\0A'
    public $AA; // This will become 'AA'
}

var_dump((array) new B());
?>
```

#### 比较

array_diff()，数组运算符。

### Object

#### 初始化

new 语句。

#### 转换为对象

如果其它任何类型的值被转换成对象，将会创建一个内置类 *stdClass* 的实例。如果该值为 **`NULL`**，则新的实例为空。 [array](https://www.php.net/manual/zh/language.types.array.php) 转换成 [object](https://www.php.net/manual/zh/language.types.object.php) 将使键名成为属性名并具有相对应的值。注意：在这个例子里， 使用 PHP 7.2.0 之前的版本，数字键只能通过迭代访问。对于其他值，会包含进成员变量名 *scalar*。

```php
<?php
$obj = (object) 'ciao';
echo $obj->scalar;  // outputs 'ciao'
?>
```

### Resource

`get_resource_type` 返回资源类型。例如：

```php
<?php
$c = mysql_connect();
echo get_resource_type($c)."\n";
// 打印：mysql link

$fp = fopen("foo","w");
echo get_resource_type($fp)."\n";
// 打印：file

$doc = new_xmldoc("1.0");
echo get_resource_type($doc->doc)."\n";
// 打印：domxml document
?>
```

### Callback/Callable

自 PHP 5.4 起可用 [callable](https://www.php.net/manual/zh/language.types.callable.php) 类型指定回调类型 callback。

### NULL

NULL 值表示变量没有值。NULL 是数据类型为 NULL 的值。

### 伪类型与变量

#### mixed

一个参数可以接受多种不同的（但不一定是所有的）类型。

#### number

可以是 [integer](https://www.php.net/manual/zh/language.types.integer.php) 或者 [float](https://www.php.net/manual/zh/language.types.float.php)。

#### callback

在 PHP 5.4 引入 [callable](https://www.php.net/manual/zh/language.types.callable.php) 类型之前使用 了 [callback](https://www.php.net/manual/zh/language.pseudo-types.php#language.types.callback) 伪类型。二者含义完全相同。

#### array|object

既可以是 [array](https://www.php.net/manual/zh/language.types.array.php) 也可以是 [object](https://www.php.net/manual/zh/language.types.object.php)。

#### void

*void* 作为返回类型意味着函数的返回值是无用的。*void* 作为参数列表意味着函数不接受任何参数。

#### ...

在函数原型中，`$...` 表示*等等*的意思。当一个函数可以接受任意个参数时使用此变量名。

### 类型转换

#### 类型设置

settype设置变量类型。

settype ( [mixed](https://www.php.net/manual/zh/language.pseudo-types.php#language.types.mixed) `&$var` , string `$type` ) : bool

var是待转换变量。type是要转换的类型。返回值是成功与否。

实例：

```php
<?php
$foo = "5bar"; // string
$bar = true;   // boolean

settype($foo, "integer"); // $foo 现在是 5   (integer)
settype($bar, "string");  // $bar 现在是 "1" (string)
?>
```

#### 类型强制转换

在要转换的变量之前加上用括号括起来的目标类型。

允许的强制转换有：

- (int), (integer) - 转换为整形 [integer](https://www.php.net/manual/zh/language.types.integer.php)
- (bool), (boolean) - 转换为布尔类型 [boolean](https://www.php.net/manual/zh/language.types.boolean.php)
- (float), (double), (real) - 转换为浮点型 [float](https://www.php.net/manual/zh/language.types.float.php)
- (string) - 转换为字符串 [string](https://www.php.net/manual/zh/language.types.string.php)
- (array) - 转换为数组 [array](https://www.php.net/manual/zh/language.types.array.php)
- (object) - 转换为对象 [object](https://www.php.net/manual/zh/language.types.object.php)
- (unset) - 转换为 [NULL](https://www.php.net/manual/zh/language.types.null.php) (PHP 5)
- (binary) 转换和 b 前缀转换支持为 PHP 5.2.1 新增。转换为二进制字符串。

> **Note:** 可以将变量放置在双引号中的方式来代替将变量转换成字符串。

## 运算符

### 运算符优先级

| 结合方向 | 运算符                                                       | 附加信息                                                     |
| :------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| 无       | clone new                                                    | [clone](https://www.php.net/manual/zh/language.oop5.cloning.php) 和 [new](https://www.php.net/manual/zh/language.oop5.basic.php#language.oop5.basic.new) |
| 右       | ****                                                         | [算术运算符](https://www.php.net/manual/zh/language.operators.arithmetic.php) |
| 右       | *++* *--* *~* *(int)* *(float)* *(string)* *(array)* *(object)* *(bool)* *@* | [类型](https://www.php.net/manual/zh/language.types.php)、[递增／递减](https://www.php.net/manual/zh/language.operators.increment.php)、[错误控制](https://www.php.net/manual/zh/language.operators.errorcontrol.php) |
| 无       | *instanceof*                                                 | [类型](https://www.php.net/manual/zh/language.types.php)     |
| 右       | *!*                                                          | [逻辑运算符](https://www.php.net/manual/zh/language.operators.logical.php) |
| 左       | *** */* *%*                                                  | [算术运算符](https://www.php.net/manual/zh/language.operators.arithmetic.php) |
| 左       | *+* *-* *.*                                                  | [算术运算符](https://www.php.net/manual/zh/language.operators.arithmetic.php) 和 [字符串运算符](https://www.php.net/manual/zh/language.operators.string.php) |
| 左       | *<<* *>>*                                                    | [位运算符](https://www.php.net/manual/zh/language.operators.bitwise.php) |
| 无       | *<* *<=* *>* *>=*                                            | [比较运算符](https://www.php.net/manual/zh/language.operators.comparison.php) |
| 无       | *==* *!=* *===* *!==* *<>* *<=>*                             | [比较运算符](https://www.php.net/manual/zh/language.operators.comparison.php) |
| 左       | *&*                                                          | [位运算符](https://www.php.net/manual/zh/language.operators.bitwise.php) 和 [引用](https://www.php.net/manual/zh/language.references.php) |
| 左       | *^*                                                          | [位运算符](https://www.php.net/manual/zh/language.operators.bitwise.php) |
| 左       | *\|*                                                         | [位运算符](https://www.php.net/manual/zh/language.operators.bitwise.php) |
| 左       | *&&*                                                         | [逻辑运算符](https://www.php.net/manual/zh/language.operators.logical.php) |
| 左       | *\|\|*                                                       | [逻辑运算符](https://www.php.net/manual/zh/language.operators.logical.php) |
| 右       | *??*                                                         | [null 合并运算符](https://www.php.net/manual/zh/language.operators.comparison.php#language.operators.comparison.coalesce) |
| 左       | *? :*                                                        | [三元运算符](https://www.php.net/manual/zh/language.operators.comparison.php#language.operators.comparison.ternary) |
| 右       | *=* *+=* *-=* **=* ***=* */=* *.=* *%=* *&=* *\|=* *^=* *<<=* *>>=* | [赋值运算符](https://www.php.net/manual/zh/language.operators.assignment.php) |
| 右       | *yield from*                                                 | [yield from](https://www.php.net/manual/zh/language.generators.syntax.php#control-structures.yield.from) |
| 右       | *yield*                                                      | [yield](https://www.php.net/manual/zh/language.generators.syntax.php#control-structures.yield) |
| 左       | *and*                                                        | [逻辑运算符](https://www.php.net/manual/zh/language.operators.logical.php) |
| 左       | *xor*                                                        | [逻辑运算符](https://www.php.net/manual/zh/language.operators.logical.php) |
| 左       | *or*                                                         | [逻辑运算符](https://www.php.net/manual/zh/language.operators.logical.php) |

### 比较运算

注意 `==` 和 `===` 的区别。

特别注意的是，$a <> $b 与 $a != $b 表示相同的意思。$a <=> $b是结合比较运算符，当$a小于、等于、大于$b时分别返回一个小于、等于、大于0的[integer](https://www.php.net/manual/zh/language.types.integer.php) 值。 PHP7开始提供。

**多类型比较（按顺序）**

| 运算数 1 类型                                                | 运算数 2 类型                                                | 结果                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| [null](https://www.php.net/manual/zh/language.types.null.php) 或 [string](https://www.php.net/manual/zh/language.types.string.php) | [string](https://www.php.net/manual/zh/language.types.string.php) | 将 **`NULL`** 转换为 ""，进行数字或词汇比较                  |
| [bool](https://www.php.net/manual/zh/language.types.boolean.php) 或 [null](https://www.php.net/manual/zh/language.types.null.php) | 任何其它类型                                                 | 转换为 [bool](https://www.php.net/manual/zh/language.types.boolean.php)，**`FALSE`** < **`TRUE`** |
| [object](https://www.php.net/manual/zh/language.types.object.php) | [object](https://www.php.net/manual/zh/language.types.object.php) | 内置类可以定义自己的比较，不同类不能比较，相同类和数组同样方式比较属性（PHP 4 中），PHP 5 有其自己的[说明](https://www.php.net/manual/zh/language.oop5.object-comparison.php) |
| [string](https://www.php.net/manual/zh/language.types.string.php)，[resource](https://www.php.net/manual/zh/language.types.resource.php) 或 [number](https://www.php.net/manual/zh/language.pseudo-types.php#language.types.number) | [string](https://www.php.net/manual/zh/language.types.string.php)，[resource](https://www.php.net/manual/zh/language.types.resource.php) 或 [number](https://www.php.net/manual/zh/language.pseudo-types.php#language.types.number) | 将字符串和资源转换成数字，按普通数学比较                     |
| [array](https://www.php.net/manual/zh/language.types.array.php) | [array](https://www.php.net/manual/zh/language.types.array.php) | 具有较少成员的数组较小，如果运算数 1 中的键不存在于运算数 2 中则数组无法比较，否则挨个值比较（见下例） |
| [object](https://www.php.net/manual/zh/language.types.object.php) | 任何其它类型                                                 | [object](https://www.php.net/manual/zh/language.types.object.php) 总是更大 |
| [array](https://www.php.net/manual/zh/language.types.array.php) | 任何其它类型                                                 | [array](https://www.php.net/manual/zh/language.types.array.php) 总是更大 |

注意！由于由于浮点数 [float](https://www.php.net/manual/zh/language.types.float.php) 的内部表达方式，不应比较两个浮点数[float](https://www.php.net/manual/zh/language.types.float.php)是否相等。更多信息参见 [float](https://www.php.net/manual/zh/language.types.float.php)。

### 三目运算

表达式 *(expr1) ? (expr2) : (expr3)* 在 expr1 求值为 **`TRUE`** 时的值为 expr2，在 expr1 求值为 **`FALSE`** 时的值为 expr3。

自 PHP 5.3 起，可以省略三元运算符中间那部分。表达式 *expr1 ?: expr3* 在 expr1 求值为 **`TRUE`** 时返回 expr1，否则返回 expr3。

注意三元运算符是个语句，因此其求值不是变量，而是语句的结果。在一个通过引用返回的函数中语句 *return $var == 42 ? $a : $b;* 将不起作用，以后的 PHP 版本会为此发出一条警告。

### NULL合并运算

当 expr1 为 **`NULL`**，表达式 *(expr1) ?? (expr2)* 等同于 expr2，否则为 expr1。

尤其要注意，当不存在左侧的值时，此运算符也和 [isset()](https://www.php.net/manual/zh/function.isset.php) 一样不会产生警告。 对于 array 键尤其有用。

```php
<?php
// NULL 合并运算符的例子
$action = $_POST['action'] ?? 'default';

// 以上例子等同于于以下 if/else 语句
if (isset($_POST['action'])) {
    $action = $_POST['action'];
} else {
    $action = 'default';
}
?>
```

注意NULL 合并运算符是一个表达式，产生的也是表达式结果，而不是变量。在返回引用的函数里就无法使用这样的语句：*return $foo ?? $bar;*，还会提示警告。

NULL 合并运算符支持简单的嵌套：

```php
<?php

$foo = null;
$bar = null;
$baz = 1;
$qux = 2;

echo $foo ?? $bar ?? $baz ?? $qux; // 输出 1

?>
```

### 算数运算

除了加减乘除取模外还有：取反：`-$x` ，并置：`$a.$b`。

取反是取相反数，并置是连接两个字符串。

取反会先将待取反的变量转换为数值的形式。

### 赋值运算

没什么好说的。注意 $a .= $b 和 $a = $a . $b 表示的意思相同。

### 递增递减

x++, ++x, x--, --x。

### 逻辑运算

and与 / or或  /xor异或 / &&与 / ||或 / !非。

### 位运算

| 例子           | 名称                | 结果                                                     |
| :------------- | :------------------ | :------------------------------------------------------- |
| **`$a & $b`**  | And（按位与）       | 将把 $a 和 $b 中都为 1 的位设为 1。                      |
| **`$a | $b`**  | Or（按位或）        | 将把 $a 和 $b 中任何一个为 1 的位设为 1。                |
| **`$a ^ $b`**  | Xor（按位异或）     | 将把 $a 和 $b 中一个为 1 另一个为 0 的位设为 1。         |
| **`~ $a`**     | Not（按位取反）     | 将 $a 中为 0 的位设为 1，反之亦然。                      |
| **`$a << $b`** | Shift left（左移）  | 将 $a 中的位向左移动 $b 次（每一次移动都表示“乘以 2”）。 |
| **`$a >> $b`** | Shift right（右移） | 将 $a 中的位向右移动 $b 次（每一次移动都表示“除以 2”）。 |

位移在 PHP 中是数学运算。向任何方向（左右）移出去的位都被丢弃。左移时右侧以零填充，**符号位被移走意味着正负号不被保留。**右移时左侧以符号位填充，意味着正负号被保留。

若&，/，^左右是字符串，则对ASCII做运算以后得到字符串结果，对于其它类型全部转成Integer，得到Integer结果。~操作同理。<<和>>左右两边都被当作 Integer 处理，String也不会转成ASCII。

PHP 的 ini 设定 error_reporting 使用了按位的值，提供了关闭某个位的实例。

```
E_ALL ^ E_NOTICE
具体运作方式是先取得 E_ALL 的值：
00000000000000000111011111111111
再取得 E_NOTICE 的值：
00000000000000000000000000001000
最后再用^得到两个值中都设定了（为 1）的位：
00000000000000000111011111110111
```

### 错误控制运算

@符号。当将其放置在一个 PHP 表达式之前，该表达式可能产生的任何错误信息都被忽略掉。

如果用 [set_error_handler()](https://www.php.net/manual/zh/function.set-error-handler.php) 设定了自定义的错误处理函数，仍然会被调用，但是此错误处理函数可以（并且也应该）调用 [error_reporting()](https://www.php.net/manual/zh/function.error-reporting.php)，而该函数在出错语句前有 @ 时将返回 0。

如果激活了 [**track_errors**](https://www.php.net/manual/zh/errorfunc.configuration.php#ini.track-errors) 特性，表达式所产生的任何错误信息都被存放在变量 [**$php_errormsg**](https://www.php.net/manual/zh/reserved.variables.phperrormsg.php) 中。此变量在每次出错时都会被覆盖，所以如果想用它的话就要尽早检查。

### 执行运算

反引号（``）。PHP 将尝试将反引号中的内容作为 shell 命令来执行，并将其输出信息返回（即，可以赋给一个变量而不是简单地丢弃到标准输出）。使用反引号运算符的效果与函数 [shell_exec()](https://www.php.net/manual/zh/function.shell-exec.php) 相同。

注：exec命令，如果指定了command，它就会取代当前的shell而不是创建新的进程。

```php
<?php
$output = `ls -al`;
echo "<pre>$output</pre>";
?>
```

反引号运算符在激活了[安全模式](https://www.php.net/manual/zh/ini.sect.safe-mode.php#ini.safe-mode)或者关闭了 [shell_exec()](https://www.php.net/manual/zh/function.shell-exec.php) 时是无效的。

与其它某些语言不同，反引号不能在双引号字符串中使用。但是，由变量搭载则可以使用，如上例。

### 数组运算

| 例子      | 名称   | 结果                                                         |
| :-------- | :----- | :----------------------------------------------------------- |
| $a + $b   | 联合   | $a 和 $b 的联合。                                            |
| $a == $b  | 相等   | 如果 $a 和 $b 具有相同的键／值对则为 **`TRUE`**。            |
| $a === $b | 全等   | 如果 $a 和 $b 具有相同的键／值对并且顺序和类型都相同则为 **`TRUE`**。 |
| $a != $b  | 不等   | 如果 $a 不等于 $b 则为 **`TRUE`**。                          |
| $a <> $b  | 不等   | 如果 $a 不等于 $b 则为 **`TRUE`**。                          |
| $a !== $b | 不全等 | 如果 $a 不全等于 $b 则为 **`TRUE`**。                        |

*+* 运算符把右边的数组元素附加到左边的数组后面，两个数组中都有的键名，则只用左边数组中的，右边的被忽略。

### 类型运算

*instanceof* 用于确定一个 PHP 变量是否属于某一类的实例：

```php
<?php
class MyClass
{
}

class NotMyClass
{
}
$a = new MyClass;

var_dump($a instanceof MyClass);   //bool(true)
var_dump($a instanceof NotMyClass);   //bool(false)
?>
```

也可以用来确定一个变量是不是继承自某一父类的子类的实例：

```php
<?php
class ParentClass
{
}

class MyClass extends ParentClass
{
}

$a = new MyClass;

var_dump($a instanceof MyClass);
var_dump($a instanceof ParentClass);   //bool(true)
?>
```

可以用 `!` 检查对象不是某个类的实例。

也可用于确定一个变量是不是实现了某个[接口](https://www.php.net/manual/zh/language.oop5.interfaces.php)的对象的实例。

虽然 *instanceof* 通常直接与类名一起使用，但也可以使用对象或字符串变量。

instanceof 的使用还有一些陷阱必须了解。在 PHP 5.1.0 之前，如果要检查的类名称不存在，*instanceof* 会调用 [__autoload()](https://www.php.net/manual/zh/function.autoload.php)。另外，如果该类没有被装载则会产生一个致命错误。可以通过使用动态类引用或用一个包含类名的字符串变量来避开这种问题。

*instanceof* 运算符是 PHP 5 引进的。在此之前用 [is_a()](https://www.php.net/manual/zh/function.is-a.php)，但是后来 [is_a()](https://www.php.net/manual/zh/function.is-a.php) 被废弃而用 *instanceof* 替代了。注意自 PHP 5.3.0 起，又恢复使用 [is_a()](https://www.php.net/manual/zh/function.is-a.php) 了。参见 [get_class()](https://www.php.net/manual/zh/function.get-class.php) 和 [is_a()](https://www.php.net/manual/zh/function.is-a.php)。

## 流程控制

### 不必多说的

if，else，while, do while, for

### elseif/else if

必须要注意的是 *elseif* 与 *else if* 只有在使用花括号的情况下才认为是完全相同。如果用冒号来定义 *if*/*elseif* 条件，那就不能用两个单词的 *else if*，否则 PHP 会产生解析错误。

```php
<?php

/* 不正确的使用方法： */
if ($a > $b):
    echo $a." is greater than ".$b;
else if ($a == $b): // 将无法编译
    echo "The above line causes a parse error.";
endif;


/* 正确的使用方法： */
if ($a > $b):
    echo $a." is greater than ".$b;
elseif ($a == $b): // 注意使用了一个单词的 elseif
    echo $a." equals ".$b;
else:
    echo $a." is neither greater than or equal to ".$b;
endif;

?>
```

### 流程控制的替代语法

PHP 提供了一些流程控制的替代语法，包括 *if*，*while*，*for*，*foreach* 和 *switch*。替代语法的基本形式是把左花括号（{）换成冒号（:），把右花括号（}）分别换成 `endif;`，`endwhile;`，`endfor;`，`endforeach;` 以及 `endswitch;`。

```php
<?php if ($a == 5): ?>
A is equal to 5
<?php endif; ?>
```

在上面的例子中，HTML 内容“A is equal to 5”用替代语法嵌套在 *if* 语句中。该 HTML 的内容仅在 $a 等于 5 时显示。

不支持在同一个控制块内混合使用两种语法。

*switch* 和第一个 *case* 之间的任何输出（含空格）将导致语法错误。例如，这样是无效的：

```php
<?php switch ($foo): ?>
    <?php case 1: ?>
    ...
<?php endswitch ?>
```

而这样是有效的，因为 *switch* 之后的换行符被认为是结束标记 *?>* 的一部分，所以在 *switch* 和 *case* 之间不能有任何输出：

```php
<?php switch ($foo): ?>
<?php case 1: ?>
    ...
<?php endswitch ?>
```

### foreach

*foreach* 语法结构提供了遍历数组的简单方式。*foreach* 仅能够应用于数组和对象，如果尝试应用于其他数据类型的变量，或者未初始化的变量将发出错误信息。有两种语法：

```php
foreach (array_expression as $value)
    statement
foreach (array_expression as $key => $value)
    statement
```

第一种格式遍历给定的 *array_expression* 数组。每次循环中，当前单元的值被赋给 *$value* 并且数组内部的指针向前移一步（因此下一次循环中将会得到下一个单元）。

第二种格式做同样的事，只除了当前单元的键名也会在每次循环中被赋给变量 *$key*。

当 *foreach* 开始执行时，数组内部的指针会自动指向第一个单元。这意味着不需要在 *foreach* 循环之前调用 [reset()](https://www.php.net/manual/zh/function.reset.php)。

可以很容易地通过在 *$value* 之前加上 & 来修改数组的元素。此方法将以[引用](https://www.php.net/manual/zh/language.references.php)赋值而不是拷贝一个值。

```php
<?php
$arr = array(1, 2, 3, 4);
foreach ($arr as &$value) {
    $value = $value * 2;
}
// $arr is now array(2, 4, 6, 8)
unset($value); // 最后取消掉引用
?>
```

*$value* 的引用仅在被遍历的数组可以被引用时才可用（例如是个变量）。以下代码则无法运行：

```php
<?php
foreach (array(1, 2, 3, 4) as &$value) {
    $value = $value * 2;
}
?>
```

foreach* 不支持用“@”来抑制错误信息的能力。

以下代码功能完全相同：

```php
<?php
$arr = array("one", "two", "three");
reset($arr);
while (list(, $value) = each($arr)) {
    echo "Value: $value<br>\n";
}

foreach ($arr as $value) {
    echo "Value: $value<br />\n";
}
?>
```

```php
<?php
$arr = array("one", "two", "three");
reset($arr);
while (list($key, $value) = each($arr)) {
    echo "Key: $key; Value: $value<br />\n";
}

foreach ($arr as $key => $value) {
    echo "Key: $key; Value: $value<br />\n";
}
?>
```

#### 用 list() 给嵌套的数组解包[ ¶](https://www.php.net/manual/zh/control-structures.foreach.php#control-structures.foreach.list)

*(PHP 5 >= 5.5.0, PHP 7)*

PHP 5.5 增添了遍历一个数组的数组的功能并且把嵌套的数组解包到循环变量中，只需将 [list()](https://www.php.net/manual/zh/function.list.php) 作为值提供。

例如：

```php
<?php
$array = [
    [1, 2],
    [3, 4],
];

foreach ($array as list($a, $b)) {
    // $a contains the first element of the nested array,
    // and $b contains the second element.
    echo "A: $a; B: $b\n";
}
?>
/*
A: 1; B: 2
A: 3; B: 4
*/
```

### break/continue

PHP中 *break*/continue 可以接受一个可选的数字参数来决定跳出几重循环。

***break\* 的更新记录**

| 版本  | 说明                                                     |
| :---- | :------------------------------------------------------- |
| 5.4.0 | *break 0;* 不再合法。这在之前的版本被解析为 *break 1;*。 |
| 5.4.0 | 取消变量作为参数传递（例如 *$num = 2; break $num;*）。   |

continue同break。

### switch

注意和其它语言不同，[continue](https://www.php.net/manual/zh/control-structures.continue.php) 语句作用到 switch 上的作用类似于 *break*。如果在循环中有一个 switch 并希望 continue 到外层循环中的下一轮循环，用 *continue 2*。

**注意 switch/case 作的是[松散比较](https://www.php.net/manual/zh/types.comparisons.php#types.comparisions-loose)。**

*switch* 语句一行接一行地执行（实际上是语句接语句）。开始时没有代码被执行。仅当一个 *case* 语句中的值和 *switch* 表达式的值匹配时 PHP 才开始执行语句，**直到 *switch* 的程序段结束或者遇到第一个 *break* 语句为止。如果不在 case 的语句段最后写上 *break* 的话，PHP 将继续执行下一个 case 中的语句段。**

在一个 case 中的语句也可以为空，这样只不过将控制转移到了下一个 case 中的语句。

```php
<?php
switch ($i) {
    case 0:
    case 1:
    case 2:
        echo "i is less than 3 but not negative";
        break;
    case 3:
        echo "i is 3";
}
?>
```

一个 case 的特例是 *default*。它匹配了任何和其它 case 都不匹配的情况。例如：

```php
<?php
switch ($i) {
    case 0:
        echo "i equals 0";
        break;
    case 1:
        echo "i equals 1";
        break;
    case 2:
        echo "i equals 2";
        break;
    default:
        echo "i is not equal to 0, 1 or 2";
}
?>
```

允许使用分号代替 case 语句后的冒号。

### declare

*declare* 结构用来设定一段代码的执行指令。*declare* 的语法和其它流程控制结构相似：

```php
declare (directive)
    statement
```

*directive* 部分允许设定 *declare* 代码段的行为。目前只认识两个指令：*ticks*（更多信息见下面 [ticks](https://www.php.net/manual/zh/control-structures.declare.php#control-structures.declare.ticks) 指令）以及 *encoding*（更多信息见下面 [encoding](https://www.php.net/manual/zh/control-structures.declare.php#control-structures.declare.encoding) 指令）。

> **Note**: encoding 是 PHP 5.3.0 新增指令。

*declare* 结构也可用于全局范围，影响到其后的所有代码（但如果有 *declare* 结构的文件被其它文件包含，则对包含它的父文件不起作用）。

```php
<?php
// these are the same:

// you can use this:
declare(ticks=1) {
    // entire script here
}

// or you can use this:
declare(ticks=1);
// entire script here
?>
```

#### Tick

Tick（时钟周期）是一个在 *declare* 代码段中解释器每执行 N 条可计时的低级语句就会发生的事件。N 的值是在 *declare* 中的 *directive* 部分用 `ticks=N` 来指定的。

不是所有语句都可计时。通常条件表达式和参数表达式都不可计时。

在每个 tick 中出现的事件是由 [register_tick_function()](https://www.php.net/manual/zh/function.register-tick-function.php) 来指定的。更多细节见下面的例子。注意每个 tick 中可以出现多个事件。

```php
<?php

declare(ticks=1);

// A function called on each tick event
function tick_handler()
{
    echo "tick_handler() called\n";
}

register_tick_function('tick_handler');

$a = 1;

if ($a > 0) {
    $a += 2;
    print($a);
}

?>
    
/*
tick_handler() called
tick_handler() called
tick_handler() called
3tick_handler() called
*/

//和上面的效果相同
<?php

function tick_handler()
{
  echo "tick_handler() called\n";
}

$a = 1;
tick_handler();

if ($a > 0) {
    $a += 2;
    tick_handler();
    print($a);
    tick_handler();
}
tick_handler();

?>
```

#### Encoding

可以用 encoding 指令来对每段脚本指定其编码方式。

```php
<?php
declare(encoding='ISO-8859-1');
// code here
?>
```

在 PHP 5.3 中除非在编译时指定了 *--enable-zend-multibyte*，否则 declare 中的 encoding 值会被忽略。注意除非用 [phpinfo()](https://www.php.net/manual/zh/function.phpinfo.php)，否则 PHP 不会显示出是否在编译时指定了 *--enable-zend-multibyte*。参见 [zend.script_encoding](https://www.php.net/manual/zh/ini.core.php#ini.zend.script-encoding)。

### return

如果在一个函数中调用 **return** 语句，将立即结束此函数的执行并将它的参数作为函数的值返回。**return** 也会终止 [eval()](https://www.php.net/manual/zh/function.eval.php) 语句或者脚本文件的执行。

如果在全局范围中调用，则当前脚本文件中止运行。如果当前脚本文件是被 [include](https://www.php.net/manual/zh/function.include.php) 的或者 [require](https://www.php.net/manual/zh/function.require.php) 的，则控制交回调用文件。此外，如果当前脚本是被 [include](https://www.php.net/manual/zh/function.include.php) 的，则 **return** 的值会被当作 [include](https://www.php.net/manual/zh/function.include.php) 调用的返回值。如果在主脚本文件中调用 **return**，则脚本中止运行。如果当前脚本文件是在 php.ini 中的配置选项 [auto_prepend_file](https://www.php.net/manual/zh/ini.core.php#ini.auto-prepend-file) 或者 [auto_append_file](https://www.php.net/manual/zh/ini.core.php#ini.auto-append-file) 所指定的，则此脚本文件中止运行。

> **Note**: 当用引用返回值时*永远不要*使用括号，这样行不通。只能通过引用返回变量，而不是语句的结果。如果使用 *return ($a);* 时其实**不是返回一个变量，而是表达式 *($a)* 的值**（当然，此时该值也正是 $a 的值）。

### include/require

包含文件用。区别是include遇错产警告，require遇错终止。

如果没有给出目录（只有文件名）时则按照 [include_path](https://www.php.net/manual/zh/ini.core.php#ini.include-path) 指定的目录寻找。如果在 [include_path](https://www.php.net/manual/zh/ini.core.php#ini.include-path) 下没找到该文件则 *include* 最后才在调用脚本文件所在的目录和当前工作目录下寻找。如果定义了路径（绝对和相对）[include_path](https://www.php.net/manual/zh/ini.core.php#ini.include-path) 会被完全忽略。

当一个文件被包含时，其中所包含的代码继承了 include 所在行的[变量范围](https://www.php.net/manual/zh/language.variables.scope.php)。从该处开始，调用文件在该行处可用的任何变量在被调用的文件中也都可用。不过所有在包含文件中定义的函数和类都具有全局作用域（?）。

如果 include 出现于调用文件中的一个函数里，则被调用的文件中所包含的所有代码将表现得如同它们是在该函数内部定义的一样。所以它将遵循该函数的变量范围。此规则的一个例外是[魔术常量](https://www.php.net/manual/zh/language.constants.predefined.php)，它们是在发生包含之前就已被解析器处理的。

```php
<?php

function foo()
{
    global $color;

    include 'vars.php';

    echo "A $color $fruit";
}

/* vars.php is in the scope of foo() so     *
 * $fruit is NOT available outside of this  *
 * scope.  $color is because we declared it *
 * as global.                               */

foo();                    // A green apple
echo "A $color $fruit";   // A green

?>
```

当一个文件被包含时，语法解析器在目标文件的开头脱离 PHP 模式并进入 HTML 模式，到文件结尾处恢复。由于此原因，目标文件中需要作为 PHP 代码执行的任何代码都必须被包括在[有效的 PHP 起始和结束标记](https://www.php.net/manual/zh/language.basic-syntax.phpmode.php)之中。（文件中的html内容会被直接显示出来。）

如果“[URL include wrappers](https://www.php.net/manual/zh/filesystem.configuration.php#ini.allow-url-include)”在 PHP 中被激活，可以用 URL（通过 HTTP 或者其它支持的封装协议——见[支持的协议和封装协议](https://www.php.net/manual/zh/wrappers.php)）而不是本地文件来指定要被包含的文件（远程包含）。如果目标服务器将目标文件作为 PHP 代码解释，则可以用适用于 HTTP GET 的 URL 请求字符串来向被包括的文件传递变量。严格的说这和包含一个文件并继承父文件的变量空间并不是一回事；该脚本文件实际上已经在远程服务器上运行了，而本地脚本则包括了其结果。

```php
<?php

/* This example assumes that www.example.com is configured to parse .php *
 * files and not .txt files. Also, 'Works' here means that the variables *
 * $foo and $bar are available within the included file.                 */

// Won't work; file.txt wasn't handled by www.example.com as PHP
include 'http://www.example.com/file.txt?foo=1&bar=2';

// Won't work; looks for a file named 'file.php?foo=1&bar=2' on the
// local filesystem.
include 'file.php?foo=1&bar=2';

// Works.
include 'http://www.example.com/file.php?foo=1&bar=2';

$foo = 1;
$bar = 2;
include 'file.txt';  // Works.
include 'file.php';  // Works.

?>
```

因为 *include* 是一个特殊的语言结构，其参数不需要括号。在比较其返回值时要注意。

```php
<?php
// won't work, evaluated as include(('vars.php') == TRUE), i.e. include('')
if (include('vars.php') == TRUE) {
    echo 'OK';
}

// works
if ((include 'vars.php') == TRUE) {
    echo 'OK';
}
?>
```

include和return：

```php
return.php
<?php

$var = 'PHP';

return $var;

?>

noreturn.php
<?php

$var = 'PHP';

?>

testreturns.php
<?php

$foo = include 'return.php';

echo $foo; // prints 'PHP'

$bar = include 'noreturn.php';

echo $bar; // prints 1

?>
```

如果在包含文件中定义有函数，这些函数不管是在 [return](https://www.php.net/manual/zh/function.return.php) 之前还是之后定义的，都可以独立在主文件中使用。如果文件被包含两次，PHP 5 发出致命错误因为函数已经被定义，但是 PHP 4 不会对在 [return](https://www.php.net/manual/zh/function.return.php) 之后定义的函数报错。推荐使用 [include_once](https://www.php.net/manual/zh/function.include-once.php) 而不是检查文件是否已包含并在包含文件中有条件返回。

将 PHP 文件“包含”到一个变量中的方法是用[输出控制函数](https://www.php.net/manual/zh/ref.outcontrol.php)结合 **include** 来捕获其输出，例如：

```php
<?php
$string = get_include_contents('somefile.php');

function get_include_contents($filename) {
    if (is_file($filename)) {
        ob_start();
        include $filename;
        $contents = ob_get_contents();
        ob_end_clean();
        return $contents;
    }
    return false;
}
?>
```

要在脚本中自动包含文件，参见 php.ini 中的 [auto_prepend_file](https://www.php.net/manual/zh/ini.core.php#ini.auto-prepend-file) 和 [auto_append_file](https://www.php.net/manual/zh/ini.core.php#ini.auto-append-file) 配置选项。

> **Note**: 因为是一个语言构造器而不是一个函数，不能被 [可变函数](https://www.php.net/manual/zh/functions.variable-functions.php) 调用。

### include_once/require_once

只包含一次。

> Note:
>
> 在 PHP 4中，_once 的行为在不区分大小写字母的操作系统（例如 Windows）中有所不同，例如：
>
> Example #1 include_once 在 PHP 4 运行于不区分大小写的操作系统中
>
> ```php
> <?php
> include_once "a.php"; // 这将包含 a.php
> include_once "A.php"; // 这将再次包含 a.php！（仅 PHP 4）
> ?>
> ```
> 此行为在 PHP 5 中改了，例如在 Windows 中路径先被规格化，因此 C:\PROGRA~1\A.php 和 C:\Program Files\a.php 的实现一样，文件只会被包含一次。

### goto

PHP 中的 *goto* 有一定限制，目标位置只能位于同一个文件和作用域，也就是说无法跳出一个函数或类方法，也无法跳入到另一个函数。也无法跳入到任何循环或者 switch 结构中。可以跳出循环或者 switch，通常的用法是用 *goto* 代替多层的 *break*。

```php
<?php
for($i=0,$j=50; $i<100; $i++) {
  while($j--) {
    if($j==17) goto end; 
  }  
}
echo "i = $i";
end:
echo 'j hit 17';
?>
    
// 无效写法
<?php
goto loop;
for($i=0,$j=50; $i<100; $i++) {
  while($j--) {
    loop:
  }
}
echo "$i = $i";
?>
```

> **Note**:
>
> goto 操作符仅在 PHP 5.3及以上版本有效。

## 函数

### 自定义函数

任何有效的 PHP 代码都有可能出现在函数内部，甚至包括其它函数和[类](https://www.php.net/manual/zh/language.oop5.basic.php#language.oop5.basic.class)定义。

函数无需在调用之前被定义，*除非*是下面两个例子中函数是有条件被定义时。当一个函数是有条件被定义时，必须在调用函数*之前*定义。

```php
<?php

$makefoo = true;

/* 不能在此处调用foo()函数，
   因为它还不存在，但可以调用bar()函数。*/

bar();

if ($makefoo) {
  function foo()
  {
    echo "I don't exist until program execution reaches me.\n";
  }
}

/* 现在可以安全调用函数 foo()了，
   因为 $makefoo 值为真 */

if ($makefoo) foo();

function bar()
{
  echo "I exist immediately upon program start.\n";
}

?>

    
<?php
function foo()
{
  function bar()
  {
    echo "I don't exist until foo() is called.\n";
  }
}

/* 现在还不能调用bar()函数，因为它还不存在 */

foo();

/* 现在可以调用bar()函数了，因为foo()函数
   的执行使得bar()函数变为已定义的函数 */

bar();

?>
```

### **递归函数**

```php
<?php
function recursion($a)
{
    if ($a < 20) {
        echo "$a\n";
        recursion($a + 1);
    }
}
?>
```

> **Note**: 但是要避免递归函数／方法调用超过 100-200 层，因为可能会使堆栈崩溃从而使当前脚本终止。 无限递归可视为编程错误。

### 函数参数

注意当使用默认参数时，任何默认参数必须放在任何非默认参数的右侧；否则，函数将不会按照预期的情况工作。考虑下面的代码片断：

**函数默认参数的不正确用法**

```php
<?php
function makeyogurt($type = "acidophilus", $flavour)
{
    return "Making a bowl of $type $flavour.\n";
}

echo makeyogurt("raspberry");   // won't work as expected
?>
```

以上例程会输出：

```
Warning: Missing argument 2 in call to makeyogurt() in 
/usr/local/etc/httpd/htdocs/phptest/functest.html on line 41
Making a bowl of raspberry .
```

正确用法：

```php
<?php
function makeyogurt($flavour, $type = "acidophilus")
{
    return "Making a bowl of $type $flavour.\n";
}

echo makeyogurt("raspberry");   // works as expected
?>
```

### 参数类型

| type                                                         | Description                                                  | Minimum PHP version |
| :----------------------------------------------------------- | :----------------------------------------------------------- | :------------------ |
| Class/interface name                                         | The parameter must be an [*instanceof*](https://www.php.net/manual/zh/language.operators.type.php) the given class or interface name. | PHP 5.0.0           |
| *self*                                                       | The parameter must be an [*instanceof*](https://www.php.net/manual/zh/language.operators.type.php) the same class as the one the method is defined on. This can only be used on class and instance methods. | PHP 5.0.0           |
| [array](https://www.php.net/manual/zh/language.types.array.php) | The parameter must be an [array](https://www.php.net/manual/zh/language.types.array.php). | PHP 5.1.0           |
| [callable](https://www.php.net/manual/zh/language.types.callable.php) | The parameter must be a valid [callable](https://www.php.net/manual/zh/language.types.callable.php). | PHP 5.4.0           |
| [bool](https://www.php.net/manual/zh/language.types.boolean.php) | The parameter must be a [boolean](https://www.php.net/manual/zh/language.types.boolean.php) value. | PHP 7.0.0           |
| [float](https://www.php.net/manual/zh/language.types.float.php) | The parameter must be a [float](https://www.php.net/manual/zh/language.types.float.php)ing point number. | PHP 7.0.0           |
| [int](https://www.php.net/manual/zh/language.types.integer.php) | The parameter must be an [integer](https://www.php.net/manual/zh/language.types.integer.php). | PHP 7.0.0           |
| [string](https://www.php.net/manual/zh/language.types.string.php) | The parameter must be a [string](https://www.php.net/manual/zh/language.types.string.php). | PHP 7.0.0           |
| *iterable*                                                   | The parameter must be either an [array](https://www.php.net/manual/zh/language.types.array.php) or an [*instanceof*](https://www.php.net/manual/zh/language.operators.type.php) **Traversable**. | PHP 7.1.0           |
| *object*                                                     | The parameter must be an [object](https://www.php.net/manual/zh/language.types.object.php). | PHP 7.2.0           |

**Nullable type declaration**

```php
<?php
class C {}

function f(C $c = null) {
    var_dump($c);
}

f(new C);
f(null);
?>
```

#### 严格类型

默认情况下，如果能做到的话，PHP将会强迫错误类型的值转为函数期望的标量类型。 例如，一个函数的一个参数期望是[string](https://www.php.net/manual/zh/language.types.string.php)，但传入的是[integer](https://www.php.net/manual/zh/language.types.integer.php)，最终函数得到的将会是一个[string](https://www.php.net/manual/zh/language.types.string.php)类型的值。

可以基于每一个文件开启严格模式。在严格模式中，只有一个与类型声明完全相符的变量才会被接受，否则将会抛出一个**TypeError**。 唯一的一个例外是可以将[integer](https://www.php.net/manual/zh/language.types.integer.php)传给一个期望[float](https://www.php.net/manual/zh/language.types.float.php)的函数。

实例：

```php
<?php
declare(strict_types=1);

function sum(int $a, int $b) {
    return $a + $b;
}

try {
    var_dump(sum(1, 2));
    var_dump(sum(1.5, 2.5));
} catch (TypeError $e) {
    echo 'Error: '.$e->getMessage();
}
?>
    
/*
int(3)
Error: Argument 1 passed to sum() must be of the type integer, float given, called in - on line 10
*/
```

#### 可变数量的参数列表

PHP 在用户自定义函数中支持可变数量的参数列表。在 PHP 5.6 及以上的版本中，由 *...* 语法实现；在 PHP 5.5 及更早版本中，使用函数 [func_num_args()](https://www.php.net/manual/zh/function.func-num-args.php)，[func_get_arg()](https://www.php.net/manual/zh/function.func-get-arg.php)，和 [func_get_args()](https://www.php.net/manual/zh/function.func-get-args.php) 。

*...* in PHP 5.6+，例如：

```php
<?php
function sum(...$numbers) {
    $acc = 0;
    foreach ($numbers as $n) {
        $acc += $n;
    }
    return $acc;
}

echo sum(1, 2, 3, 4);
?>
```

也可以用...把list解压到参数列表中：

```php
<?php
function add($a, $b) {
    return $a + $b;
}

echo add(...[1, 2])."\n";

$a = [1, 2];
echo add(...$a);
?>
```

在老版本（低于5.5）时，不需要特殊语法标注可变，但是需要使用函数，例如： [func_num_args()](https://www.php.net/manual/zh/function.func-num-args.php), [func_get_arg()](https://www.php.net/manual/zh/function.func-get-arg.php) 和 [func_get_args()](https://www.php.net/manual/zh/function.func-get-args.php).

实例：

```php
<?php
function sum() {
    $acc = 0;
    foreach (func_get_args() as $n) {
        $acc += $n;
    }
    return $acc;
}

echo sum(1, 2, 3, 4);
?>
```

### 返回值

函数不能返回多个值，但可以通过返回一个数组来得到类似的效果。

从函数返回一个引用，必须在函数声明和指派返回值给一个变量时都使用引用运算符 &。例如：

```php
<?php
function &returns_reference()
{
    return $someref;
}

$newref =& returns_reference();
?>
```

PHP 7 增加了对返回值类型声明的支持。在默认的弱模式中，如果返回值与返回值的类型不一致，则会被强制转换为返回值声明的类型。在强模式中，返回值的类型必须正确，否则将会抛出一个**TypeError**异常。

```php
<?php
declare(strict_types=1);

function sum($a, $b): int {
    return $a + $b;
}

var_dump(sum(1, 2));
var_dump(sum(1, 2.5));
?>
    
/*
int(3)

Fatal error: Uncaught TypeError: Return value of sum() must be of the type integer, float returned in - on line 5 in -:5
Stack trace:
#0 -(9): sum(1, 2.5)
#1 {main}
  thrown in - on line 5
*/
```

返回对象：

```php
<?php
class C {}

function getC(): C {
    return new C;
}

var_dump(getC());
?>
```

返回null（PHP 7.1.0+）：

```php
<?php
function get_item(): ?string {
    if (isset($_GET['item'])) {
        return $_GET['item'];
    } else {
        return null;
    }
}
?>
```

### 可变函数

一个变量名后有圆括号，PHP 将寻找与变量的值同名的函数，并且尝试执行它。可变函数可以用来实现包括回调函数，函数表在内的一些用途。

实例：

```php
<?php
class Foo
{
    function Variable()
    {
        $name = 'Bar';
        $this->$name(); // This calls the Bar() method
    }

    function Bar()
    {
        echo "This is Bar";
    }
}

$foo = new Foo();
$funcname = "Variable";
$foo->$funcname();   // This calls $foo->Variable()

?>
```

当调用静态方法时，函数调用要比静态属性优先。

调用数组和类中的方法，例：

```php
<?php
class Foo
{
    static function bar()
    {
        echo "bar\n";
    }
    function baz()
    {
        echo "baz\n";
    }
}

$func = array("Foo", "bar");
$func(); // prints "bar"
$func = array(new Foo, "baz");
$func(); // prints "baz"
$func = "Foo::bar";
$func(); // prints "bar" as of PHP 7.0.0; prior, it raised a fatal error
?>
```

| 版本  | 说明                                                         |
| :---- | :----------------------------------------------------------- |
| 7.0.0 | 'ClassName::methodName' is allowed as variable function.     |
| 5.4.0 | Arrays, which are valid callables, are allowed as variable functions. |

### 内置函数

PHP手册中每一个函数的页面中都有关于函数参数、行为改变、成功与否的返回值以及使用条件等信息。了解这些重要的（常常是细微的）差别是编写正确的 PHP 代码的关键。

有时需要加载模块（和扩展模块一起编译）以使用模块中的函数。

### 匿名函数

也叫闭包函数。允许临时创建一个没有指定名称的函数。

### Arrow函数

PHP 7.4之后新增的使匿名函数语法更简洁的函数。

## 类与对象

### 基本概念

#### 简单的类定义

```php
<?php
class SimpleClass
{
    // 声明属性
    public $var = 'a default value';

    // 声明方法
    public function displayVar() {
        echo $this->var;
    }
}
?>
```

当一个方法在类定义内部被调用时，有一个可用的伪变量 $this。$this 是一个到主叫对象的引用（通常是该方法所从属的对象，但如果是从第二个对象[静态](https://www.php.net/manual/zh/language.oop5.static.php)调用时也可能是另一个对象）。

示例：

```php
<?php
class A
{
    function foo()
    {
        if (isset($this)) {
            echo '$this is defined (';
            echo get_class($this);
            echo ")\n";
        } else {
            echo "\$this is not defined.\n";
        }
    }
}

class B
{
    function bar()
    {
        A::foo();
    }
}

$a = new A();
$a->foo();

A::foo();

$b = new B();
$b->bar();

B::bar();
?>
```

Output of the above example in PHP 5:

```php
$this is defined (A)
$this is not defined.
$this is defined (B)
$this is not defined.
```

Output of the above example in PHP 7:

```php
$this is defined (A)
$this is not defined.
$this is not defined.
$this is not defined.
```

#### new

```php
<?php
$instance = new SimpleClass();

// 也可以这样做：
$className = 'Foo';
$instance = new $className(); // Foo()
?>
```

在类定义内部，可以用 *new self* 和 *new parent* 创建新对象。

当把一个对象已经创建的实例赋给一个新变量时，新变量会访问同一个实例，就和用该对象赋值一样。此行为和给函数传递入实例时一样。可以用[克隆](https://www.php.net/manual/zh/language.oop5.cloning.php)给一个已创建的对象建立一个新实例。

#### 对象赋值

```php
<?php

$instance = new SimpleClass();

$assigned   =  $instance;
$reference  =& $instance;

$instance->var = '$assigned will have this value';

$instance = null; // $instance and $reference become null

var_dump($instance);
var_dump($reference);
var_dump($assigned);
?>
```

以上例程会输出：

```php
NULL
NULL
object(SimpleClass)#1 (1) {
   ["var"]=>
     string(30) "$assigned will have this value"
}
```

PHP 5.4.0 起，可以通过一个表达式来访问新创建对象的成员：

```php
<?php
echo (new DateTime())->format('Y');
?>
```

以上例程的输出类似于：

```
2016
```

### 属性

类的变量成员叫做“属性”。

属性声明是由关键字 *public*，*protected* 或者 *private* 开头，然后跟一个普通的变量声明来组成。属性中的变量可以初始化，但是初始化的值必须是常数，这里的常数是指 PHP 脚本在编译阶段时就可以得到其值，而不依赖于运行时的信息才能求值。

为了向后兼容 PHP 4，PHP 5 声明属性依然可以直接使用关键字 *var* 来替代（或者附加于）*public*，*protected* 或 *private*。但是已不再需要 *var* 了。如果直接使用 *var* 声明属性，而没有用 *public*，*protected* 或 *private* 之一，PHP 5 会将其视为 *public*。

在类的成员方法里面，可以用 *->*（对象运算符）：$this->property（其中 *property* 是该属性名）这种方式来访问非静态属性。静态属性则是用 *::*（双冒号）：self::$property 来访问。更多静态属性与非静态属性的区别参见 [Static 关键字](https://www.php.net/manual/zh/language.oop5.static.php)。

实例：

```php
<?php
class SimpleClass
{
   // 错误的属性声明
   public $var1 = 'hello ' . 'world';
   public $var2 = <<<EOD
hello world
EOD;
   public $var3 = 1+2;
   public $var4 = self::myStaticMethod();
   public $var5 = $myVar;

   // 正确的属性声明
   public $var6 = myConstant;
   public $var7 = array(true, false);

   //在 PHP 5.3.0 及之后，下面的声明也正确(newdoc)
   public $var8 = <<<'EOD'
hello world
EOD;
}
?>
```

### 类常量

可以把在类中始终保持不变的值定义为常量。在定义和使用常量的时候不需要使用 $ 符号。

常量的值必须是一个定值，不能是变量，类属性，数学运算的结果或函数调用。

实例：

```php
<?php
class MyClass
{
    const constant = 'constant value';

    function showConstant() {
        echo  self::constant . "\n";
    }
}

echo MyClass::constant . "\n";

$classname = "MyClass";
echo $classname::constant . "\n"; // 自 5.3.0 起

$class = new MyClass();
$class->showConstant();

echo $class::constant."\n"; // 自 PHP 5.3.0 起
?>
```

### 类的自动加载

在编写面向对象（OOP） 程序时，很多开发者为每个类新建一个 PHP 文件。 这会带来一个烦恼：每个脚本的开头，都需要包含（include）一个长长的列表（每个类都有个文件）。

在 PHP 5 中，已经不再需要这样了。 [spl_autoload_register()](https://www.php.net/manual/zh/function.spl-autoload-register.php) 函数可以注册任意数量的自动加载器，当使用尚未被定义的类（class）和接口（interface）时自动去加载。通过注册自动加载器，脚本引擎在 PHP 出错失败前有了最后一个机会加载所需的类。

自动加载不可用于 PHP 的 CLI [交互模式](https://www.php.net/manual/zh/features.commandline.php)。

实例：

```php
<?php
spl_autoload_register(function ($class_name) {
    require_once $class_name . '.php';
});

$obj  = new MyClass1();
$obj2 = new MyClass2();
?>
```

### 构造函数和析构函数

#### __construct()

创建新对象时先调用此方法。初始化。

> **Note**: 如果子类中定义了构造函数则不会隐式调用其父类的构造函数。要执行父类的构造函数，需要在子类的构造函数中调用 **parent::__construct()**。如果子类没有定义构造函数则会如同一个普通的类方法一样从父类继承（假如没有被定义为 private 的话）。

#### __destruct()

析构函数会在到某个对象的所有引用都被删除或者当对象被显式销毁时执行。

和构造函数一样，父类的析构函数不会被引擎暗中调用。要执行父类的析构函数，必须在子类的析构函数体中显式调用 **parent::__destruct()**。此外也和构造函数一样，子类如果自己没有定义析构函数则会继承父类的。

析构函数即使在使用 [exit()](https://www.php.net/manual/zh/function.exit.php) 终止脚本运行时也会被调用。在析构函数中调用 [exit()](https://www.php.net/manual/zh/function.exit.php) 将会中止其余关闭操作的运行。

> **Note**: 试图在析构函数（在脚本终止时被调用）中抛出一个异常会导致致命错误。

### 访问控制（可见性）

对属性或方法的访问控制，是通过在前面添加关键字 *public*（公有），*protected*（受保护）或 *private*（私有）来实现的。被定义为公有的类成员可以在任何地方被访问。被定义为受保护的类成员则可以被其自身以及其子类和父类访问。被定义为私有的类成员则只能被其定义所在的类访问。

#### 属性

类属性必须定义为公有，受保护，私有之一。如果用 *var* 定义，则被视为公有。

实例：

```php
<?php
/**
 * Define MyClass
 */
class MyClass
{
    public $public = 'Public';
    protected $protected = 'Protected';
    private $private = 'Private';

    function printHello()
    {
        echo $this->public;
        echo $this->protected;
        echo $this->private;
    }
}

$obj = new MyClass();
echo $obj->public; // 这行能被正常执行
echo $obj->protected; // 这行会产生一个致命错误
echo $obj->private; // 这行也会产生一个致命错误
$obj->printHello(); // 输出 Public、Protected 和 Private


/**
 * Define MyClass2
 */
class MyClass2 extends MyClass
{
    // 可以对 public 和 protected 进行重定义，但 private 而不能
    protected $protected = 'Protected2';

    function printHello()
    {
        echo $this->public;
        echo $this->protected;
        echo $this->private;
    }
}

$obj2 = new MyClass2();
echo $obj2->public; // 这行能被正常执行
echo $obj2->private; // 未定义 private
echo $obj2->protected; // 这行会产生一个致命错误
$obj2->printHello(); // 输出 Public、Protected2 和 Undefined

?>
```

#### 方法

类中的方法可以被定义为公有，私有或受保护。如果没有设置这些关键字，则该方法默认为公有。

实例：

```php
<?php
/**
 * Define MyClass
 */
class MyClass
{
    // 声明一个公有的构造函数
    public function __construct() { }

    // 声明一个公有的方法
    public function MyPublic() { }

    // 声明一个受保护的方法
    protected function MyProtected() { }

    // 声明一个私有的方法
    private function MyPrivate() { }

    // 此方法为公有
    function Foo()
    {
        $this->MyPublic();
        $this->MyProtected();
        $this->MyPrivate();
    }
}

$myclass = new MyClass;
$myclass->MyPublic(); // 这行能被正常执行
$myclass->MyProtected(); // 这行会产生一个致命错误
$myclass->MyPrivate(); // 这行会产生一个致命错误
$myclass->Foo(); // 公有，受保护，私有都可以执行


/**
 * Define MyClass2
 */
class MyClass2 extends MyClass
{
    // 此方法为公有
    function Foo2()
    {
        $this->MyPublic();
        $this->MyProtected();
        $this->MyPrivate(); // 这行会产生一个致命错误
    }
}

$myclass2 = new MyClass2;
$myclass2->MyPublic(); // 这行能被正常执行
$myclass2->Foo2(); // 公有的和受保护的都可执行，但私有的不行

class Bar 
{
    public function test() {
        $this->testPrivate();
        $this->testPublic();
    }

    public function testPublic() {
        echo "Bar::testPublic\n";
    }
    
    private function testPrivate() {
        echo "Bar::testPrivate\n";
    }
}

class Foo extends Bar 
{
    public function testPublic() {
        echo "Foo::testPublic\n";
    }
    
    private function testPrivate() {
        echo "Foo::testPrivate\n";
    }
}

$myFoo = new foo();
$myFoo->test(); // Bar::testPrivate 
                // Foo::testPublic
?>
```

### 继承

当扩展一个类，子类就会继承父类所有公有的和受保护的方法。除非子类覆盖了父类的方法，被继承的方法都会保留其原有功能。

```php
class bar extends foo
{
    // codes
}
```

### 范围解析操作符 （::）

可以用于访问[静态](https://www.php.net/manual/zh/language.oop5.static.php)成员，[类常量](https://www.php.net/manual/zh/language.oop5.constants.php)，还可以用于覆盖类中的属性和方法。

实例：

在类定义外部使用：

```php
<?php
class MyClass {
    const CONST_VALUE = 'A constant value';
}

$classname = 'MyClass';
echo $classname::CONST_VALUE; // 自 PHP 5.3.0 起

echo MyClass::CONST_VALUE;
?>
```

self，parent 和 static 这三个特殊的关键字是用于在类定义的内部对其属性或方法进行访问的。

在类定义内部使用：

```php
<?php
class OtherClass extends MyClass
{
    public static $my_static = 'static var';

    public static function doubleColon() {
        echo parent::CONST_VALUE . "\n";
        echo self::$my_static . "\n";
    }
}

$classname = 'OtherClass';
echo $classname::doubleColon(); // 自 PHP 5.3.0 起

OtherClass::doubleColon();
?>
```

调用父类方法：

```php
<?php
class MyClass
{
    protected function myFunc() {
        echo "MyClass::myFunc()\n";
    }
}

class OtherClass extends MyClass
{
    // 覆盖了父类的定义
    public function myFunc()
    {
        // 但还是可以调用父类中被覆盖的方法
        parent::myFunc();
        echo "OtherClass::myFunc()\n";
    }
}

$class = new OtherClass();
$class->myFunc();
?>
```

### Static

声明类属性或方法为静态，就可以不实例化类而直接访问。静态属性不能通过一个类已实例化的对象来访问（但静态方法可以）。

由于静态方法不需要通过对象即可调用，所以伪变量 $this 在静态方法中不可用。

静态属性不可以由对象通过 -> 操作符来访问。

### 抽象类

定义为抽象的类不能被实例化。如果它里面至少有一个方法是被声明为抽象的，那么这个类就必须被声明为抽象的。继承一个抽象类的时候，子类必须定义父类中的所有抽象方法；另外，这些方法的[访问控制](https://www.php.net/manual/zh/language.oop5.visibility.php)必须和父类中一样（或者更为宽松）。

### 重载

动态地创建类属性和方法。通过魔术方法（magic methods）来实现。

当调用当前环境下未定义或不[可见](https://www.php.net/manual/zh/language.oop5.visibility.php)的类属性或方法时，重载方法会被调用。

所有的重载方法都必须被声明为 *public*。

####  属性重载

在给不可访问属性赋值时，[__set()](https://www.php.net/manual/zh/language.oop5.overloading.php#object.set) 会被调用。

读取不可访问属性的值时，[__get()](https://www.php.net/manual/zh/language.oop5.overloading.php#object.get) 会被调用。

当对不可访问属性调用 [isset()](https://www.php.net/manual/zh/function.isset.php) 或 [empty()](https://www.php.net/manual/zh/function.empty.php) 时，[__isset()](https://www.php.net/manual/zh/language.oop5.overloading.php#object.isset) 会被调用。

当对不可访问属性调用 [unset()](https://www.php.net/manual/zh/function.unset.php) 时，[__unset()](https://www.php.net/manual/zh/language.oop5.overloading.php#object.unset) 会被调用。

#### 方法重载

在对象中调用一个不可访问方法时，[__call()](https://www.php.net/manual/zh/language.oop5.overloading.php#object.call) 会被调用。

在静态上下文中调用一个不可访问方法时，[__callStatic()](https://www.php.net/manual/zh/language.oop5.overloading.php#object.callstatic) 会被调用。

### 魔术方法

#### \_\_sleep()和\_\_wakeup()

序列化时先调用__sleep()方法。反序列话是wakeup方法。



