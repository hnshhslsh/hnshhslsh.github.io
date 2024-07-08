---
layout: post
title: "Ruby学习笔记（一） 基本概念、控制结构"
categories:
  - Ruby
tags:
  - Ruby
last_modified_at: 2017-01-20 12:15:25 +08:00







---

* content
{:toc}
这是我在学习 [SoloLearn](https://www.sololearn.com/) 的 [Ruby Tutorial](https://www.sololearn.com/Course/Ruby/) 时的笔记的第一部分，包括Ruby编程中的基本概念与控制结构。具体包括：输入输出、注释、变量常量、数学计算、字符串、布尔运算，`if`、`unless`、`case`条件语句，`while`、`until`、`for`、`loop do`循环语句。



在本文中我省略了该课程中的一些编程的基础概念，并且将Ruby的语法与其它语言的进行了一些比较，适合已经有一些其它编程语言的基础的朋友阅读。由于我水平有限，错误在所难免，如发现错误请在留言内指教，谢谢！

本文在本人新博客的链接：[http://www.myblog.link/2017/01/16/Note-of-Ruby-1/](http://www.myblog.link/2017/01/16/Note-of-Ruby-1/)

## 基本概念

### 基本输出

``` ruby
puts  "Hello World" # 输出Hello World并换行
print "Hello World" # 输出Hello World不换行
```

### 注释

``` ruby
# 行注释

=begin
段注释
=end
```

### 变量与常量

- 下面的语句申明变量x并赋值为8。变量的类型会被自动确定，重新赋值为不同类型的值将自动改变其类型。

  ``` ruby
  x = 8
  ```

- 变量名可以为字母、数字、下划线，不能以大写字母开头，以大写字母开头的为常量，赋初值后不能改变，否则会给出Warning。

- 使用变量的值。使用`#{变量名}`可以在双引号中的字符串里使用变量的值。

  ``` ruby
  puts x                          # 输出 8 
  puts "the value of x is #{x}" 	# 在字符串中使用变量的值，输出 the value of x is 8
  ```

### 数学计算

- 加减乘除模 `+` `-` `*` `/` `%` ，与C/C++/Java一致，`5/2`值为`2`，跟Javascript不一样。

- a的b次方表示为`a ** b` 。

- 有`x += 1`，没有`x++` 。

- Parallel Assignment：

  ``` ruby
  x,y,z = 10,20,30 # 给x,y,z分别赋值为10,20,30
  a,b = b,a        # 交换a、b变量的值
  ```

- 优先级： `**`大于`*` `/` `%`大于`+` `-` 。


### 字符串

- 字符串使用成对的单引号或者双引号：`'字符串'` `"字符串"`。

- 单引号的字符串里可以直接有`"`，要使用 `'` 需转义`\'`，要有 `\` 需使用 `\\` 转义，单引号中只将这两种视为转义字符。

- 双引号的字符串中可以直接有`'`，可以用`\n`换行，`#{表达式}`使用表达式的值等。

- 使用`+` 连接两个字符串，`'1' + '1'`的结果为字符串`'11'`，而不像`1 + 1`的值为`2`。 

  与Javascript不同，`'1' + 1`与`1 + '1'`均不合法。

- 使用`*`将字符串重复，`'abc' * 3 ` 的结果为 `'abcabcabc'`，`3 * 'abc'` 不合法。

### 输入

-  `gets` 输入字符串，带换行。

-  `gets.chomp` 输入字符串，不带换行。

-  `gets.to_i` 输入整数。


## 控制结构

### 布尔类型介绍

- 布尔类型：`true`与`false`表示真与假。`nil`（有点像于C/C++的`NULL`，Java的`null`，Javascript的`undefined`？）不属于布尔类型的值。

- Ruby可以将其它表达式自动转换成布尔，只有值为`false`或者`nil`的表达式会被转换成假，0也会被认为是真。这跟C/C++/Java都不一样。

- `==` `!=` `>` `<` `>=`  `<=` 与C/C++ 类似，它们也可以按字典序比较字符串。 

  `3 == 3.0`为真，要带类型一起比较需使用`3.eql?(3.0)`，值为假。

- `&&` `||` `!` 与 C/C++/Java 类似，带短路，也可使用`and` `or` `not` ，但不提倡。

### 条件语句

- `if`语句格式如下（可嵌套，`end`不可省略）：

  ``` ruby
  if 表达式1
    # 表达式1为真时要执行的语句
  elsif 表达式2
    # 表达式2为真时要执行的语句
  else
    # 前面所有表达式均为假时要执行的语句
  end
  ```

- `unless`语法与`if`类似，但是是条件为假时执行相应语句 

- `case` 语句格式如下（注意没有`break`，但是也只会进入其中的一个分支，跟其他语言的`switch`不同）：

  ``` ruby
    case 待求值判断的表达式
    when 值1
      # 表达式的值为值1时执行的语句
    when 值2,值3
      # 表达式的值为值2或值3时执行的语句
    when 开始值..结束值
      # 表达式的值为[开始值,结束值]时执行的语句
    else
      # 表达式的值为其它时执行的语句
    end
  ```

- ​`if` `unless`也可以后置，如
  ``` ruby
  puts "x is greater than 10" if  x > 10
  ```

### 循环语句

- `while`语法如下：

  ``` ruby
  while 循环条件
    # 待循环执行的语句
  end
  ```

- `until`语法与`while`类似，但是是条件为假时循环执行相应语句 。

- `for`使用一个范围进行循环

  - Ruby中可以使用`..`或者`...`表示一个范围，如`1..10`表示闭区间[1,10]，`1...10`表示前闭后开区间[1,10)， `"a".."z" `表示字符串`"a"`到`"z"`。 这样可以使用`a = (1..7).to_a`来创建一个包含[1,7]的数组`a`，也可将类似`when 1..7`的语法用在`case`语句中。
  - `for`循环中使用范围作为循环的条件，如：

    ``` ruby
    for i in 1..10
      puts i
    end
    ```

- `break`终止循环，`next` 跳过本次循环，`redo` 重复本次循环，`retry`从头开始整个循环。

- `loop do`创建始终继续的循环，直到使用`break`结束它

  ``` ruby
  loop do
    # 循环的语句
    break if 终止条件 # if后置的一个break语句，不一定要在循环体末尾
  end
  ```

  用`{` `}`可以替换 `do` 与 `end` 。
