# Chapter 6 枚举和注解
## 第34条 用enum代替int常量
在编程语言中还没有引入枚举类型之前, 表示枚举类型的常用模式是声明一组具名的int常量. 
这种方法称作**int枚举模式**. 存在诸多不足, 在类型安全性和使用方便性方面没有任何帮助.

采用int枚举模式的程序是十分脆弱的, 因为int枚举是编译时常量, 被编译到使用它们的客户端中. 
如果与枚举常量关联的int发生了变化, 客户端就必须重新编译. 如果不重新编译, 程序还是可以运行, 但是行为是不确定的.

而且, 要打印int枚举, 所见到的只是一个数字. 要遍历一个组中所有的int枚举常量, 或者获得int枚举组的大小, 这些都没有很可靠的方法.

你还可能碰到这种模式的变体: 使用String常量, 被称作**String枚举模式**.
虽然提供了可打印的字符串, 但是会有性能问题, 因为它依赖于字符串的比较操作. 
更糟糕的是, 它会导致初级用户把字符串常量硬编码到客户端代码中, 而不是使用适当的field. 
如果这样的硬编码字符串常量中包含书写错误, 编译时不会检测到, 在运行时会报错.

Java1.5开始提供了枚举类型.
枚举类型: 实例受控, 是单例的泛型化, 本质上是单元素的枚举.

枚举类型提供的优点:
* 编译时的类型安全.
* 多个枚举类型可包含同名常量.
* 增加或重新排列枚举类型中的常量, 无需重新编译它的客户端代码.
* `toString()`方法将枚举转化成可打印的字符串.
* 允许添加任意的方法和域, 并实现任意的接口. 
* 提供了`Object`方法的实现, 实现了`Comparable`和`Serializable`.
* 静态的`values()`方法可以按照声明顺序返回它的值数组.

为了将数据与枚举常量关联, 要声明实例域, 并编写一个带有数据并将数据保存在域中的构造器. 
枚举天生不可变, 因此所有的域都是final的.

有时候需要将不同的行为与每个常量关联起来, 可以在枚举中定义抽象方法, 这样添加新的常量的时候就必须提供这个方法.

如果多个枚举常量同时共享相同的行为, 则考虑策略枚举.

枚举的性能与int常量相比是相当的, 有个微小的性能缺点, 即装载和初始化枚举时会有空间和时间的成本, 但是实践上通常是可忽略的.

## 第35条 用实例域代替序数
所有的枚举都有一个`ordinal()`方法, 返回每个枚举常量在类型中的数字位置.

永远不要根据枚举的序数导出与它关联的值, 而是要将它保存在一个实例域中.

## 第36条 用EnumSet代替位域
如果一个枚举类型的元素主要用在集合中, 一般就用int枚举模式, 将2的不同倍数赋予每个常量.

这种表示法让你用OR运算将几个常量合并到一个集合中, 称作位域(bit field).

位域表示法也允许利用位操作有效地执行像联(union)合和交集(intersection)这样的集合操作.

但位域有着int枚举常量的所有缺点. (无法打印, 无法遍历.)

java.util提供了`EnumSet`类来有效地表示从单个枚举类型中提取的多个值的多个集合.

## 第37条 用EnumMap代替序数索引
有时候, 你可能会见到用`ordinal`方法来索引数组的代码.

利用`EnumMap`可以更好地解决问题. (一维和多维的例子.)

## 第38条 用接口模拟可扩展的枚举
Java语言上是不支持枚举继承的, 这并不意外, 因为枚举的扩展最后证明都不是什么好点子. 
如果枚举可以继承, 那么怎么列举基本类型的所有元素及其扩展? 最终, 扩展会导致设计和实现的许多方面变得复杂起来.

但是有时候会有这种需求, 例子: 定义操作的枚举类型, 允许用户扩展自己的操作.

解决方法: 基本操作的枚举实现接口, 用户可以定义新的操作类型枚举, 只要实现这个接口就可以.

虽然无法编写可扩展的枚举类型, 却可以通过编写接口以及实现该接口的基础枚举类型, 对它进行模拟. 这样允许客户端编写自己的枚举来实现接口. 
如果API是根据接口编写的, 那么在可以使用基础枚举类型的任何地方, 也都可以使用这些枚举.

## 第39条 注解优先于命名模式
Java 1.5之前, 一般使用命名模式(naming pattern)表明有些程序元素需要通过某种工具或者框架进行特殊处理. 
比如JUnit3要求测试方法名以test开头. 这样做有很多缺点, 注解很好地解决了这些问题.

代码例子:
* `@Test`注解.
* 有参数的`@ExceptionTest`注解.
* Java 8的`@Repeatable`注解.

## 第40条 坚持使用Override注解
举例: 覆写了`equals()`方法(还有`hashCode()`), 但是却没有得到期待的结果, Set中添加了好多重复的实例.

为什么呢? 因为`equals()`方法的参数写成了具体类型, 应该写`Object`. 没有加`@Override`注解的时候, 编译器不会报错, 而是把它当做一次方法重载.

加上`@Override`注解, 编译器会提示, 可以及时发现错误.

应该在你想要覆盖超类声明的每个方法声明中使用`@Override`注解.

如果不写IDE会有警告.

如果是实现抽象方法, 不写`@Override`注解IDE不会警告, 但是这样做没什么坏处.

## 第41条 用标记接口定义类型
标记接口(marker interface)是没有包含方法声明的接口, 而只是指明一个类实现了具有某种属性的接口. 例如`Serializable`接口.

标记接口和标记注解各有用处.
