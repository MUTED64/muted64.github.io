---
title: PHP相关姿势
date: 2020-06-16 19:30:37
tags:
  - CTF
  - PHP
categories:
  - 网络安全
---

整理 CTF 中 PHP 相关的利用姿势，不定期补充。

<!-- more -->

## 弱类型相关姿势

弱类型的语言对变量的数据类型没有限制，你可以在任何地时候将变量赋值给任意的其他类型的变量，同时变量也可以转换成任意地其他类型的数据。

### 比较操作符

#### 类型转换

弱等的比较中不需左右两边的类型相等，会经过转换变成相同的类型后比较，由此产生安全问题。如：

```php
$a=NULL;$b=FALSE;  //$a==$b
$a='';$b=NULL;  //$a==$b
```

还有，bool类型的true跟任意字符串可以弱类型相等。

以及类型转换的一些规则，如字符串转数字会从不满足转换规则的位置开始截断只取最前边的数字部分而不会报错等。（ 注：数字到字符串可以用strval()，字符串和数字可以用intval() ）

```php
0 == '0'		//true
0 == 'abcdefg'	//true
0 === 'abcdefg'	//false
1 == '1abcdef'	//true
"0e123456"=="0e4456789"   //true
```

#### 哈希比较

##### md5 弱等的绕过

md5('240610708') == md5('QNKCDZO') (0exxxxxxx)

在进行比较运算时，如果遇到了0e\d+这种字符串，就会将这种字符串解析为科学计数法。所以上面例子中2个数的值都是0因而就相等了。如果不满足0e\d+这种模式就不会相等。

0e\d+ 的字符串整理：

```php
QNKCDZO
0e830400451993494058024219903391
s878926199a
0e545993274517709034328855841020
s155964671a
0e342768416822451524974117254469
s214587387a
0e848240448830537924465865611904
s214587387a
0e848240448830537924465865611904
s878926199a
0e545993274517709034328855841020
s1091221200a
0e940624217856561557816327384675
s1885207154a
0e509367213418206700842008763514
```



强等的绕过方法放在后文的 内置函数的参数松散性 一节中。

##### 绕过 md5()函数注入 MySQL

`ffifdyop` 这个字符串被 md5 哈希了之后会变成 276f722736c95d99e921722cf9ed621c，这个字符串前几位刚好是 ‘ or ‘6（`'or'6\xc9]\x99\xe9!r,\xf9\xedb\x1c`），而 Mysql 刚好又会把 hex 转成 ascii 解释，因此拼接之后的形式是 `select * from 'admin' where password='' or '6xxxxx'`

等价于 or 一个永真式，因此相当于万能密码，可以绕过 md5()函数。

或者 129581926211651571912466741651878684928，
md5 值为 `\x06\xdaT0D\x9f\x8fo#\xdf\xc1'or'8` 也可行。

#### 十六进制转换

还存在一种十六进制余字符串进行比较运算时的问题。例子如下：

```
"0x1e240"=="123456"		//true
"0x1e240"==123456		//true
"0x1e240"=="1e240"		//false
```

当其中的一个字符串是0x开头的时候，PHP会将此字符串解析成为十进制然后再进行比较，0x1e240解析成为十进制就是123456，所以与 int 类型和 string 类型的 123456 比较都是相等。攻防平台中的**起名字真难**就是考察的这个特性。

#### json绕过 

```php
<?php
if (isset($_POST['message'])) {
    $message = json_decode($_POST['message']);
    $key ="*********";
    if ($message->key ==$key ) {
        echo "flag";
    } 
    else {
        echo "fail";
    }
 }
 else{
     echo "~~~~";
 }
?>
```

输入一个json类型的字符串，json_decode函数解密成一个数组，判断数组中key的值是否等于$key的值，但$key的值我们不知道,这时我们构造一个和任意字符串返回为真的数组{“key”:true}。即可绕过。

payload: message={"key",true}

### 内置函数的参数松散性

#### md5()  强等的绕过

md5()中的需要是一个string类型的参数。如果md5 函数的参数是一个数组值，会导致函数返回 false。除了 md5 之外 sha1 函数也有这个特性。 例如传入为 `?a[]=1&b[]=2`

 或者找到 md5 值相等的二进制数据：

```url
a=%4d%c9%68%ff%0e%e3%5c%20%95%72%d4%77%7b%72%15%87%d3%6f%a7%b2%1b%dc%56%b7%4a%3d%c0%78%3e%7b%95%18%af%bf%a2%02%a8%28%4b%f3%6e%8e%4b%55%b3%5f%42%75%93%d8%49%67%6d%a0%d1%d5%5d%83%60%fb%5f%07%fe%a2

b=%4d%c9%68%ff%0e%e3%5c%20%95%72%d4%77%7b%72%15%87%d3%6f%a7%b2%1b%dc%56%b7%4a%3d%c0%78%3e%7b%95%18%af%bf%a2%00%a8%28%4b%f3%6e%8e%4b%55%b3%5f%42%75%93%d8%49%67%6d%a0%d1%55%5d%83%60%fb%5f%07%fe%a2
```

#### strcmp()

strcmp()函数在PHP官方手册中的描述是`int strcmp ( string $str1 , string $str2 )`,需要给strcmp()传递2个string类型的参数。如果str1小于str2,返回-1，相等返回0，否则返回1。strcmp函数比较字符串的本质是将两个变量转换为ascii，然后进行减法运算，然后根据运算结果来决定返回值。
如果传入给出strcmp()的参数是其它类型，可能产生其它不希望的结果。如：

```
$array=[1,2,3];
var_dump(strcmp($array,'123')); //null,在某种意义上null也就是相当于false，。
```

#### switch()

如果switch是数字类型的case的判断时，switch会将其中的参数转换为int类型。如下：

```
$i ="2abc";
switch ($i) {
case 0:
case 1:
case 2:
    echo "i is less than 3 but not negative";
    break;
case 3:
    echo "i is 3";
}
```

这个时候程序输出的是`i is less than 3 but not negative`，是由于switch()函数将$i进行了类型转换，转换结果为2。

#### in_array()

在PHP手册中，in_array()函数的解释是`bool in_array ( mixed $needle , array $haystack [, bool $strict = FALSE ] )`,如果strict参数没有提供，那么in_array就会使用松散比较来判断$needle是否在$haystack中。当strince的值为true时，needls的类型和haystack中的类型会被严格比较。

```
$array=[0,1,2,'3'];
var_dump(in_array('abc', $array));  //true
var_dump(in_array('1bc', $array));	//true
```

array_search()与in_array()也有一样的问题。

## PHP Filter 相关姿势

```
php://filter/read=convert.base64-encode/resource=
```