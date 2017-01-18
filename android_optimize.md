# Android优化 杂 note


## 关于性能分析

---

大多数开发者在没有发现严重性能问题之前是不会特别花精力去关注性能优化的，通常大家关注的都是功能是否实现。当性能问题真的出现的时候，请不要慌乱。我们通常采用下面三个步骤来解决性能问题。

### 1. Gather：收集数据
我们可以通过Android SDK里面提供的诸多工具来收集CPU，GPU，内存，电量等等性能数据，

### 2. Insight：分析数据
通过上面的步骤，我们获取到了大量的数据，下一步就是分析这些数据。工具帮我们生成了很多可读性强的表格，我们需要事先了解如何查看表格的数据，每一项代表的含义，这样才能够快速定位问题。如果分析数据之后还是没有找到问题，那么就只能不停的重新收集数据，再进行分析，如此循环。

### 3. Action：解决问题
定位到问题之后，我们需要采取行动来解决问题。解决问题之前一定要先有个计划，评估这个解决方案是否可行，是否能够及时的解决问题。


为了寻找内存的性能问题，Android Studio提供了工具来帮助开发者:

- Memory Monitor: 查看整个app所占用的内存，以及发生GC的时刻，短时间内发生大量的GC操作是一个危险的信号。
- Allocation Tracker: 使用此工具来追踪内存的分配，前面有提到过。
- Heap Tool: 查看当前内存快照，可以看到当前进程中的Heap Size的情况，分别有哪些类型的数据，占比是多少,便于对比分析哪些对象有可能是泄漏了的.
- TraceView-"start method profiling"/"stop method profiling":TraceView 是 Android 平台配备一个很好的性能分析的工具。它可以通过图形化的方式让我们了解我们要跟踪的程序的性能，并且能具体到method,即能知道每个方法的执行时间.
- Mat: Eclipse Memory Analysis Tools (MAT) 是一个分析 Java堆数据的专业工具，用它可以定位内存泄漏的原因。
从一般的Java应用程序或者Android程序中dump出hprof文件，然后用Mat/Eclipse打开，进行分析，从而分析内存泄露原因。
- Lint: Lint是Android提供的一个静态扫描应用源码并找出其中的潜在问题的一个强大的工具。如果我们在onDraw方法里面执行了new对象的操作，Lint就会提示我们这里有性能问题，并提出对应的建议方案。Lint的功能非常强大，他能够扫描各种问题。当然我们可以通过Android Studio设置找到Lint，对Lint做一些定制化扫描的设置，可以选择忽略掉那些不想Lint去扫描的选项，我们还可以针对部分扫描内容修改它的提示优先级。	
前三个都是内存相关的.


### 什么是Memory Churn内存抖动

Memory Churn内存抖动，内存抖动是因为在短时间内大量的对象被创建又马上被释放。瞬间产生大量的对象会严重占用Young Generation的内存区域，当达到阀值，剩余空间不够的时候，会触发GC从而导致刚产生的对象又很快被回收。即使每次分配的对象占用了很少的内存，但是他们叠加在一起会增加Heap的压力，从而触发更多其他类型的GC。这个操作有可能会影响到帧率，并使得用户感知到性能问题。

应该避免在onDraw()方法里面执行导致内存分配的操作,因为onDraw会经常调用,如果在这里分配内存,内存会经常分配,也会造成内存抖动.

采用对象池可以解决内存抖动问题.
才对对象池,一开始是没对象的, 可以用到时判断没有,创建一个然后加进去,也可以预先分配一定量的对象.

使用对象池也有不好的一面，程序员需要手动管理这些对象的分配与释放，所以我们需要慎重地使用这项技术，避免发生对象的内存泄漏。为了确保所有的对象能够正确被释放，我们需要保证加入对象池的对象和其他外部对象没有互相引用的关系。



# 图片优化


## 图片像素格式

---

为了避免加载一张超大的图片，需要尽量减少这张图片所占用的内存大小，Android为图片提供了4种解码格式，他们分别占用的内存大小如下图所示：

