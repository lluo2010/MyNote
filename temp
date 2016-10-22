# RxJava入门之几个容易误解出错的地方

下面的内容主要是整理了初次接触RxJava时容易误解,出错的地方.

## 关于defer的用途

defer操作符与create、just、from等操作符一样，是创建Observable的操作符，不过所有与该操作符相关的数据都是在订阅是才生效的。

这里有个容易不好理解的地方.

先看下面的例子:

```
static class SomeType {
	private String value ="default"; 
	public void setValue(String value) { this.value = value; } 
	public Observable<String> valueObservable() {
		return Observable.just(value); 
	} 
		
	public Observable<String> valueObservableByDefer() {
		return Observable.defer(()->Observable.just(value));
	} 
} 
```

value默认值为"default",在subscribe前设置value值为"some value".

如下代码, 如果我们使用instance.valueObservable()获取observable, 输出值为"default",而不是"Some value":

```
SomeType instance = new SomeType(); 
Observable<String> value = instance.valueObservable(); 
instance.setValue("Some Value"); 
value.subscribe(System.out::println);

```

如下代码, 如果我们使用instance.valueObservableByDefer()获取observable, 输出值为"Some value":

```
SomeType instance = new SomeType(); 
Observable<String> value = instance.valueObservableByDefer();
instance.setValue("Some Value"); 
value.subscribe(System.out::println);
```

valueObservableByDefer和valueObservable的区别是前者使用了defer.

上面的例子能帮我们更好的理解下面这段话:

>The defer Observer allows you to defer or delay emitting items from an Observable until such time as an Observer subscribes to the Observable. This allows an Observer to easily obtain updates or a  refreshed version of the sequence.

可以简单的理解为defer创建的Observable是在subscribe调用时才创建的.


## 操作到底在那个线程执行

平常我们通过subscribeOn指定执行的线程.比如下面, doSomething()就是在指定线程里执行的:

```
Observable.create(s->{
			s.onNext(doSomething());
			s.onCompleted();
		}).subscribeOn(Schedulers.io()).subscribe(System.out::println);
```

但是下面doSomething切是在当前线程而不是io线程执行的, 执行完成后,获取的值再传入just:

```
Observable.just(doSomething())
	      .subscribeOn(Schedulers.io()).subscribe(System.out::println);
```

如果要在指定线程执行,可以采用上面Observable.create的做法, 也可以使用如下defer:

```
Observable.defer(()->Observable.just(doSomething()))
		  .subscribeOn(Schedulers.io()).subscribe(System.out::println);
```

## 和时间相关的几个操作符是在computation Scheduler里执行的
一般情况下,如果不指定执行线程,都在当前线程执行的,但是和时间相关的几个操作符,比如timer,delay,interval,它们都是在computation Scheduler里执行的.

当然,它们也提供了对应的函数指定线程.

比如delay,下面第一个是在computation Scheduler里执行的, 第二个可以指定scheduler:

```
public final Observable<T> delay(long delay, TimeUnit unit);
public final Observable<T> delay(long delay, TimeUnit unit, Scheduler scheduler);
```

>By default this variant of delay operates on the computation Scheduler, but you can choose a different Scheduler by passing it in as an optional third parameter to delay


## Schedulers.immediate和Schedulers.trampoline区别

Schedulers.immediate和Schedulers.trampoline都在当前线程执行,区别是前者马上执行,后者是等队列中的其他任务完成后才执行.

最明显的例子是,当你要执行一个任务,而任务中有要执行另一个任务时, 使用这两种方式就能明显看出区别.

下面的例子比较容易看出区别.

看下面代码任务outerAction中又启一个innerAction任务:

```
    static void testScheduler(Scheduler scheduler){
        Worker worker = scheduler.createWorker();
        Action0 leafAction = () -> System.out.println("----leafAction.");
        Action0 innerAction = () ->
        {
            System.out.println("--innerAction start.");
            worker.schedule(leafAction);
            System.out.println("--innerAction end.");
        };


        Action0 outerAction = () ->
        {
            System.out.println("outer start.");
            worker.schedule(innerAction);
            System.out.println("outer end.");
        };

        worker.schedule(outerAction);
    }

```

使用Schedulers.immediate():

testScheduler(Schedulers.immediate());


