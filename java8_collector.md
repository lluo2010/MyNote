---
title: Java8 Collector note
tags: Note
notebook: Java
---

# Java8 Collector note

collect是一个将数据流缩减为一个值的归约操作。这个值可以是集合、映射，或者一个值对象。你可以使用collect达到以下目的：

    - 将数据流缩减为一个单一值：一个流执行后的结果能够被缩减为一个单一的值。单一的值可以是一个Collection，或者像int、double等的数值，再或者是一个用户自定义的值对象。
    - 将一个数据流中的元素进行分组：根据任务类型将流中所有的任务进行分组。这将产生一个Map<TaskType, List<Task>>的结果，其中每个实体包含一个任务类型以及与它相关的任务。你也可以使用除了列表以外的任何其他的集合。如果你不需要与一任务类型相关的所有的任务，你可以选择产生一个Map<TaskType, Task>。这是一个能够根据任务类型对任务进行分类并获取每类任务中第一个任务的例子。
    - 分割一个流中的元素：你可以将一个流分割为两组——比如将任务分割为要做和已经做完的任务。




## 生成一个单一值


### toList-生成list

### toSet-生成Set


### toMap-生成Map






## 分组





## 辅助接口

### Supplier

```
@FunctionalInterface  
public interface Supplier<T>
```
Supplier\<T>接口是一个函数接口，该接口声明了一个get方法，主要用来创建返回一个指定数据类型的对象。

声明：

    @FunctionalInterface 
    public interface Supplier { 
        T get(); 
    }


### BiConsumer



BiConsumer<T, U>接口是一个函数接口，该接口声明了accept方法，并无返回值，该函数接口主要用来声明一些预期操作。

实现：

    @FunctionalInterface
    public interface BiConsumer<T, U> {
        void accept(T t, U u);

        default BiConsumer<T, U> andThen(BiConsumer<? super T, ? super U> after) {
            Objects.requireNonNull(after);

            return (l, r) -> {
                accept(l, r);
                after.accept(l, r);
            };
        }
    }



### BinaryOperator

BinaryOperator接口继承于BiFunction接口，该接口指定了apply方法执行的参数类型及返回值类型均为T:

1. BiFunction是两个输入一个输出，提供方法apply和andThen.
1. BinaryOperator参数都相同。


实现：
```
    @FunctionalInterface
    public interface BinaryOperator<T> extends BiFunction<T,T,T> {

        public static <T> BinaryOperator<T> minBy(Comparator<? super T> comparator) {
            Objects.requireNonNull(comparator);
            return (a, b) -> comparator.compare(a, b) <= 0 ? a : b;
        }

        public static <T> BinaryOperator<T> maxBy(Comparator<? super T> comparator) {
            Objects.requireNonNull(comparator);
            return (a, b) -> comparator.compare(a, b) >= 0 ? a : b;
        }
    }

    @FunctionalInterface
    public interface BiFunction<T, U, R> {


        R apply(T t, U u);

        default <V> BiFunction<T, U, V> andThen(Function<? super R, ? extends V> after) {
            Objects.requireNonNull(after);
            return (T t, U u) -> after.apply(apply(t, u));
        }
    }

```






## Reference

- [Java8新特性之collectors***](http://www.drfish.me/java/2016/09/14/Java8%E6%96%B0%E7%89%B9%E6%80%A7%E4%B9%8Bcollectors/)
- [Java 8系列之Stream的强大工具Collector](http://blog.csdn.net/io_field/article/details/54971608)



