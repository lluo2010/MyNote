# Android-Universal-Image-Loader cache整理


Universal image loader里, 对cache按回收条件划分, 分一下几类:

1. 有按size大小的, 比如总的size超过了一个设定的最大值, 比如:
    - LruMemoryCache
    - LimitedMemoryCache

1. 也有按已经存放时间的,比如LimitedAgeMemoryCache.
1. 也有按其他条件的, 比如FuzzyKeyMemoryCache, 使用时需要提供一个Comparator, 当Comparator返回0时,删除.


当需要回收时, 按删除策略划分, 分为几下几类:

1. LRU删除, 即最近使用的最后删除, 比如LruMemoryCache.
1. FIFO回收, 即先进来先回收, 比如FIFOLimitedMemoryCache.
1. 弱引用自动回收, 比如WeakMemoryCache.
1. 按size大小, 先删除大的,再删除小的. 比如LargestLimitedMemoryCache
1. 时间超过了就回收, 比如LimitedAgeMemoryCache.
1. 按时间频率回收, 使用频率越低越先回收, 比如UsingFreqLimitedMemoryCache.


这里还有一个技巧, 使用强引用加弱引用的方式实现cache, 比如LimitedMemoryCache:

1. 强引用的目的是在满足条件(比如大小)的情况下, 一定要保证是缓存的.
1. 弱引用的目的是, 当超过满足条件(比如总大小超过设定值)后, 强引用删除了, 但是如果整个环境的内存是不紧张的(使用soft Reference)或者还没进行GC的, 可以让那么已经remove掉的强引用的对象还可以存活一段时间, 这样在内存充足时可以充分利用缓存, 内存紧张时又会释放掉.



## MemoryCache

---

memory cache的接口，其他类cache接口/类的接口. 提供方法有:

```
boolean put(String key, Bitmap value);

/** Returns value by key. If there is no value for key then null will be returned. */
Bitmap get(String key);

/** Removes item by key */
Bitmap remove(String key);

/** Returns all keys of cache */
Collection<String> keys();

/** Remove all items from cache */
void clear();
```



## BaseMemoryCache

---

是一个抽象类, 是其他通过soft/weak引用实现的cache的抽象基类.

它使用一个value是Reference<BitMap>的HashMap存储key/value. 至于Reference最终是soft或者是weak, 则由子类实现抽象方法createReference(Bitmap)决定.

部分代码如下:

```
    public abstract class BaseMemoryCache implements MemoryCache {

	/** Stores not strong references to objects */
	private final Map<String, Reference<Bitmap>> softMap = Collections.synchronizedMap(new HashMap<String, Reference<Bitmap>>());
    ...
    /** Creates {@linkplain Reference not strong} reference of value */
	protected abstract Reference<Bitmap> createReference(Bitmap value);
    ...
    }
```



## LruMemoryCache

---

>A cache that holds strong references to a limited number of Bitmaps. Each time a Bitmap is accessed, it is moved to the head of a queue.

使用LinkedHashMap实现的强引用的cache. 根据LinkedHashMap的特性, 最近访问过的都放在后面. 当往里put后,会进行大小的check, 当当前大小超过设置的最大时候, 会从LinkedHashMap的头部开始做清除, 实现了LRU.

相应部分代码如下:

```
public class LruMemoryCache implements MemoryCache {

	private final LinkedHashMa<String, Bitmap> map;

	private final int maxSize;
	/** Size of this cache in bytes */
	private int size;

	/** @param maxSize Maximum sum of the sizes of the Bitmaps in this cache */
	public LruMemoryCache(int maxSize) {
		if (maxSize <= 0) {
			throw new IllegalArgumentException("maxSize <= 0");
		}
		this.maxSize = maxSize;
		this.map = new LinkedHashMap<String, Bitmap>(0, 0.75f, true);
	}
```


## FuzzyKeyMemoryCache:

---

该cache通过指定Comparator实现灵活的删除策略.

采用饰着者模式, 包含一个MemoryCache和一个Comparator，当put的key通过Comparator和已经存在的相同，则先删除已经存在的。至于比较规则, 通过实现Comparator灵活设置.

相应代码如下:

```
public class FuzzyKeyMemoryCache implements MemoryCache {

	private final MemoryCache cache;
	private final Comparator<String> keyComparator;

	public FuzzyKeyMemoryCache(MemoryCache cache, Comparator<String> keyComparator) {
		this.cache = cache;
		this.keyComparator = keyComparator;
	}
    @Override
	public boolean put(String key, Bitmap value) {
		// Search equal key and remove this entry
		synchronized (cache) {
			String keyToRemove = null;
			for (String cacheKey : cache.keys()) {
				if (keyComparator.compare(key, cacheKey) == 0) {
					keyToRemove = cacheKey;
					break;
				}
			}
			if (keyToRemove != null) {
				cache.remove(keyToRemove);
			}
		}
		return cache.put(key, value);
	}
    ...
```



