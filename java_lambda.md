# Java 8 Lambda及相关 note

## 函数式接口

---

** 如果一个接口定义个唯一一个抽象方法，那么这个接口就成为函数式接口。**

@FunctionalInterface:

这是一个新引入的注解, 可以把它放在一个接口前，表示这个接口是一个函数式接口。这个注解是非必须的，只要接口只包含一个方法的接口，虚拟机会自动判断，不过最好在接口上使用注解 @FunctionalInterface 进行声明。在接口中添加了 @FunctionalInterface 的接口，只允许有一个抽象方法，否则编译器也会报错。



## Lambda 表达式

---

** 函数式接口的重要属性是：我们能够使用 Lambda 实例化它们，Lambda 表达式让你能够将函数作为方法参数，或者将代码作为数据对待。**

lambda表达式的语法由参数列表、箭头符号->和函数体组成。 比如下面:

```
(subscriber)->{
    log("execute OnSubscribe.call()");
    subscriber.onNext("hello");
    subscriber.onCompleted();
});
```

### 关于函数体

函数体既可以是一个表达式，也可以是一个语句块：

1. 表达式

    方法体为表达式，该表达式的值作为返回值返回。大致如下, 不需要写return:

    ```
    (parameters) -> expression
    ```
    表达式函数体适合小型lambda表达式，它消除了return关键字，使得语法更加简洁。

1. 语句块

    方法体为代码块，必须用 {} 来包裹起来，且需要一个 return 返回值，但若函数式接口里面方法返回值是 void，则无需返回值.和一般的语句块类似:
    
    - return语句会把控制权交给匿名方法的调用者. 如果函数体有返回值，那么函数体内部的每一条路径都必须返回值
    - break和continue只能在循环中使用

    格式大致如下:

    ```
    (parameters) -> { statements; return xx; }
    ```



### 目标类型

对于给定的lambda表达式，它的类型是什么？答案是：它的类型是由其上下文推导而来。

这就意味着同样的lambda表达式在不同上下文里可以拥有不同的类型, 比如对于下面的代码:

```
Callable<String> c = () -> "done";
PrivilegedAction<String> a = () -> "done";
```

第一个lambda表达式() -> "done"是Callable的实例，而第二个lambda表达式则是PrivilegedAction的实例。

编译器负责推导lambda表达式的类型。它利用lambda表达式所在上下文所期待的类型进行推导，这个被期待的类型被称为目标类型。

** lambda表达式只能出现在目标类型为函数式接口的上下文中。**

当然，lambda表达式对目标类型也是有要求的。编译器会检查lambda表达式的类型和目标类型的方法签名（method signature）是否一致。当且仅当下面所有条件均满足时，lambda表达式才可以被赋给目标类型T：

1. T是一个函数式接口.
1. lambda表达式的参数和T的方法参数在数量和类型上一一对应的.
1. lambda表达式的返回值和T的方法返回值相兼容（Compatible）.
1. lambda表达式内所抛出的异常和T的方法throws类型相兼容.

由于目标类型（函数式接口）已经“知道”lambda表达式的形式参数（Formal parameter）类型，所以我们没有必要把已知类型再重复一遍。** 也就是说，lambda表达式的参数类型可以从目标类型中得出**. 比如下面:

```
Comparator<String> c = (s1, s2) -> s1.compareToIgnoreCase(s2);
```
在上面的例子里，编译器可以推导出s1和s2的类型是String。

此外，当lambda的参数只有一个而且它的类型可以被推导得知时，**该参数列表外面的括号可以被省略**:

```
FileFilter java = f -> f.getName().endsWith(".java");
button.addActionListener(e -> ui.dazzle(e.getModifiers()));
````


### 词法作用域（Lexical scoping）

todo...XXX



## 函数式通用接口

---

要使用Lambda表达式，需要定义一个函数式接口，这样往往会让程序充斥着过量的仅为 Lambda 表达式服务的函数式接口。为了减少这样过量的函数式接口，Java 8 在 java.util.function 中增加了不少新的函数式通用接口。

1. 基本

    函数接口|作用|部分方法
    -|-|-
    Predicate<T>    |传入一个参数，返回一个bool结果              |方法boolean test(T t) 
    Consumer<T>     |传入一个参数，无返回值，纯消费               |方法void accept(T t) 
    Supplier<T>     |无参数传入，返回一个结果                    |方法T get()
    Function<T,R>   |传入一个参数，返回一个结果                   |方法R apply(T t)
    BiFunction<T,U,R>|两个入参,返回一个结果                      |方法R apply(T t,U u)
    UnaryOperator    |一元操作符， 继承Function,传入参数的类型和返回类型相同.          |方法方法T apply(T t)
    BinaryOperator   |二元操作符， 传入的两个参数的类型和返回类型相同，继承BiFunction   |方法T apply(T t,T u).

1. 更多

    Java8提供了一些针对原始类型（Primitive type）的特化（Specialization）函数式接口:

    - DoubleXXX,LongXXX, IntXXXX, 比如LongSupplier,LongPredicate,都是将第一个类型指定为具体的.
    - ToDoubleBiFunction<T,U>,ToDoubleFunction<T>,,oIntBiFunction<T,U>,ToIntFunction<T>,ToLongBiFunction<T,U>,ToLongFunction<T>是指定返回值的类型.
    - ObjDoubleConsumer<T>,ObjIntConsumer<T>,ObjLongConsumer<T>让Comsumer有两个参数,第一个为T,第二个根据名字指定为double,int, long.

    对于Function,还有下面组合, 其实就是名称显示执行了参数类型,比如<int,int>,<int,double>,<int,long>,<double,int>,<double,double>,<double,long>,<long,int>,<long,double>,<long,long>:

    - IntToDoubleFunction
    - IntToLongFunction
    - IntUnaryOperator
    - LongToDoubleFunction
    - LongToIntFunction
    - LongUnaryOperator
    - DoubleToIntFunction
    - DoubleToLongFunction
    - DoubleUnaryOperator

    另外还有Bi开头的 BiConsumer<T,U>, BiFunction<T,U,R>,BiPredicate<T,U>, 和原来的差别是输入参数多了一个.

1. 总结:

    - Predicate传入一个参数,返回一个boolean.
    - Consumer有入参,无返回值.
    - Supplier无入参有返回值.
    - Function有一个入参,一个返回值,两者的类型可以一样,也可以不一样.
    - BiFunction有两个入参,一个返回值, 类型可以相同也可以不同.
    - UnaryOperator继承自Function, 有一个入参,一个返回值,类型相同.
    - BinaryOperator继承自BiFunction,两个入参一个返回值,类型相同.



## Reference

---

1. [深入理解Java 8 Lambda（语言篇——lambda，方法引用，目标类型和默认方法）](http://zh.lucida.me/blog/java-8-lambdas-insideout-language-features/)
1. [Java 8新特性概述](https://www.ibm.com/developerworks/cn/java/j-lo-jdk8newfeature/)
1. [JAVA8 十大新特性详解](http://www.jb51.net/article/48304.htm)
1. [Java 8全面解析](http://www.infoq.com/cn/news/2013/08/everything-about-java-8/)
1. [Java 8](http://www.baeldung.com/java8)



