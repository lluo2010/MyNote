# Java Optional<T> study note


## Optinal创建

三种方式构建：Optional.of(obj),  Optional.ofNullable(obj) 和明确的 Optional.empty():

创建方法|描述
---|---
Optional.of(obj)|返回指定值的Optional类型， 如果指定值为null, 则抛出NullPointerException。
Optional.ofNullable(obj)|返回指定值的Optional类型， 如果指定值为null, 则返回Optional.empty()
Optional.empty()|返回空的Optional.

**注意， 不要用Optional.empty()判断Optional是否为空，要使用isPresent().**



## 获取值

返回的是原来类型的值，不是Optional<T>.

1. 存在即返回, 无则提供默认值:

    return user.orElse(null);  //而不是 return user.isPresent() ? user.get() : null;
    return user.orElse(UNKNOWN_USER);

1. 存在即返回, 无则由函数来产生:

    return user.orElseGet(() -> fetchAUserFromDatabase()); //而不要 return user.isPresent() ? user: fetchAUserFromDatabase();



## 空/不空时的不同处理

可以如下：

    user.ifPresent(System.out::println);

其实就是：

    user.ifPresent(u->{//todo something});

//而不要下边那样:

    if (user.isPresent()) {
    System.out.println(user.get());
    }



## map

map方法用来对Optional实例的值执行一系列操作,**返回另一个Optional**. 它的参数是一个Function,如参为T,返回参数为U,注意，Function的返回参数为U, 不是Optional<U>, map会根据Function的结果在转化为Optional<U>.

格式如下：

    public <U> Optional<U> map(Function<? super T,? extends U> mapper)

>If a value is present, apply the provided mapping function to it, and if the result is non-null, return an Optional describing the result. Otherwise return an empty Optional.

当Optional不为空，map执行Function,返回Function的结果, 当optional为空，map返回空。

** 所以map返回空其实有两种情况， 一种是本身Optional为空，所以map返回空，另一种情况是Optional不为空，但是Function执行后返回空。**

例子：

    Optional<String> opt = Optional.of("hello");
    Optional<String> upperOpt = opt.map(v -> v.toUpperCase());
    System.out.println(upperOpt.orElse("empty"));



## flatmap

格式如下：

    public <U> Optional<U> flatMap(Function<? super T,Optional<U>> mapper)

>If a value is present, apply the provided Optional-bearing mapping function to it, return that result, otherwise return an empty Optional. This method is similar to map(Function), but the provided mapper is one whose result is already an Optional, and if invoked, flatMap does not wrap it with an additional Optional.


例子：

    todo...



## filter

filter个方法通过传入限定条件对Optional实例的值进行过滤,如果有值并且满足断言条件返回包含该值的Optional，否则返回空Optional。

    public Optional<T> filter(Predicate<? super T> predicate)

>If a value is present, and the value matches the given predicate, return an Optional describing the value, otherwise return an empty Optional.


例子：

    Optional<Integer> optInt = Optional.of(10);
    String strInfo = optInt.filter(v->v.intValue()%2==0).map(v->v+" is even").orElse("it's odd");


## 总结

1. Optional.of, Optional.ofNullable,Optional.empty()均返回一个Optional。
1. get/orElse/orElseGet均返回Optional对应的对象。 get()返回Optional对应的对象，orElse当optional为空的时候指定默认值，orElseGet当对象为空时，通过一个Supplier接口返回值。
1. Optional.map也返回一个Optional,只是这个Optional是根据指定的Function将原来的Optional转为另一个Optional， 前提是本Optional不为空，如果为空，什么也不处理。
1. Optional.isPresent判断Optional是否为空， 另一个isPresent指定consumer接口，当Optional不为空时，通过consume接口处理。




String::new，对应的Lambda：() -> new String()


方法引用有以下几种方式：

方式|格式|例子及对应Lambda|说明
---|---|---|---
引用静态方法|ContainingClass::staticMethodName|String::valueOf，等价的Lambda：(s) -> String.valueOf(s).|和静态方法调用相比，只是把.换为::, 传进来的参数作为静态方法的参数。
引用构造函数|ClassName::new|String::new，相当于()->new String()|构造函数本质上是静态方法，只是方法名字比较特殊。

