# observeOn & subscribeOn及线程切换


## 简介

---

subscribeOn()和observeOn()都是用来切换线程用的:

- subscribeOn()主要改变的是订阅的线程，即call()执行的线程, 也会影响到doOnSubscribe(),但不会影响到onStart().
- observeOn()主要改变的是发送的线程，即onNext(),onError(), onCompleted()执行的线程。



## 几个接口的调用顺序

---

RxJava从创建Observable到中间的各种处理(比如map, filter)到subscriberOn,ObservableOn,到最后的subscribe, 除了最后的一个显式的subscriber外,中间会创建很多的subscriber.

整个过程中除了一开始显式创建的Observable外, 中间也隐式创建了不少Observable, 每个Observable都有一个OnSubscribe.


### 各个Subscriber.onXX()调用顺序

因为数据都是从最初获取的地方(显式创建的Observable的OnSubscribe.call()里)往外传的, 所以对于每个subscriber的函数(onNext, onError, onCompleted)调用,都是从最前面(最老的)开始的, 最后到了subscribe时显示指定的subscriber.


### 各个OnSubscribe.call()调用顺序

OnSubscribe.call()是在subscribe()函数被调用时调用的, 所以这个调用从最后一个显式订阅subscribe动作开始, 所以Observable的OnSubscribe.call()的调用是最最后一个(最新的)Observable开始的.


总结:

** 总之就是onNext, onError, onCompleted是从上往下(或者是从老往新)方向调用的, 而OnSubscribe.call()是从下往上或者从新往旧调用的. **

可以看下下面的图, Observable4表示最后的Observable, Observable1表示第一个Observable.