输出:

```
outer start.
--innerAction start.
----leafAction.
--innerAction end.
outer end.
```

使用Schedulers.trampoline():

testScheduler(Schedulers.trampoline());

输出:

```
outer start.
outer end.
--innerAction start.
--innerAction end.
----leafAction.

```


## 使用默认值XXXOrDefault
如果我们使用某种操作符获取某个数据,有可能数据根本就不存在, 则时候会引发NoSuchElementException或者IndexOutOfBoundsException等异常, 这时候可以采用相应的OrDefault版本,提供默认值.

比如下面, 我们发射三个数据,然后忽略三个,再去取第一个,其实已经没有第一个了, 这时候会发生NoSuchElementException, onError被调用,打印"java.util.NoSuchElementException: Sequence contains no elements":

```
Observable.just(1,2,3).skip(3).first().subscribe(System.out::println
				,e->System.out.println(e.toString())
				,()->System.out.println("onCompleted"));
```
输出:

```
java.util.NoSuchElementException: Sequence contains no elements
```

如果使用firstOrDefault,输出默认值,且onCompleted会被调用.

```
Observable.just(1,2,3).skip(3).firstOrDefault(-100).subscribe(System.out::println
				,e->System.out.println(e.toString())
				,()->System.out.println("onCompleted"));
```			

输出:

```
-100
onCompleted
```

有OrDefault版本的函数有如下:

elementAtOrDefault, firstOrDefault, lastOrDefault, singleOrDefault


## zip函数的推迟功能
> combine the emissions of multiple Observables together via a specified function and emit single items for each combination based on the results of this function

我们知道, zip将多个Observable相应位置上的数据(用item更贴切)按指定的函数合成一个结果,然后重新形成一个新的Observable.

比如Observable1发射了第一个数据,那么必须等到Observable2发射了第一个数据,才能按指定的函数生成新的Observable的第一个数据.

利用这个特性,我们可以做一些推迟的工作.

比如如下, Observable1是一个执行5毫秒后发送一个数据的Observable,Observable2是执行doSomething()后产生的结果:

```
Observable.zip(Observable.timer(5,TimeUnit.MILLISECONDS) 
				,Observable.just(doSomething()), (x,y)->y)
		  .subscribe(System.out::println));
```
上面的做法其实目的就是讲doSomething()产生的数据推迟5毫秒发射出来,但是要注意, doSomething()这个动作是马上执行的,而不是推迟5毫秒执行的,推迟的是数据的发射.

以前刚接触时我误以为是doSomething()也推迟了,其实不是的.


## XXDelayError

我们知道, 在进行一些合并操作时,如果碰到某个Observable发送了Error事件,则操作就会终止. 这时候如果需要先暂时忽略错误,将相应的操作进行完后再将发送Error事件,测可以用该方法对应的DelayError版本的方法.

举个例子, Observable.concat是将几个Observable的数据合并, 如下所示,第一个Observable除了发射数据外,还会发射一个Error,如果使用concat, 则无法合并第二个Observable的内容, 具体如下:

```
Observable<String> obs1 = Observable.create(s->{
			s.onNext("a1");
			s.onNext("a2");
			s.onNext("a3");
			s.onError(new Throwable("error from obs1"));
		});
		
Observable<String> obs2 = Observable.just("b1","b2","b3");
```


使用concat:

```
Observable.concat(obs1,obs2).subscribe(System.out::println
				,e->System.out.println(e.getMessage())
				,()->System.out.println("onCompleted"));
```				

输出如下:

```
a1
a2
a3
error from obs1

```

改用concatDelayError,则Error会推迟到最后获得, 能获取到其他的数据, 具体如下:

```
Observable.concatDelayError(Arrays.asList(obs1,obs2)).subscribe(System.out::println
				,e->System.out.println(e.getMessage())
				,()->System.out.println("onCompleted"));

```

输出如下:			

```
a1
a2
a3
b1
b2
b3
error from obs1
```

很多函数都有提供DelayError版本的方法, 比如:

combineLatestDelayError,concatDelayError, mergeDelayError, concatMapDelayError, switchMapDelayError, switchOnNextDelayError.




//-----------


Investigating Your RAM Usage
https://developer.android.com/studio/profile/investigate-ram.html