![像素格式](http://hukai.me/images/android_perf_2_pixel_format.png)

随着解码占用内存大小的降低，清晰度也会有损失。我们需要针对不同的应用场景做不同的处理，大图和小图可以采用不同的解码率。

## 缓存

---

xxxx


## 对bitmap做缩放

---

对bitmap做缩放的意义很明显，提示显示性能，避免分配不必要的内存。

缩放方法:

1. createScaledBitmap(): 这个方法可以获取到一张经过缩放的图片,这个方法能够执行的前提是，原图片需要事先加载到内存中，如果原图片过大，很可能导致OOM。
1. 使用BitmapFactory.Options.inSampleSize来decodeFile: 属性值inSampleSize表示缩略图大小为原始图片大小的几分之一，即如果这个值为2，则取出的缩略图的宽和高都是原始图片的1/2，图片大小就为原始大小的1/4.inSampleSize能够等比的缩放显示图片，同时还避免了需要先把原图加载进内存的缺点。
1. 还可以使用BitmapFactory.Options的inScaled，inDensity，inTargetDensity的属性来对解码图片做处理.

还有一个经常使用到的技巧是inJustDecodeBounds，使用这个属性去尝试解码图片，可以事先获取到图片的大小而不至于占用什么内存.



## 使用第三方图片加载库

---

上面说的很多策略这些库都使用了.




## 延迟任务

很多场景下的可以采用延迟任务达到优化的目的, 通常有下面三种方式达到优化：

1. AlarmManager: 使用AlarmManager设置定时任务，可以选择精确的间隔时间，也可以选择非精确时间作为参数。除非程序有很强烈的需要使用精确的定时唤醒，否者一定要避免使用他，我们应该尽量使用非精确的方式。
2. SyncAdapter: 我们可以使用SyncAdapter为应用添加设置账户，这样在手机设置的账户列表里面可以找到我们的应用。这种方式功能更多，但是实现起来比较复杂。我们可以从这里看到官方的培训课程：http://developer.android.com/training/sync-adapters/index.html
3. JobSchedulor: 这是最简单高效的方法，我们可以设置任务延迟的间隔，执行条件，还可以增加重试机制。



## Reference:

1. [Android性能优化典范 - 第1季]( http://hukai.me/android-performance-patterns/)
1. [Android性能优化之运算篇]( http://hukai.me/android-performance-compute/)
1. [Android性能优化之渲染篇]( http://hukai.me/android-performance-render/)
1. [Android性能优化之内存篇]( http://hukai.me/android-performance-memory/)
1. [Android性能优化之电量篇]( http://hukai.me/android-performance-battery/)
1. [Android性能优化典范 - 第2季](http://hukai.me/android-performance-patterns-season-2/)
1. [Android性能优化典范 - 第3季](http://hukai.me/android-performance-patterns-season-3/)
1. [Android性能优化典范 - 第4季](http://hukai.me/android-performance-patterns-season-4/)
1. [Android性能优化典范 - 第5季](http://hukai.me/android-performance-patterns-season-5/)
1. [Android性能优化典范 - 第6季](http://hukai.me/android-performance-patterns-season-6/)





## 1. Daily todo
1. SparseArray
1. ArrayMap


Java 8：CompletableFuture终极指南(http://www.importnew.com/10815.html)
Java CompletableFuture 详解(http://www.cnblogs.com/leetieniu2014/p/5403277.html)


Thread t = new  Thread(new Runnable()){

}




ss  
    debugCompile files('fuyou_lib/paysdkPY36sdk_debug.jar')
    releaseCompile files('fuyou_lib/paysdkPY36sdk_release.jar')

packagingOptions {
    exclude 'fuyou_lib/paysdkPY36sdk_debug.jar'
}
packagingOptions {
    exclude 'fuyou_lib/paysdkPY36sdk_release.jar'
}

fuyou_lib/paysdkPY36sdk_release.jar




目前：
iOS分享活动时，标题和正文均显示的是标题的内容；
Android分享活动时，标题和正文均显示的是标题的内容；

目标：
iOS分享活动时，标题显示标题的内容，正文显示正文的内容。

特殊场景方案：
如分享时，获取不到内容，图片则用logo,标题默认“石头理财”，正文默认空。




JSONObject jsonObject = new JSONObject(result);
						if (jsonObject.has("resText")) {
							String resText = jsonObject.getString("resText");
							String isEnc = jsonObject.getString("isEnc");