![调用顺序图](https://www.evernote.com/shard/s164/sh/2414963d-b239-44a5-940f-4f24d7c7ee59/600d42d911a7b862/res/bee5f1d8-ec9d-4a2e-8373-05b81e7fa64d/observable.png?resizeSmall&width=832)



## 关于subscribeOn

---

### 源码分析

从subscribeOn的源码看, 它调用"Observable\<T> create(OnSubscribe<T> f)"重新创建了一个Observable, 代码如下:

```
public final Observable<T> subscribeOn(Scheduler scheduler) {
    if (this instanceof ScalarSynchronousObservable) {
        return ((ScalarSynchronousObservable<T>)this).scalarScheduleOn(scheduler);
    }
    return create(new OperatorSubscribeOn<T>(this, scheduler));
}
```

OperatorSubscribeOn就是一个OnSubscribe<T>, 它在指定的scheduler里订阅Observer.

从下面的代码看, 在OperatorSubscribeOn.call()里,根据scheduler创建了一个worker, 然后调用worker.scheduler()执行Action0.call();

Action0.call()里重新生成一个新的Subscribe s,这个新的subscriber的onNext, onError, onCompleted调用原来的subscriber相应的函数, 然后执行source.unsafeSubscribe(s).
source是原来的observable,source.unsafeSubscribe(s)是用原来的observalbe订阅新创建的subscriber.

这里不直接调用source.unsafeSubscribe(subscriber)执行订阅操作而要重新创建一个Subscriber的原因估计是为了每次执行onXXX之后的inner.unsubscribe().

相关代码如下:

```
public final class OperatorSubscribeOn<T> implements OnSubscribe<T> {
    ...
    public OperatorSubscribeOn(Observable<T> source, Scheduler scheduler) {
        ...
    }
    @Override
    public void call(final Subscriber<? super T> subscriber) {
        final Worker inner = scheduler.createWorker();
        subscriber.add(inner);
    
        inner.schedule(new Action0() {
            @Override
            public void call() {
                final Thread t = Thread.currentThread();
    
                Subscriber<T> s = new Subscriber<T>(subscriber) {
                    @Override
                    public void onNext(T t) {
                        subscriber.onNext(t);
                    }
    
                    @Override
                    public void onError(Throwable e) {
                        try {
                            subscriber.onError(e);
                        } finally {
                            inner.unsubscribe();
                        }
                    }
    
                    @Override
                    public void onCompleted() {
                        try {
                            subscriber.onCompleted();
                        } finally {
                            inner.unsubscribe();
                        }
                    }
                    ...

                    source.unsafeSubscribe(s);
        });

```

总结一下:

OperatorSubscribeOn就是一个OnSubscribe<T>, 它在指定的scheduler里订阅一个新的Observer(其实目的就是为了在指定scheduler里执行OnSubscriber.call()). subscribeOn使用新的OnSubscribe即OperatorSubscribeOn重新创建了一个Observable.
这样做实现了原来的Onsubscriber.call()方法在指定的scheduler里执行.

至于OperatorSubscribeOn的实现,也比较简单,既然是一个OnSubscribe<T>, 就需要实现call()方法, 于是它在call()方法里,调用worker.schedule(Action0)执行action0.call(). 在action0.call里,进行原Observable的订阅操作, 最终达到了在指定的scheduler里执行原来的OnSubscribe.call()方法.


subscribeOn不单改变了SubscribeOn.call()方法的执行线程,同时也改变了doOnSubscribe()的执行线程. 那么如果有多个doOnsubsriber()同时有多个subscribeOn调用, 结果会怎样? 见下面:


### 例子

1. 单个subscribeOn

    如下代码:

    ```
    private void testSingle(){
        Observable<String> observable = createObservable();
        Subscriber<String> subscriber = createSubscriber();
        observable.doOnSubscribe(()->{
            log("doOnSubscriber1");
        })
        .subscribeOn(getNameScheduler("thread1"))
        .subscribe(subscriber);
    }

    private Subscriber<String> createSubscriber(){
        return new Subscriber<String>() {
            
            @Override
            public void onNext(String arg0) {
                log("onNext");
            }
            
            @Override
            public void onError(Throwable arg0) {
                log("onError");
            }
            
            @Override
            public void onCompleted() {
                log("onCompleted");
            }
        };
    }

    private Observable<String> createObservable(){
        return Observable.create(subscriber->{
            log("execute OnSubscribe.call()");
            subscriber.onNext("hello");
            subscriber.onCompleted();
        });

    ```

    输出结果如下:

    ```
    [thread1]:doOnSubscriber1
    [thread1]:execute OnSubscribe.call()
    [thread1]:onNext
    [thread1]:onCompleted
    ```

    即Onsubscribe.call里执行线程和doOnSubscribe的执行线程都切换到了subscribeOn()指定的线程.


## 多个subscribeOn

```
private void testMultiSubscribeOn(){
	Utils.printlnHead("testMultiSubscribeOn");
	Observable<String> observable = createObservable();
	Subscriber<String> subscriber = createSubscriber();
	observable.doOnSubscribe(()->{
		log("doOnSubscriber1");
	})
	.subscribeOn(getNameScheduler("thread1"))
	.doOnSubscribe(()->{
		log("doOnSubscriber2");
	})
	.subscribeOn(getNameScheduler("thread2"))
	.subscribe(subscriber);
}
```

输出结果如下:

    [thread2]:doOnSubscriber2
    [thread1]:doOnSubscriber1
    [thread1]:execute OnSubscribe.call()
    [thread1]:onNext
    [thread1]:onCompleted

从上面的结果有两个发现:

1. 后面的doOnSubscribe()是先执行的. 
1. subscribeOn影响的是它前面的subscribeOn.call()和doOnSubscribe(), 直到碰到另外一个subscribeOn结束.  
    因为subscriberOn是对它前面传进来前的observable以及subscriberOn的封装, 所以对于第二点是可以理解的.


下面图表示两个subscribeOn时候的线程切换情况, 红色的为后面的subscribeOn的线程范围, 绿色的为前面的subscribeOn的线程范围.可以理解为在后面的红色的线程里又开了一个绿色线程做OnSubscribe.call()和第一个doOnSubscribe().

![subscribeOn](https://www.evernote.com/shard/s164/sh/2414963d-b239-44a5-940f-4f24d7c7ee59/600d42d911a7b862/res/c084f1a8-ee96-4020-9210-dca244610011.png?resizeSmall&width=832)



## 关于observeOn

---

### 源码分析

observeOn的部分代码如下:

```
public final Observable<T> observeOn(Scheduler scheduler, boolean delayError, int bufferSize) {
    if (this instanceof ScalarSynchronousObservable) {
        return ((ScalarSynchronousObservable<T>)this).scalarScheduleOn(scheduler);
    }
    return lift(new OperatorObserveOn<T>(scheduler, delayError, bufferSize));
}
```

#### 关于lift

>Lifts a function to the current Observable and returns a new Observable that when subscribed to will pass the values of the current Observable through the Operator function.

lift的作用是将一种类型的Observable转为另一种类型的Observable, 两种Observable主要区别在对应的subscriber的不同, 这个是靠lift的第一个参数Operator\<R, T>转化的, 它的定义如下:

```
public interface Operator<R, T> extends Func1<Subscriber<? super R>, Subscriber<? super T>> {
    // cover for generics insanity
}
```

lift的部分源码如下:

```
public final <R> Observable<R> lift(final Operator<? extends R, ? super T> operator) {
    return new Observable<R>(new OnSubscribe<R>() {
        @Override
        public void call(Subscriber<? super R> o) {
            try {
                Subscriber<? super T> st = hook.onLift(operator).call(o);
                try {
                    // new Subscriber created and being subscribed with so 'onStart' it
                    st.onStart();
                    onSubscribe.call(st);
                } catch (Throwable e) {
                    // localized capture of errors rather than it skipping all operators 
                    // and ending up in the try/catch of the subscribe method which then
                    // prevents onErrorResumeNext and other similar approaches to error handling
                    Exceptions.throwIfFatal(e);
                    st.onError(e);
                }
            } catch (Throwable e) {
                Exceptions.throwIfFatal(e);
                // if the lift function failed all we can do is pass the error to the final Subscriber
                // as we don't have the operator available to us
                o.onError(e);
            }
        }
    });
}

```

在lift里, 创建新的Observable, 在新的Observable的OnSubscribe.call()里面, 通过Operator, 将新的subscriber转化为老的subscriber,然后将老的subscriber作为参数调用原来老的onSubscribe.call(xx).

对于lift, 这里有两个Subscriber, 一个是lift新创建的Observable对应的Subscriber, 另一个就是lift本身所属于的Observable对应的Subscriber. lift的第一个参数operator的call函数的作用都是如何将新Observable对应的Subscriber转为与原来Observable相匹配的subscriber.

总结一下:  
lift将原来的observable转为另一种observable, 通过lift的Operator参数将新的observable的subscriber转化出于原来Observable匹配的subscriber,最终是在新的observable的OnSubscribe.call里面调用的是老的onSubscribe.call(), 参数也是老的subscriber.
** lift这样做的目的是通过构建新的Observable转变subscriber类型,将后续的subscriber转为和之前匹配的subscriber. 这就是lift第一个参数operator的作用**, 这块如果不清楚, 也可以看下map()的实现.



#### OperatorObserveOn

>Delivers events on the specified {@code Scheduler} asynchronously via an unbounded buffer.


** OperatorObserveOn是一个Operator, 和其他Operator一样, 它提供一个Subscriber的转化方式. 和别的Operator不同的是, 它转化后返回的Subscriber包含了原来的subscriber, 并且对应的onXX并不是直接调用原来Subscriber相应的onXXX(),而是在Scheduler对应的线程/线程组里执行相应的onXXX(). **

相应代码如下:

```
public final class OperatorObserveOn<T> implements Operator<T, T> {
    ...
    public OperatorObserveOn(Scheduler scheduler, boolean delayError) {
    this(scheduler, delayError, RxRingBuffer.SIZE);
    }
    ....
    @Override
    public Subscriber<? super T> call(Subscriber<? super T> child) {
        if (scheduler instanceof ImmediateScheduler) {
            // avoid overhead, execute directly
            return child;
        } else if (scheduler instanceof TrampolineScheduler) {
            // avoid overhead, execute directly
            return child;
        } else {
            ObserveOnSubscriber<T> parent = new ObserveOnSubscriber<T>(scheduler, child, delayError, bufferSize);
            parent.init();
            return parent;
        }
    }
```

ObserveOnSubscriber是一个Subscriber, 从下面的代码我们可以看到, 当该subscriber的onXXX函数调用时, 如果有对应的值(比如onNext())会被对应的值放入队列,然后起一个scheduler(onNext, onError, onCompleted都一样),在多线程中, 最后call()函数会被调用.

在call()函数里,执行localChild的onError,onComplete,或者从队列里取出值, 然后执行localChild的onNext.

这里的localChild就是传进来的child, 即OperatorObserveOn里需要被转化的subscriber,其实就是新的Observable的subscriber.

** 所以我们可以理解为ObserveOnSubscriber是一个为了和之前的老的subscriber互匹配的subscriber, 当有数据或者事件到时(onNext, onError, onComplete), 它不是直接往外传的, 而是通过scheduler, 在scheduler里往外传的, 这就意味着在它后面产生的Subscriber的onXXXX都是在这个指定的Scheduler的线程/线程组下执行的. **

ObserveOnSubscriber相应代码如下:

```
private static final class ObserveOnSubscriber<T> extends Subscriber<T> implements Action0 {
    ...
    final Scheduler.Worker recursiveScheduler;
    final Subscriber<? super T> child;
    ...
    final Queue<Object> queue;
    ...
    public ObserveOnSubscriber(Scheduler scheduler, Subscriber<? super T> child, boolean delayError, int bufferSize) {
        this.child = child;
        ...
    }

    @Override
    public void onNext(final T t) {
        ...
        if (!queue.offer(on.next(t))) {
            onError(new MissingBackpressureException());
            return;
        }
        schedule();
    }

    @Override
    public void onCompleted() {
        ...
        schedule();
    }
    ...
    protected void schedule() {
        if (counter.getAndIncrement() == 0) {
            recursiveScheduler.schedule(this);
        }
    }

    // only execute this from schedule()
    @Override
    public void call() {
        long missed = 1L;
        long currentEmission = emitted;

        // these are accessed in a tight loop around atomics so
        // loading them into local variables avoids the mandatory re-reading
        // of the constant fields
        final Queue<Object> q = this.queue;
        final Subscriber<? super T> localChild = this.child;
        final NotificationLite<T> localOn = this.on;
  
        // requested and counter are not included to avoid JIT issues with register spilling
        // and their access is is amortized because they are part of the outer loop which runs
        // less frequently (usually after each bufferSize elements)
  
        for (;;) {
            long requestAmount = requested.get();
  
            while (requestAmount != currentEmission) {
                boolean done = finished;
                Object v = q.poll();
                boolean empty = v == null;
  
                if (checkTerminated(done, empty, localChild, q)) {
                    return;
                }
  
                if (empty) {
                    break;
                }
  
                localChild.onNext(localOn.getValue(v));
                ...

```


### 例子

#### 单个observeOn

```
Observable<String> observable = createObservable();
Subscriber<String> subscriber = createSubscriber();
observable.doOnSubscribe(()->{
	log("doOnSubscriber1");
})
.map(str-> {
	log("map1");
	return str+",map1";
})
.map(str->{
	log("map2");
	return str+",map2";
})
.observeOn(getNameScheduler("thread1"))
.map(str->{
	log("map3");
	return str+",map3";
})
.subscribe(subscriber);
```

输出:

```
[main]:doOnSubscriber1
[main]:execute OnSubscribe.call()
[main]:map1
[main]:map2
[thread1]:map3
[thread1]:onNext:hello,map1,map2,map3
[thread1]:onCompleted
```

从上面也验证了单个ObserveOn影响到了后面的操作以及subscriber.onXX().


#### 多个observeOn

```
Observable<String> observable = createObservable();

Subscriber<String> subscriber = createSubscriber();

observable.doOnSubscribe(()->{

	log("doOnSubscriber1");

})

.map(str-> {

	log("map1");

	return str+",map1";

})

.map(str->{

	log("map2");

	return str+",map2";

})

.observeOn(getNameScheduler("thread1"))

.map(str->{

	log("map3");

	return str+",map3";

})

.observeOn(getNameScheduler("thread2"))

.subscribe(subscriber);
```

输出:

```
[main]:doOnSubscriber1
[main]:execute OnSubscribe.call()
[main]:map1
[main]:map2
[thread1]:map3
[thread2]:onNext:hello,map1,map2,map3
[thread2]:onCompleted
```

上面输出也说明了observeOn影响它后面的操作,知道出现另外一个observeOn.


### observeOn总结

** observeOn通过lift(new OperatorObserveOn<T>(scheduler, delayError, bufferSize)), 返回一个新的Observable, 对于该Observable的subscriber来将, 上面Observable触发的onXXX(),并不是直接调用目前这个Observable的subscriber.onXXX(), 而是在指定的scheduler里触发onXXX().**

具体实现是因为OperatorObserveOn返回的Subscriber是ObserveOnSubscriber, 该subscriber相应的onXX()被调用后, 是scheduler相应的线程/线程组,在线程/线程组里再调用被转化Subscriber.onXX().

** 所以说, observeOn指定的scheduler影响的是它后面的操作, 直到一个新的observeOn. **

下面是反映线程切换的一张图:

![observeOn线程切换图](https://www.evernote.com/shard/s164/sh/2414963d-b239-44a5-940f-4f24d7c7ee59/600d42d911a7b862/res/df72aa90-95b6-4b63-9055-5085bf645cab.png?resizeSmall&width=832)



## 总结

---

subscribeOn()和observeOn()都是用来切换线程用的.

1. subscribeOn()

    - 主要改变的是订阅的线程，即call()执行的线程, 也会影响到doOnSubscribe(),但不会影响到onStart().
    - subscribeOn影响的是它前面的subscribeOn.call()和doOnSubscribe(), 直到碰到另外一个subscribeOn结束.  

1. observeOn()

    - 主要改变的是发送的线程，即onNext(),onError(), onCompleted()执行的线程。
    - observeOn指定的scheduler影响的是它后面的操作, 直到一个新的observeOn.



上面的理解感觉是subscribeOn影响subscribeOn.call()和doOnSubscribe(),observeOn()影响onNext(), onError(), onCompleted(), 但是要注意, 想.map(),.filter()等其实它的处理函数都是在中间生成的Subscriber中调用的, 所以如果observeOn()在这些函数之前调用,因为它影响的是onNext,onError,onCompleted, 所以也会影响map,filter它们的线程.
** 所以我们一般都是将subscribeOn()在所有的操作后面调用,observeO()挨着subscribe调用,这样就可以达到中间操作都是按subscribeOn指定的线程模型来, 而最后的Subscriber的onNext,onCompleted, onError线程则由observeOn决定. **

## Reference

---

1. [RxJava线程变换之observeOn与subscribeOn](http://www.jianshu.com/p/59c3d6bb6a6b)
