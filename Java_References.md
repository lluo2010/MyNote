# Java三种Reference理解


## Java.lang.ref

---

1. 概述

    Java.lang.ref 是 Java 类库中比较特殊的一个包，它提供了与Java垃圾回收器密切相关的引用类。这些引用类对象可以指向其它对象，但它们不同于一般的引用，因为它们的存在并不防碍Java垃圾回收器对它们所指向的对象进行回收。其好处就在于使用者可以保持对使用对象的引用，同时 JVM 依然可以在内存不够用的时候对使用对象进行回收。因此这个包在用来实现与缓存相关的应用时特别有用。同时该包也提供了在对象的“可达”性发生改变时，进行提醒的机制。

1. 包结构

    ![包结构图](https://www.evernote.com/shard/s164/sh/6de7c6b8-dff0-4aeb-af62-c79e02171c64/9341b62f71030a49/res/28429120-a46a-45e9-9954-49b620756f0a.png?resizeSmall&width=832)
     
1. 各个类关系

    ![各个类关系图](https://www.evernote.com/shard/s164/sh/6de7c6b8-dff0-4aeb-af62-c79e02171c64/9341b62f71030a49/res/0f67f408-c18e-433b-8a30-620a7754b829.png?resizeSmall&width=832)



## StrongReference

---

其实就是我们平常用的引用，目前看是没有和SoftReference类似的StrongReference类的，它只是对我们平常的引用的一个称呼。



## SoftReference

---

### 描述

SoftReference 在“弱引用”中属于最强的引用。SoftReference 所指向的对象，当没有强引用指向它时，会在内存中停留一段的时间，垃圾回收器会根据 JVM 内存的使用情况（内存的紧缺程度）以及 SoftReference 的 get() 方法的调用情况来决定是否对其进行回收。
软引用可用来实现内存敏感的高速缓存。

软引用可以和一个引用队列（ReferenceQueue）联合使用，如果软引用所引用的对象被垃圾回收器回收，Java虚拟机就会把这个软引用加入到与之关联的引用队列中。


### 用法

一般是通过 SoftReference 的构造方法，将需要用弱引用来指向的对象包装起来。当需要使用的时候，调用 SoftReference 的 get() 方法来获取。当对象未被回收时 SoftReference 的 get() 方法会返回该对象的引用, 如果回收了返回null.

```
String abc=new String("abc");  //1  
SoftReference<String> abcSoftRef=new SoftReference<String>(abc);  //2  
```

当gc决定要收集软引用是执行以下过程,以上面的abcSoftRef为例：

1. 首先将abcSoftRef的referent设置为null，不再引用heap中的new String("abc")对象。
1. 将heap中的new String("abc")对象设置为可结束的(finalizable)。
1. 当heap中的new String("abc")对象的finalize()方法被运行而且该对象占用的内存被释放， abcSoftRef被添加到它的ReferenceQueue中。

注:对ReferenceQueue软引用和弱引用可以有可无，但是虚引用必须有，参见：

```
Reference(T paramT, ReferenceQueue<? super T>paramReferenceQueue)   
```

### 特征

1. 软引用使用 get() 方法取得对象的引用从而访问目标对象。
1. 软引用所指向的对象按照 JVM 的使用情况（Heap 内存是否临近阈值）来决定是否回收。
1. 软引用可以避免 Heap 内存不足所导致的异常。

** 当垃圾回收器决定对其回收时，会先清空它的 SoftReference，也就是说 SoftReference 的 get() 方法将会返回 null，然后再调用对象的 finalize() 方法，并在下一轮 GC 中对其真正进行回收。也就是说在调用finalize()方法之前SoftReference的get()方法就返回null了。**


### 例子

```
A obj = new A();
SoftRefenrence sr = new SoftReference(obj);
obj = null; //强引用释放了
```

引用时：

```
if(sr!=null){
    obj = sr.get();
}else{
    obj = new A();
    sr = new SoftReference(obj);
}
```
以上可以作为cache的用法。



## WeakReference

---

### 描述
WeakReference 是弱于 SoftReference 的引用类型。弱引用的特性基本与软引用相似，区别就在于弱引用所指向的对象只要满足进行系统垃圾回收的条件（没有强引用），不管内存使用情况如何，永远对其进行回收（get() 方法返回 null）。不过，由于垃圾回收器是一个优先级很低的线程，因此不一定会很快发现那些只具有弱引用的对象, 也就是说,并不是说对象被设置为null后,它就马上被回收了。


### 用法
和SoftReference类似。


### 特征

1. 弱引用使用 get() 方法取得对象的引用从而访问目标对象。
1. 一旦系统内存回收，无论内存是否紧张，弱引用指向的对象都会被回收。
1. 弱引用也可以避免 Heap 内存不足所导致的异常。
      ** 也就说当系统进行回收时就会把WeakReference指向的对象回收了。**


### 作用

** 如果你希望能随时取得某对象的信息，但又不想影响此对象的垃圾收集(也就是说对象不再使用时而去GC时,对象能被GC掉)，那么你应该用 Weak Reference 来记住此对象，而不是用一般的 reference **.   所以Android里内部的静态Handler使用WeakReference的Activity, 这样保证就可以引用Activity,又可以在Activity生命周期结束后能正常被释放.

### 例子1

```
String abc=new String("abc");  
WeakReference<String> abcWeakRef = new WeakReference<String>(abc);  
abc=null;  
System.out.println("before gc: "+abcWeakRef.get());  
System.gc();  
System.out.println("after gc: "+abcWeakRef.get());  
```

运行结果:  

```
before gc: abc  
after gc: null  
```
gc收集弱可及对象的执行过程和SoftReference一样，只是gc不会根据内存情况来决定是不是收集该对象。


### 例子2

```
A obj = new A();
WeakReference wr = new WeakReference(obj);
obj = null;
//等待一段时间，obj对象就会被垃圾回收
...
if (wr.get()==null) {
System.out.println("obj 已经被清除了 ");
} else {
System.out.println("obj 尚未被清除，其信息是 "+obj.toString());
}
...
}
```



## PhantomReference

### 描述
PhantomReference 是所有“弱引用”中最弱的引用类型。** 不同于软引用和弱引用，虚引用无法通过 get() 方法来取得目标对象的引用从而使用目标对象，观察源码可以发现 get() 被重写为永远返回 null。**

** 虚引用主要被用来跟踪对象被垃圾回收的状态，通过查看引用队列中是否包含对象所对应的虚引用来判断它是否即将被垃圾回收，从而采取行动。它并不被期待用来取得目标对象的引用，而目标对象被回收前，它的虚引用会被放入一个 ReferenceQueue 对象中，从而达到跟踪对象垃圾回收的作用。**

当GC一旦发现了虚引用对象，将会将PhantomReference对象插入ReferenceQueue队列.

而此时PhantomReference所指向的对象并没有被GC回收(finalized()应该是已经调用了)，而是要等到ReferenceQueue被你真正的处理后才会被回收.


### 用法
所以具体用法和之前两个有所不同，它必须传入一个 ReferenceQueue 对象。当虚引用所引用对象被垃圾回收后，虚引用会被添加到这个队列中。

```
Object obj = new Object(); 
ReferenceQueue<Object> refQueue = new ReferenceQueue<Object>(); 
PhantomReference<Object> phantom = new PhantomReference<Object>(obj, refQueue); 
```

### 特征

1. 虚引用与软引用和弱引用的一个区别在于：虚引用必须和引用队列 （ReferenceQueue）联合使用。
1. PhantomReference的get()方法永远null,所以PhantomReference永远无法拿到它关联的原来的对象。
1. 它的作用是通过对加入的ReferenceQueue来判断它指向的对象是不是被回收了从而做善后处理。

如果它指向的对象还没有被回收，ReferenceQueue内是无法poll到该PhantomReference，如果回收了(finalize()后，真正回收前,下一步就是回收了)，则ReferenceQueue.poll则返回不为null.

** 通过判断引用队列中是否已经加入了虚引用，来了解被引用的对象是否将要被垃圾回收。** 如果程序发现某个虚引用已经被加入到ReferenceQueue，那么就可以在所引用的对象的内存被回收之前采取必要的行动.

** 由于Object.finalize()方法的不安全性、低效性，常常使用虚引用完成对象回收前的资源释放工作.**

### 例子

```
private void phantomRefTest(){
     Object obj = new Object(); 
     ReferenceQueue<Object> refQueue = new ReferenceQueue<Object>(); 
     PhantomReference<Object> phantom = new PhantomReference<Object>(obj, refQueue); 
     System.out.println("phantom.get:"+phantom.get());      //null
     System.out.println("RefQueue.poll:"+refQueue.poll());// null 
     obj = null; 
     System.gc(); 

     // 调用phanRef.get()不管在什么情况下会一直返回null 
     System.out.println("phantom.get:"+phantom.get());  //null

     // 当GC发现了虚引用，GC会将phanRef插入进我们之前创建时传入的refQueue队列 
     // 注意，此时phanRef所引用的obj对象，并没有被GC回收，在我们显式地调用refQueue.poll返回phanRef之后 
     // 当GC第二次发现虚引用，而此时JVM将phanRef插入到refQueue会插入失败，此时GC才会对obj进行回收 
     try {
          Thread.sleep(200);
     } catch (InterruptedException e) {
          e.printStackTrace();
     } 
     System.out.println("refqueue.poll:"+refQueue.poll());       //java.lang.ref.PhantomReference@xxxxx
}
```


## 例子

---

1. 检测是否被GC的一个应用

    这是Universal imager loader的一个例子, ViewAware保持对View的一个引用,但是又不想影响对象的GC,采用的就是WeakReference,方法isCollected()用来判断对象是否已经GC了.

    部分代码如下:

    ```
    public abstract class ViewAware implements ImageAware {
        protected Reference<View> viewRef;

        public ViewAware(View view, boolean checkActualViewSize) {
            if (view == null) throw new IllegalArgumentException("view must not be null");
            this.viewRef = new WeakReference<View>(view);
            ...
        }

        @Override
        public boolean isCollected() {
            return viewRef.get() == null;
        }
    }
    ```

1. 检测对象何时被GC

    ```
    Utils.printHeader("testPhantomReference");
    latch = new CountDownLatch(1);
    Runnable r = new Runnable() {
        public void run() {
            FinalizeClass c1 = new FinalizeClass(1, "1");
            ReferenceQueue<FinalizeClass> rq = new ReferenceQueue<>();
            PhantomReference<FinalizeClass> pr = new PhantomReference<ReferenceTest.FinalizeClass>(c1, rq);
            Utils.println("before set obj to null;");
            c1 = null;
            System.gc();
            Utils.println("after set obj to null;");
            test1Run(rq);
        }
    };

    new Thread(r).start();

    try {
        latch.await();
    } catch (InterruptedException e) {
        e.printStackTrace();
    }


    private void test1Run(ReferenceQueue<FinalizeClass> rq){
        while (true){
            Utils.println("try to gc again");
            System.gc();
            Reference<FinalizeClass> r = (Reference<FinalizeClass>)rq.poll();
            if (r!=null){
                Utils.println("--polled reference from queue");
                break;
            }
        }
        latch.countDown();
    }
    ```
上面的例子如果使用WeakReference,发现对poll()后得到的WeakReference进行get操作, 得到的对象已经是null.

1. 当对象不可用时,对其相关的资源做回收

    平常我们尽管检查到某个对象不可用了, 如果对其相关的资源进行回收呢? 可以采用WeakReference(对, 是WeakReference, 不是PhatomeReference)以及ReferenceQueue. 通过判断WeakRerence.get()返回是不是null判断对象是不是可以被回收了, 然后通过ReferenceQueue.poll()找到是哪个被回收了.

    如下代码判断来自Guava中的Cache相关,各个Entry都从WeakReference派生, 构造时除了传入key, 还传入ReferenceQueue和hash, 当发现Entry的key为空时, 调用drainKeyReferenceQueue().
    drainKeyReferenceQueue()对ReferenceQueue队形poll(), 如果返回不等于pull, 表示有对象即将被回收, 则通过Entry内部的hash找到其他对应的需要回收的资源进行回收.

    ```
    static class WeakEntry<K, V> extends WeakReference<K> implements ReferenceEntry<K, V> {
        WeakEntry(ReferenceQueue<K> queue, K key, int hash, @Nullable ReferenceEntry<K, V> next) {
        super(key, queue);
        this.hash = hash;
        this.next = next;
        }

    static class WeakValueReference<K, V> extends WeakReference<V> implements ValueReference<K, V> {
        final ReferenceEntry<K, V> entry;

        WeakValueReference(ReferenceQueue<V> queue, V referent, ReferenceEntry<K, V> entry) {
        super(referent, queue);
        this.entry = entry;
        }


    <K, V> ReferenceEntry<K, V> newEntry(
            Segment<K, V> segment, K key, int hash, @Nullable ReferenceEntry<K, V> next) {
            return new WeakEntry<K, V>(segment.keyReferenceQueue, key, hash, next);
        }

    void drainKeyReferenceQueue() {
        Reference<? extends K> ref;
        int i = 0;
        while ((ref = keyReferenceQueue.poll()) != null) {
            @SuppressWarnings("unchecked")
            ReferenceEntry<K, V> entry = (ReferenceEntry<K, V>) ref;
            map.reclaimKey(entry);
            if (++i == DRAIN_MAX) {
            break;
            }
        }
        }
    ```


## 总结

---

引用和引用队列提供了一种通知机制，允许我们知道对象已经被销毁或者即将被销毁。GC要回收一个对象的时候，如果发现该对象有软、弱、虚引用的时候，会将这些引用加入到注册的引用队列中。

也就是说如果这三种引用它们在创建时有和ReferenceQueue关联，当这三种引用指向的对象要被回收时，它们会被加入ReferenceQueue.

如果SoftReference和WeakReference(PhantomReference是一定要关联的)没有和ReferenceQueue关联，则指向的对象被回收时，SoftReference和WeakReference是不为null的，所以可以通过的get()返回是不是null来判断对象有没有被回收了。


Type | Purpose | Use | When GCed | Implementing Class
-|-|-|-|-
Strong Reference| An ordinary reference. Keeps objects alive as long as they are referenced.| normal reference.| Any object not pointed to can be reclaimed.| default
Soft Reference|Keeps objects alive provided there’s enough memory.| to keep objects alive even after clients have removed their references (memory-sensitive caches), in case clients start asking for them again by key.| After a first gc pass, the JVM decides it still needs to reclaim more space.	| java.lang.ref.SoftReference
Weak Reference|Keeps objects alive only while they’re in use (reachable) by clients.| Containers that automatically delete objects no longer in use.| After gc determines the object is only weakly reachable| java.lang.ref.WeakReference java.util.WeakHashMap
Phantom Reference|Lets you clean up after finalization but before the space is reclaimed (replaces or augments the use of finalize())| Special clean up processing| After finalization.| java.lang.ref.PhantomReference