## LimitedAgeMemoryCache

---

当cache的时间大于设定时间后, 就不缓存了, 这个工作每次在get()时候去做.

采用的是强应用,HashMap的value里存放缓存put()时候的时间. 由于它采用装饰者模式, 里面包含一个MemoryCache,所以和其他Cache一起用,包含其他cache的策略.

相应的代码判断如下:

```
public class LimitedAgeMemoryCache implements MemoryCache {

	private final MemoryCache cache;

	private final long maxAge;
	private final Map<String, Long> loadingDates = Collections.synchronizedMap(new HashMap<String, Long>());

	/**
	 * @param cache  Wrapped memory cache
	 * @param maxAge Max object age <b>(in seconds)</b>. If object age will exceed this value then it'll be removed from
	 *               cache on next treatment (and therefore be reloaded).
	 */
	public LimitedAgeMemoryCache(MemoryCache cache, long maxAge) {
		this.cache = cache;
		this.maxAge = maxAge * 1000; // to milliseconds
	}
```



## WeakMemoryCache

---

从BaseMemoryCache派生, 使用弱引用WeakReference实现缓存.

代码如下:

```
public class WeakMemoryCache extends BaseMemoryCache {
	@Override
	protected Reference<Bitmap> createReference(Bitmap value) {
		return new WeakReference<Bitmap>(value);
	}
}
```



## LimitedMemoryCache

---

从BaseMemoryCache派生, 是一个抽象类, 采用强引用加soft或者weak reference实现cache. 使用LinkedList存储Bitmap, 这里是强引用, 调用put()时候, 会先check是否超过了指定的sizeLimit, 如果是, 一直remove到不超过.
至于如何删除, 删除的策略是什么, 则由子类实现createReference()决定.

注意, 这里的删除只是从LinkedList上删除,即删除了强对象, soft/weak reference还在的, 实际对象会根据实际情况多存留一段时间.

采用强引用,保持在sizeLimit范围内的都cache, 使用soft/weak reference, 可以保证即使超过sizeLimit了, cache对象还能根据内存实际情况多存留一段时间.

子类需要实现的方法有:

1. protected abstract int getSize(Bitmap value):  
    返回图片的大小.
1. protected abstract Bitmap removeNext():  
    超过sizeLimit, 需要需要删除的Bitmap, 这里决定删除策略.
1. protected abstract Reference<Bitmap> createReference(Bitmap value):  
    决定对象的引用方式.

部分代码如下:

```
public abstract class LimitedMemoryCache extends BaseMemoryCache {

    ...
	private final int sizeLimit;

	private final AtomicInteger cacheSize;

	/**
	 * Contains strong references to stored objects. Each next object is added last. If hard cache size will exceed
	 * limit then first object is deleted (but it continue exist at {@link #softMap} and can be collected by GC at any
	 * time)
	 */
	private final List<Bitmap> hardCache = Collections.synchronizedList(new LinkedList<Bitmap>());

	/** @param sizeLimit Maximum size for cache (in bytes) */
	public LimitedMemoryCache(int sizeLimit) {
        ...
```



## LRULimitedMemoryCache

---

从LimitedMemoryCache继承, 除了拥有LimitedMemoryCache的最大size的限制外, 还使用LinkedHashMap实现了LRU. 它的Reference则采用WeakReference.

也就是说它本身包含一个LinkedHashMap, 从LimitedMemoryCache继承, 又包含一个LinkedList, LimitedMemoryCache从BaseMemoryCache派生,又包含了一个以Reference<Bitmap>为key的HashMap.

其实如果不是这种继承关系, 只要一个强引用的LinkedHashMap加一个Reference<Bitmap>为key的HashMap就可以了.

相应的代码如下:

```
public class LRULimitedMemoryCache extends LimitedMemoryCache {
	private static final int INITIAL_CAPACITY = 10;
	private static final float LOAD_FACTOR = 1.1f;

	private final Map<String, Bitmap> lruCache = Collections.synchronizedMap(new LinkedHashMap<String, Bitmap>(INITIAL_CAPACITY, LOAD_FACTOR, true));

	/** @param maxSize Maximum sum of the sizes of the Bitmaps in this cache */
	public LRULimitedMemoryCache(int maxSize) {
		super(maxSize);
	}

    @Override
	protected Bitmap removeNext() {
		Bitmap mostLongUsedValue = null;
		synchronized (lruCache) {
			Iterator<Entry<String, Bitmap>> it = lruCache.entrySet().iterator();
			if (it.hasNext()) {
				Entry<String, Bitmap> entry = it.next();
				mostLongUsedValue = entry.getValue();
				it.remove();
			}
		}
		return mostLongUsedValue;
	}

```


