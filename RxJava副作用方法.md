# RxJava副作用方法(side effect methods)

## 介绍

---
RxJava的观察者类有许多方法，可以转换发出的字节流为任何你需要的数据类型。这些方法是RxJava非常核心的方法，是RxJava具有吸引力的重要缘故。

但是有些方法无论如何都不能改变流本身，这些方法称为副作用(Side Effect)方法。

>Since those methods do not return anything, they cannot be used to change the emitted items and thus do not change the stream of items in any way. Instead these methods are intended to cause side effects like writing something on disk, cleaning up state or anything else that manipulates the state of the system itself instead of the stream of events.

这些方法一般用来在磁盘上写入一些东西(比如日志)、清空状态或者其他任何能操纵系统本身状态而不是事件流本身的事情。

Note:
     
>The side effect methods themselves (doOnNext(), doOnCompleted() and so on) do return an Observable. That’s to keep the interface fluent. But the returned Observable is of the same type and emits the same items as the source Observable.

doOnNext,doOnCompleted()方法会方法Observable,这些类型和调用它们之前的类型相同的.

## 方法及对应的函数接口

---
Method| Functional Interface| Event
:--|:--|:--
doOnSubscribe()| Action0| A subscriber subscribes to the Observable
doOnUnsubscribe()| Action0 |A subscriber unsubscribes from the subscription
doOnNext()| Action1<T>| The next item is emitted
doOnCompleted()| Action0| The Observable will emit no more items
doOnError()| Action1<T>| An error occurred
doOnTerminate()| Action0| Either an error occurred or the Observable will emit no more items,发生在oncomplete,onerror前.
doAfterTerminate()|Action0|Registers an Action0 to be called when this Observable invokes either onCompleted or onError.发生在oncomplete,onerror后.
doOnEach()| Action1<Notification<T>>| Either an item was emitted, the Observable completes or an error occurred. The Notification object contains information about the type of event
doOnRequest()| Action1<Long>| A downstream operator requests to emit more items

## 详解

---
### doOnSubscribe & doOnUnsubscribe

```
public final Observable<T> doOnSubscribe(Action0 subscribe)
public final Observable<T> doOnUnsubscribe(Action0 unsubscribe)
```

Subscription 和 unsubscription 并不是 Observable 发射的事件。而是 该 Observable 被 Observer 订阅和取消订阅的事件。
例子:

```
Subscription s = Observable.just("Louis1","Louis2")
                .doOnSubscribe(()->System.out.println("subscribed"))
                .doOnUnsubscribe(()->System.out.println("unsubscribed"))
                .subscribe(System.out::println);
                s.unsubscribe();
output:
    subscribed
    Louis1
    Louis2
    unsubscribed
```

### doOnCompleted() & doOnError()
    public final Observable<T> doOnCompleted(Action0 onCompleted)
    public final Observable<T> doOnError(Action1<java.lang.Throwable> onError)
分别在成功和失败的时候调用.

例子:

```
Observable.just("Luo1","Luo2")
          .doOnCompleted(()->System.out.println("onComplted"))
          .subscribe(System.out::println);

output:
    Luo1
    Luo2
    onComplted
```

### doOnNext
    public final Observable<T> doOnNext(Action1<? super T> onNext)

Modifies the source Observable so that it invokes an action when it calls onNext.

onNext调用时调动该函数,在Observer.onNext前调用.

例子1:

```
    Observable.just("Luo1","Luo2")
            .doOnNext(s->System.out.println("doOnNext:"+s))
            .subscribe(System.out::println);
output:
    doOnNext:Luo1
    Luo1
    doOnNext:Luo2
    Luo2
```
例子2:

```
Observable.just("Luo1","Luo2")
        .map(s->"map1:"+s)
        .doOnNext(s->System.out.println("doOnNext:"+s))
        .map(s->"map2:"+s)
        .doOnNext(s->System.out.println("doOnNext:"+s))
        .subscribe(System.out::println);
output:
    doOnNext:map1:Luo1
    doOnNext:map2:map1:Luo1
    map2:map1:Luo1
    doOnNext:map1:Luo2
    doOnNext:map2:map1:Luo2
    map2:map1:Luo2
```

上面的情况说明可以有多个doOnNext,每个doOnNext的结果都是它上一次的转化的结果, 所以doOnNext可以打印中间的状态.

### doOnEach
    public final Observable<T> doOnEach(Action1<Notification<? super T>> onNotification)
    public final Observable<T> doOnEach(Observer<? super T> observer)

Modifies the source Observable so that it notifies an Observer for each item and terminal event it emits.

Observable调用onNext后调用,在observer调用onNext前调用.

*** 和doOnNext的区别是doOnEach还可以在onCompleted或者onError时调用.***

例子:

```
    Observable.just("Luo1","Luo2")
        .doOnEach(new Observer<String>(){

            @Override
            public void onCompleted() {
                Utils.println("testDoOnEach:onCompleted");
            }

            @Override
            public void onError(Throwable arg0) {
                Utils.println("testDoOnEach:onError");
            }

            @Override
            public void onNext(String arg0) {
                Utils.println("testDoOnEach:onNext:"+arg0);
            }

        })
        .subscribe(System.out::println);
output:
    testDoOnEach:onNext:Luo1
    Luo1
    testDoOnEach:onNext:Luo2
    Luo2
    testDoOnEach:onCompleted
```

### doOnRequest
    public final Observable<T> doOnRequest(Action1<java.lang.Long> onRequest)

Modifies the source Observable so that it invokes the given action when it receives a request for more items.

当 Subscriber 请求更多的时候的时候， doOnRequest 就会被调用。参数中的值为请求的数量。

例子:

```
    Observable.just("luo1", "luo2")
            .doOnRequest(num>System.out.println("DoOnRequest:"+num.longValue()))
            .subscribe(str->System.out.println("onNext:"+str));
```


### doOnTerminate
     public final Observable<T> doOnTerminate(final Action0 onTerminate);

Modifies the source Observable so that it invokes an action when it calls Observable.onCompleted or onError.

*** 也就是Observable.onComplete或者onError被调用的时候Action0会被调用,在它们前调用***


### doAfterTerminate
    public final Observable<T> doAfterTerminate(Action0 action)

Registers an Action0 to be called when this Observable invokes either onCompleted or onError.

*** 也就是Observable.onComplete或者onError被调用的时候Action0会被调用,在它们后调用***

## Reference

---
1. [RxJava’s Side Effect Methods](http://www.grokkingandroid.com/rxjavas-side-effect-methods)

1. [RxJava的副作用方法](http://www.tuicool.com/articles/RzUvMzb)