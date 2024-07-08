---
layout: post
title: "Ruby学习笔记（三） 面向对象编程"
categories:
  - Ruby
tags:
  - Ruby
last_modified_at: 2017-01-31 23:57:56 +08:00
---

* content
{:toc}
这是我在学习 [SoloLearn](https://www.sololearn.com/) 的 [Ruby Tutorial](https://www.sololearn.com/Course/Ruby/) 时的笔记的第三部分，包括Ruby编程中的面向对象编程知识。具体包括类与对象、实例变量、实例方法与Accessors、类方法与类变量、继承、运算符重载、访问控制。



在本文中我省略了该课程中的一些编程的基础概念，并且将Ruby的语法与其它语言的进行了一些比较，适合已经有一些其它编程语言的基础的朋友阅读。文中的加粗内容表示跟我以前接触过的其它语言（C/C++/Java/Javascript）看起来相似但不一致的地方。由于我水平有限，错误在所难免，如发现错误请在留言内指教，谢谢！

本文在本人新博客的链接：[http://www.myblog.link/2017/01/20/Note-of-Ruby-3/](http://www.myblog.link/2017/01/20/Note-of-Ruby-3/)


## 面向对象编程

### 类与对象

- Ruby 是纯面向对象的语言，所有东西都是对象。

- 使用 `class` 关键字创建类，`initialize()` 方法用于初始化类的对象：

  ``` ruby
  class 类名
    def initialize(可选的参数)
      # 创建该类的对象时自动调用的函数，用于初始化，类似构造函数
    end
  end
  ```

- 使用**类的`new`方法**创建类的对象，`new` 方法的参数会被传递给`initialize()`方法。

  - `类名.new(给initialize方法的参数)` 


### 实例变量、实例方法与Accessors

- `@`开头的变量为实例变量(Instance Variables)。类的每个实例，即每个对象，有各自的一份实例变量。实例变量可以在该对象中被调用。

- 实例方法(Instance Methods)需要用`对象名.方法名`调用，必须用它们来从外部访问该对象的实例变量。如用来读写实例变量的accessors，包括：

  - getter：用来获取该对象的某个实例变量的值。方法名最好跟读取的实例变量相关，如读取`@foo变量`，则方法名为`get_foo` ，或者直接使用`foo`。
  - setter：用来改变该对象的某个实例变量的值。方法名规则类似getter，但需要以`=`结尾，如用`变量名=`，调用时可以使用`对象名.变量名 = 值` 这样更自然的语言来调用setter方法修改该实例变量的值。

  ``` ruby
  class Test
    @foo 			# 实例变量@foo，只能在该对象中被访问	
    def initialize foo	# 初始化方法，传入自定义参数初始化实例变量
      @foo = foo
    end
    def foo	 	# getter,读实例变量@foo的值
      @foo
    end
    def foo= foo	  	# setter，写实例变量@foo的值
      @foo = foo
    end
  end

  test = Test.new 1 	# 获取Test的一个对象，并且用1传给初始化方法对@foo进行初始化
  puts test.foo		# 调用 foo 方法获取@foo的值，而不是直接访问的@foo，这里输出1
  test.foo = 2		# 调用 foo= 方法设置@foo的值，2作为该函数的参数，而不是直接访问@foo进行赋值
  puts test.foo		# 输出2
  ```

- 像上面那样给每个实例变量写getter和setter很不方便，Ruby提供了*几个方法 以symbol为参数* 来自动生成getter/setter：

  - `attr_accessor` ：生成getter和setter
  - `attr_reader` ： 生成getter
  - `attr_writer`： 生成setter

  以下代码产生了`@a`、`@b`、`@c`、`@d` 4个实例变量，其中`@a`、`@b`、`@c` 生成了getter，`@c`、`@d` 生成了setter：

  ``` ruby
  class Test
    attr_reader :a, :b	# 生成getter；可以接多个symbol作为参数，下同
    attr_accessor :c	# 生成getter和setter
    attr_writer :d	# 生成setter
  end
  ```

- `self.方法名`用在实例方法的代码里，用来表示调用该对象的该**实例方法**。（看起来像`this`，作用不完全一样。）

- 每个类都有`to_s`方法，需要将该类的对象转换成字符串时（如`puts`、`#{变量名}`）会自动调用这个方法，可以自行重写以改变转换的结果，类似于Java的`toString()`。


### 类方法与类变量

- 在类里定义方法时的方法名前加上`self.`来定义类方法(Class Methods)。类方法可以使用类名调用，而不需要具体的对象，类似于C++/Java的`static`的方法。

- 在类里定义变量时使用`@@变量名` 来定义类变量(Class Variables)。类变量被该类的所有对象共享，但不能从外界直接使用变量名访问，类似于C++/Java中的`private`的`static`变量。

- 在类里使用大写字母开头的为类常量(Class Constants)。类常量可在类的外部通过`类名::常量名`来访问。类似于Java的`public static final`变量，但注意**访问用`::`**而不是`.`。

  ``` ruby
  class Foo
    @@cnt = 0		# 类变量，不可以从外面直接访问
    CLASSNAME = "Foo" 	# 类常量，可以从外面直接访问
    def initialize
      @@cnt = @@cnt + 1
    end
    def self.getCnt	# 类方法
      @@cnt
    end
  end

  puts Foo::CLASSNAME	# 输出Foo
  puts Foo.getCnt		# 输出0
  Foo.new
  Foo.new
  puts Foo.getCnt		# 输出2
  ```



### 继承

- 在定义类时使用`class 子类名 < 父类名`来使子类继承父类。只允许继承一个父类。

- 子类可以重写父类的方法。在重写的方法里可以使用`super`**方法** 来调用父类的同名方法。

  ``` ruby
  class Base		# 定义父类Base
    def initialize
      puts "I'm Base"
    end
  end

  class Foo < Base	# 定义子类Foo，继承Base类
    def initialize	# 重写父类的initialize方法
      puts "I'm Foo"
      super		# 使用super方法调用父类的同名方法，这里是initialize
    end
  end

  Foo.new 	# 输出I'm Foo【换行】I'm Base
  ```

### 运算符重载

- 定义方法时使用`def 运算符`来重载运算符。

  ``` ruby
  class Foo
    attr_accessor :val
    def initialize val
      self.val = val	# self.val调用本对象的val方法，也就是@val的setter；第二个val是函数参数
    end
    def + other		# 重载+号，参数other为另一个参见+运算的Foo对象
      Foo.new val+other.val
    end
    def to_s		# 覆盖自动转换成字符串的函数
      @val.to_s		# 注意这里必须返回一个字符串，直接写成 @val 是不行的
    end
  end

  a = Foo.new 1
  b = Foo.new 1
  puts a + b		# 输出2
  ```

### 访问控制

- 方法有三种访问类型：
  - `public` 所有地方均可访问（除了`initialize`方法以外的其它方法的默认值）
  - `private`仅该对象内部可以访问
  - `protected` 该类及其子类的对象可以访问 
- 实例变量与类变量一定是`private`的，类常量一定是`public`的

``` ruby
class Foo
  def method1	# 公开（默认公开）
  end
  private	# 从这行开始直到public之前的所有方法都是私有的
  def method2	# 私有
  end
  def method3	# 还是私有
  end
  CONST = 0	# 类常量永远公开，不受private的影响
  public	# 从该行开始的方法都是公开的，因为后面没有其它的申明
  def method4	# 公开
  end
  @val1 = 1	# 变量永远私有，不受public影响
  @@val2 = 2	# 变量永远私有，不受public影响
end
```