## FIFOLimitedMemoryCache

---

和LRULimitedMemoryCache类似, 从LimitedMemoryCache继承, 除了拥有LimitedMemoryCache的最大size的限制外, 还有一个包含强以后用的LinkedList, 实现了removeNext, 当要删除时,将最早入队的删除, 即先进先出.

实现了removeNext和createReference().

部分代码如下:

```
public class FIFOLimitedMemoryCache extends LimitedMemoryCache {

	private final List<Bitmap> queue = Collections.synchronizedList(new LinkedList<Bitmap>());

	public FIFOLimitedMemoryCache(int sizeLimit) {
		super(sizeLimit);
	}
    @Override
	protected Bitmap removeNext() {
		return queue.remove(0);
	}

	@Override
	protected Reference<Bitmap> createReference(Bitmap value) {
		return new WeakReference<Bitmap>(value);
	}
```

## UsingFreqLimitedMemoryCache

当大小超过sizeLimit时, 先删除使用频率少的. 

和LRULimitedMemoryCache类似, 从LimitedMemoryCache继承, 除了拥有LimitedMemoryCache的最大size的限制外, 还有一个包含HashMap<Bitmap, Integer>(), 里面记录了每个bitmap的使用次数.这个次数在每次get或者put时增加.重写removeNext(),每次删除使用频率最小的.

对应代码:

```
public class UsingFreqLimitedMemoryCache extends LimitedMemoryCache {
	/**
	 * Contains strong references to stored objects (keys) and last object usage date (in milliseconds). If hard cache
	 * size will exceed limit then object with the least frequently usage is deleted (but it continue exist at
	 * {@link #softMap} and can be collected by GC at any time)
	 */
	private final Map<Bitmap, Integer> usingCounts = Collections.synchronizedMap(new HashMap<Bitmap, Integer>());

	public UsingFreqLimitedMemoryCache(int sizeLimit) {
		super(sizeLimit);
	}
    ...
    protected Bitmap removeNext() {
		Integer minUsageCount = null;
		Bitmap leastUsedValue = null;
		Set<Entry<Bitmap, Integer>> entries = usingCounts.entrySet();
		synchronized (usingCounts) {
			for (Entry<Bitmap, Integer> entry : entries) {
				if (leastUsedValue == null) {
					leastUsedValue = entry.getKey();
					minUsageCount = entry.getValue();
				} else {
					Integer lastValueUsage = entry.getValue();
					if (lastValueUsage < minUsageCount) {
						minUsageCount = lastValueUsage;
						leastUsedValue = entry.getKey();
					}
				}
			}
		}
		usingCounts.remove(leastUsedValue);
		return leastUsedValue;
	}

	@Override
	protected Reference<Bitmap> createReference(Bitmap value) {
		return new WeakReference<Bitmap>(value);
	}
```



## LargestLimitedMemoryCache

---

当大小超过sizeLimit时, 先删除size最大的.

和LRULimitedMemoryCache类似, 从LimitedMemoryCache继承, 除了拥有LimitedMemoryCache的最大size的限制外, 还有一个包含HashMap<Bitmap, Integer>(), 里面记录了每个bitmap的大小.

对应代码:

```
public class LargestLimitedMemoryCache extends LimitedMemoryCache {
	/**
	 * Contains strong references to stored objects (keys) and sizes of the objects. If hard cache
	 * size will exceed limit then object with the largest size is deleted (but it continue exist at
	 * {@link #softMap} and can be collected by GC at any time)
	 */
	private final Map<Bitmap, Integer> valueSizes = Collections.synchronizedMap(new HashMap<Bitmap, Integer>());

	public LargestLimitedMemoryCache(int sizeLimit) {
		super(sizeLimit);
	}

    ...
    @Override
        protected Bitmap removeNext() {
            Integer maxSize = null;
            Bitmap largestValue = null;
            Set<Entry<Bitmap, Integer>> entries = valueSizes.entrySet();
            synchronized (valueSizes) {
                for (Entry<Bitmap, Integer> entry : entries) {
                    if (largestValue == null) {
                        largestValue = entry.getKey();
                        maxSize = entry.getValue();
                    } else {
                        Integer size = entry.getValue();
                        if (size > maxSize) {
                            maxSize = size;
                            largestValue = entry.getKey();
                        }
                    }
                }
            }
            valueSizes.remove(largestValue);
            return largestValue;
        }

        @Override
        protected Reference<Bitmap> createReference(Bitmap value) {
            return new WeakReference<Bitmap>(value);
        }
```



整理的类图关系如下:

![图关系图](https://www.evernote.com/shard/s164/sh/2414963d-b239-44a5-940f-4f24d7c7ee59/600d42d911a7b862/res/d390b836-8b71-42c0-8821-4ae02ff76226/cache.png?resizeSmall&width=832)