# FuncX & ActionX接口

## Function

---

它是一个空接口, 所有的Action接口和Func接口都从Function派生:
public interface Function {
}


## FuncX

---

### Func0

从Function和Callable派生, 没有输入参数,一个返回值,一个call方法:

```
    public interface Func0<R> extends Function, Callable<R> {
        @Override
        R call();
    }
```


### Func1

一个输入参数一个返回值:

```
    public interface Func1<T, R> extends Function {
        R call(T t);
    }
```


### Func2...Func9

对应个数的输入一个返回值.

Func2:

```
    public interface Func2<T1, T2, R> extends Function {
        R call(T1 t1, T2 t2);
    }
```

Func9:

```
    public interface Func9<T1, T2, T3, T4, T5, T6, T7, T8, T9, R> extends Function {
        R call(T1 t1, T2 t2, T3 t3, T4 t4, T5 t5, T6 t6, T7 t7, T8 t8, T9 t9);
    }
```

### FuncN

一个返回值,n个参数,由call函数中的参数决定:

```
    public interface FuncN<R> extends Function {
        R call(Object... args);
    }
```

### 总结:

*** 上面的所有接口都是从Function接口派生,包含一个call()方法返回一个R,输入参数由名称决定. ***



## ActionX

---

### Action

从Function派生,无方法,所有其他ActionX接口都从它派生:

```
public interface Action extends Function {
}
```


### Action0

一个无返回值的call方法,无输入参数:

```
    public interface Action0 extends Action {
        void call();
    }
```


### Action1

无返回值,一个输入参数:

```
    public interface Action1<T> extends Action {
        void call(T t);
    }
```


### Action2...Action9

对应个数的输入,无返回值.

Action2:

```
    public interface Action2<T1, T2> extends Action {
        void call(T1 t1, T2 t2);
    }
```

Action9:

```
    public interface Action9<T1, T2, T3, T4, T5, T6, T7, T8, T9> extends Action {
        void call(T1 t1, T2 t2, T3 t3, T4 t4, T5 t5, T6 t6, T7 t7, T8 t8, T9 t9);
    }
```


### ActionN

n个参数,由call函数中的参数决定:

```
    public interface ActionN extends Action {
        void call(Object... args);
    }
```


### 总结:

*** 上面的所有接口都是从Action接口派生,包含一个call()方法,无返回值,输入参数由名称决定. ***



## Functions

---

这个类不可以创建对象,全都是静态方法.

```
    public final class Functions {
        private Functions() {
            throw new IllegalStateException("No instances!");
        }
        ...
```

这个类提供两类静态方法,分别将FuncX转成FuncN或者将ActionX转出ActionN:

### 1. fromFunc
将FuncX转化成FuncN.

比如下面这个是将Func0转成FuncN的实现:

```
    public static <R> FuncN<R> fromFunc(final Func0<? extends R> f) {
            return new FuncN<R>() {

                @Override
                public R call(Object... args) {
                    if (args.length != 0) {
                        throw new RuntimeException("Func0 expecting 0 arguments.");
                    }
                    return f.call();
                }

            };
        }
```

而下面是将Func9转成FuncN的实现:

```
    public static <T0, T1, T2, T3, T4, T5, T6, T7, T8, R> FuncN<R> fromFunc(final Func9<? super T0, ? super T1, ? super T2, ? super T3, ? super T4, ? super T5, ? super T6, ? super T7, ? super T8, ? extends R> f) {
            return new FuncN<R>() {

                @SuppressWarnings("unchecked")
                @Override
                public R call(Object... args) {
                    if (args.length != 9) {
                        throw new RuntimeException("Func9 expecting 9 arguments.");
                    }
                    return f.call((T0) args[0], (T1) args[1], (T2) args[2], (T3) args[3], (T4) args[4], (T5) args[5], (T6) args[6], (T7) args[7], (T8) args[8]);
                }

            };
        }
```

### 2. fromAction  
将Action0,Action1,Action2,Action3转成ActionN.

比如下面这个是将Action0转成ActionN的实现:

```
    public static FuncN<Void> fromAction(final Action0 f) {
            return new FuncN<Void>() {

                @Override
                public Void call(Object... args) {
                    if (args.length != 0) {
                        throw new RuntimeException("Action0 expecting 0 arguments.");
                    }
                    f.call();
                    return null;
                }

            };
        }
```

比如下面这个是将Action3转成ActionN的实现:

```
    public static <T0, T1, T2> FuncN<Void> fromAction(final Action3<? super T0, ? super T1, ? super T2> f) {
            return new FuncN<Void>() {

                @SuppressWarnings("unchecked")
                @Override
                public Void call(Object... args) {
                    if (args.length != 3) {
                        throw new RuntimeException("Action3 expecting 3 arguments.");
                    }
                    f.call((T0) args[0], (T1) args[1], (T2) args[2]);
                    return null;
                }

            };
        }
```

没提供Action3后面的转化了.


## 总结

---

1. Function接口是个空接口
1. Action接口是个空接口,Action从Function派生.
1. FuncX是从Function接口派生.
1. ActionX是从Action接口派生.
1. FuncX和ActionX都有一个call的方法.
1. 不同的是FuncX有返回值,ActionX没有返回值.

