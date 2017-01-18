# LruCache & DiskLruCache note


## LruCache

---

Android用LruCache来取代原来强引用和软引用实现内存缓存.LruCache使用一个LinkedHashMap简单的实现内存的缓存，没有软引用，都是强引用。

主要算法原理是把最近使用的对象用强引用存储在LinkedHashMap 中，并且把最近最少使用的对象在缓存值达到预设定值之前从内存中移除。

在过去，我们经常会使用一种非常流行的内存缓存技术的实现，即软引用或弱引用 (SoftReference or WeakReference)。但是现在已经不再推荐使用这种方式了，从Android 2.3 (API Level 9) 开始，垃圾回收开始强制的回收掉 soft/weak 引用 从而导致这些缓存没有任何效率的提升。因为从 Android 2.3 (API Level 9)开始，垃圾回收器会更倾向于回收持有软引用或弱引用的对象，这让软引用和弱引用变得不再可靠。

为了能够选择一个合适的缓存大小给LruCache, 有以下多个因素应该放入考虑范围内，例如：

1. 你的设备可以为每个应用程序分配多大的内存？
2. 设备屏幕上一次最多能显示多少张图片？有多少图片需要进行预加载，因为有可能很快也会显示在屏幕上？
3. 你的设备的屏幕大小和分辨率分别是多少？一个超高分辨率的设备（例如 Galaxy Nexus) 比起一个较低分辨率的设备（例如 Nexus S），在持有相同数量图片的时候，需要更大的缓存空间。
4. 图片的尺寸和大小，还有每张图片会占据多少内存空间。
5. 图片被访问的频率有多高？会不会有一些图片的访问频率比其它图片要高？** 如果有的话，你也许应该让一些图片常驻在内存当中，或者使用多个LruCache 对象来区分不同组的图片。**
6. 你能维持好数量和质量之间的平衡吗？有些时候，存储多个低像素的图片，而在后台去开线程加载高像素的图片会更加的有效。

并没有一个指定的缓存大小可以满足所有的应用程序，这是由你决定的。你应该去分析程序内存的使用情况，然后制定出一个合适的解决方案。一个太小的缓存空间，有可能造成图片频繁地被释放和重新加载，这并没有好处。而一个太大的缓存空间，则有可能还是会引起 java.lang.OutOfMemory 的异常。

重要函数：

1. int sizeOf (K key, V value)

	>Returns the size of the entry for key and value in user-defined units. The default implementation returns 1 so that size is the number of entries and max size is the maximum number of entries.
	
	如果没有重写该函数，默认返回的是1，表示entries的个数。如果重写了，表示k,v对应的entry的大小。

1. 构造函数 public LruCache (int maxSize)

	>maxSize for caches that do not override sizeOf(K, V), this is the maximum number of entries in the cache. For all other caches, this is the maximum sum of the sizes of the entries in this cache.
	
	如果sizeof函数没有重写，maxSize是整个cache内允许的最大的entries数，如果重写了，表示的是允许的所有的entries的size大小，不是个数。：w



## DiskLruCache

---

由于DiskLruCache并不是由Google官方编写的，所以这个类并没有被包含在Android API当中，我们需要将这个类从网上下载下来，然后手动添加到项目当中。DiskLruCache的源码在Google Source上，地址如下： android.googlesource.com/platform/libcore/+/jb-mr2-release/luni/src/main/java/libcore/io/DiskLruCache.java

其实这个类就是负责把图片缓冲在文件中，一开始可以设定文件缓存的总大小，超过后会自动清除。

对于从网络上下载的图片，文件的名称需要和url对应，考虑到url有特殊字符，所以一般采用对url进行md5.



## Reference

---

1. [Android使用 LruCache 缓存图片](http://www.bdqn.cn/news/201307/10432.shtml)
1. [基于LinkedHashMap实现LRU缓存调度算法原理及应用](http://www.eoeandroid.com/thread-278540-1-1.html)
1. [Android DiskLruCache完全解析，硬盘缓存的最佳方案](http://blog.csdn.net/guolin_blog/article/details/28863651)
1. [Android照片墙完整版，完美结合LruCache和DiskLruCache](http://blog.csdn.net/fangzhibin4712/article/details/38823533)
1. [每天一个开源代码——硬盘缓存解决方案：DiskLruCache解析](http://www.eoeandroid.com/forum.php?mod=viewthread&tid=541478)
