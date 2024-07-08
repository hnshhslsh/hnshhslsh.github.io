---
layout: post
title: "Ruby学习笔记（四） 模块、Mixins、标准类与文件"
categories:
  - Ruby
tags:
  - Ruby
last_modified_at: 2018-02-24 21:17:56 +08:00
---

* content
{:toc}
这是我在学习 [SoloLearn](https://www.sololearn.com/) 的 [Ruby Tutorial](https://www.sololearn.com/Course/Ruby/) 时的笔记的第三部分，包括Ruby编程中的面向对象编程知识。具体包括模块、Mixins、标准类与文件。



在本文中我省略了该课程中的一些编程的基础概念，并且将Ruby的语法与其它语言的进行了一些比较，适合已经有一些其它编程语言的基础的朋友阅读。文中的加粗内容表示跟我以前接触过的其它语言（C/C++/Java/Javascript）看起来相似但不一致的地方。由于我水平有限，错误在所难免，如发现错误请在留言内指教，谢谢！

本文在本人新博客的链接：[http://www.myblog.link/2017/02/01/Note-of-Ruby-4/](http://www.myblog.link/2017/02/01/Note-of-Ruby-4/)

## 模块、Mixin与标准类

###  模块作为Mixin与命名空间

- 模块可以用来作为方法的集合来供其它类包含(include)。Ruby不允许一个类继承多个类，但允许一个类包含多个模块。include这个模块之后，该类就包含了这个模块的方法。这样的模块名一般为`动词able`的形式。感觉这类似于Java的接口。这样使用的模块被称为mixin。使用`module`来创建模块，使用`include`来包含模块。

  ``` ruby
  module ModuleName1		# 定义模块ModuleName1
    def method1			# 定义模块ModuleName1的方法method1
      puts "method1 called"
    end
  end

  module ModuleName2		# 定义模块ModuleName2
    def method2			# 定义模块ModuleName2的方法method2
      puts "method2 called"
    end
  end

  class ClassName
    include ModuleName1		# 包含ModuleName1
    include ModuleName2		# 包含ModuleName2
  end

  foo = ClassName.new
  foo.method1			# 调用模块ModuleName1的方法method1，输出 method1 called
  foo.method2			# 调用模块ModuleName2的方法method2，输出 method2 called
  ```

- `Comparable`模块是Ruby内置的一个mixin。包含该模块并定义了`<=>`方法的类就可以使用6种比较操作（ 运算符 `<`, `<=`, `==`, `>=`,  `>` 与方法`between?`）。`<=>`方法的规则是：小于返回-1，大于返回1，等于返回0，无法比较返回`nil`。

  ``` ruby
  class MyNumber
    attr_accessor :val
    include Comparable		# 包含Comparable
    def <=> other			# 定义<=>方法
      return @val <=> other.val
    end
  end
  ```

- 模块也可以作为命名空间来使用。模块中可以包含类、常量、类方法。模块中的常量使用`模块名::常量名`访问，类使用`模块名::类名`访问，`def self.方法名` 定义的方法可以用`模块名.方法名`访问。这样做可以防止命名时的冲突。

  ``` ruby
  module MyNamespace
    CONST = "CONST"		# 常量
    def self.myMethod		# 方法
      puts "myMethod called"
    end
    class ClassName		# 类
      def myInstanceMethod
        puts "instanceMethod in class called"
      end
    end
  end

  puts MyNamespace::CONST		# 使用::访问模块中的常量，输出 CONST
  MyNamespace.myMethod		# 使用.访问模块中的方法，输出 myMethod called
  foo = MyNamespace::ClassName.new# 使用::访问模块中的类
  foo.myInstanceMethod		# 输出 instanceMethod in class called
  ```

### 结构体标准类

- `Struct`类可以快速生成结构体（不含其它方法的类），将Symbol作为`Struct.new`的参数，将自动生成相应的实例变量，getter与setter，以及使用这些相应数量参数进行初始化的`initialize`等方法。

  ``` ruby
  Point = Struct.new :x,:y	# 使用Struct.new生成类Point，注意大写
  foo = Point.new 1,2		# 使用自动生成的类Point创建对象
  puts foo			# 输出 #<struct Point x=1, y=2>
  foo.x = 3			# 调用自动生成的@x的setter
  puts foo.x			# 调用自动生成的@x的getter，输出 3
  ```

- `OpenStruct`类不需要提前定义结构体包含哪些变量，其直接生成 可以直接使用任意getter/setter 的对象。使用`OpenStruct`类需要添加`require "ostruct"`。

  ``` ruby
  require "ostruct"		# 要加这行

  foo1 = OpenStruct.new		# 无参直接创建对象
  foo1.myVal = 1			# 直接使用@myVal的setter
  puts foo1.myVal			# 直接使用@myVal的getter,输出1

  # 还能用类似Hash的语法作new的参数来初始化生成的对象，注意没有花括号
  foo2 = OpenStruct.new key1:"value1", key2:"value2"
  puts foo2.key1			# 获取初始化的key1的值，输出value1
  foo2.key3 = "value3"		# 仍然可以自动生成getter/setter
  puts foo2.key3			# 输出 value3
  ```



### 标准类Math与Time

- `Math`包含数学相关的方法与常量

  - 方法
    `acos` , `acosh` , `asin` , `asinh` , `atan` , `atan2` , `atanh` , `cbrt` , `cos` , `cosh` , `erf` , `erfc` , `exp` , `frexp` , `gamma` , `hypot` , `ldexp` , `lgamma` , `log` , `log10` , `log2` , `rsqrt` , `sin` , `sinh` , `sqrt` , `tan` , `tanh`

  - 常量

    `E` , `PI`

- `Time`包含时间相关的方法与常量

