# 读EventBus代码 note


## 对象池PendingPost
PendingPost既是一个对象,又是一个对象池.

1. 对象存放  
内部有一个静态的名为pendingPostPool的list,用来存放对象.

1. 对象的获取  
如果需要对象,通过obtainPendingPost(xx,xx)方法获取一个PendingPost,这个方法会把PendingPost内实际的参数带进来.如果list里有对象,从list里取出一个对象,将参数的值赋给该对象,然后返回;如果list里无对象,则直接new一个新的.  
**注意:从list里获取对象前,要将对象从list里移除,这样对象就不属于list了.**

1. 对象的移除  
其实对于对象池来说,是对象重新回到对象池.调用函数releasePendingPost来完成.这里需要注意两点:
    + 要将PendingPost内部的对象都置成null来释放引用.
    + 不能无限制的重新加到对象池,需要有一个限制,超过该限制不会往pool里增加,目前为10000.

1. 里面的next是为了Queue服务的,和对象池没有什么关系.
1. 成员变量subscription包含了要执行的方法的Method,以及整个方法的执行对象.

代码如下:

```
final class PendingPost {
    private final static List<PendingPost> pendingPostPool = new ArrayList<PendingPost>();

    Object event;
    Subscription subscription;
    PendingPost next;

    private PendingPost(Object event, Subscription subscription) {
        this.event = event;
        this.subscription = subscription;
    }

    static PendingPost obtainPendingPost(Subscription subscription, Object event) {
        synchronized (pendingPostPool) {
            int size = pendingPostPool.size();
            if (size > 0) {
                PendingPost pendingPost = pendingPostPool.remove(size - 1);
                pendingPost.event = event;
                pendingPost.subscription = subscription;
                pendingPost.next = null;
                return pendingPost;
            }
        }
        return new PendingPost(event, subscription);
    }

    static void releasePendingPost(PendingPost pendingPost) {
        pendingPost.event = null;
        pendingPost.subscription = null;
        pendingPost.next = null;
        synchronized (pendingPostPool) {
            // Don't let the pool grow indefinitely
            if (pendingPostPool.size() < 10000) {
                pendingPostPool.add(pendingPost);
            }
        }
    }

}
```

## 半阻塞队列PendingPostQueue
之所以说是半阻塞队列,是因为往队列添加元素enqueue时是不阻塞的,是不考虑元素存放限制的. 只是在获取时提供了一种无数据是等待一段时间后超时的方法.

1. 是一个双向链表  
有头尾两个PendingPost:head 和tail. PendingPost里有next指向它的下一个,所以形成了一个双向链表.  
1. 取出poll  
返回head,同时将head指向它的下一个. poll分两种:
    + 是空不等待,直接返回null
    + 是空的话会等待一定的时间,再尝试获取.

1. 锁加在poll和enqueue()的方法上.
1. 由于阻塞的那个poll会处于等待的场景, 所以enqueue时候会调用notifyAll()通知

代码如下:

```
final class PendingPostQueue {
    private PendingPost head;
    private PendingPost tail;

    synchronized void enqueue(PendingPost pendingPost) {
        if (pendingPost == null) {
            throw new NullPointerException("null cannot be enqueued");
        }
        if (tail != null) {
            tail.next = pendingPost;
            tail = pendingPost;
        } else if (head == null) {
            head = tail = pendingPost;
        } else {
            throw new IllegalStateException("Head present, but no tail");
        }
        notifyAll();
    }

    synchronized PendingPost poll() {
        PendingPost pendingPost = head;
        if (head != null) {
            head = head.next;
            if (head == null) {
                tail = null;
            }
        }
        return pendingPost;
    }

    synchronized PendingPost poll(int maxMillisToWait) throws InterruptedException {
        if (head == null) {
            wait(maxMillisToWait);
        }
        return poll();
    }

}

```

## BackgroundPoster在后台单任务中处理多个任务

在eventbus中这个是用来在后台的一个线程中执行任务.

BackgroundPoster本身就是一个Runnable,多个任务被添加到队列PendingPostQueue里,任务的处理都是在run()函数里处理.外部通过enqueue()函数将任务加进来.

每个批次的任务都是在同一个线程执行,执行完了线程结束,下次再有任务进来,又会从线程池获取一个线程执行那个批次的所有任务.

这与每个任务一个线程执行是不一样的.

具体的:

1. 任务的加入和执行  
通过enqueue()方法,任务被加入queue里.中间也使用了对象池PendingPost. 在run()函数里,任务被不停的取出并执行.

1. 线程的启动
Runnable是被加入线程池执行的,也就是说它只使用一个线程池中的一个线程来执行一个批次的任务.
标记executorRunning用来表示线程是否开启. 当调用enqueue()添加任务,会利用该标记判断线程是否开启,如果没有开启,会执行getExecutorService().execute(this)开启线程.

1. 线程的结束  
当queue中的任务执行完了,线程也就结束了, executorRunning标记置为false.


代码如下:

```
final class BackgroundPoster implements Runnable {

    private final PendingPostQueue queue;
    private final EventBus eventBus;

    private volatile boolean executorRunning;

    BackgroundPoster(EventBus eventBus) {
        this.eventBus = eventBus;
        queue = new PendingPostQueue();
    }

    public void enqueue(Subscription subscription, Object event) {
        PendingPost pendingPost = PendingPost.obtainPendingPost(subscription, event);
        synchronized (this) {
            queue.enqueue(pendingPost);
            if (!executorRunning) {
                executorRunning = true;
                eventBus.getExecutorService().execute(this);
            }
        }
    }

    @Override
    public void run() {
        try {
            try {
                while (true) {
                    PendingPost pendingPost = queue.poll(1000);
                    if (pendingPost == null) {
                        synchronized (this) {
                            // Check again, this time in synchronized
                            pendingPost = queue.poll();
                            if (pendingPost == null) {
                                executorRunning = false;
                                return;
                            }
                        }
                    }
                    eventBus.invokeSubscriber(pendingPost);
                }
            } catch (InterruptedException e) {
                Log.w("Event", Thread.currentThread().getName() + " was interruppted", e);
            }
        } finally {
            executorRunning = false;
        }
    }

}

```

## HandlerPoster

在eventbus中, HandlerPoster用来在主线程中执行某些任务. 其实它也可以用来在某个线程中执行某些任务, 主要关联到其他线程的looper就可以了.

一般要利用Handler执行一些Runnable,会将Handler和一个looper关联,然后需要执行任务时,直接post(runnable).但是如果是在UI线程中执行,需要考虑主线程还需要影响其他的请求的,如果执行任务占用时间太多,会造成ANR.

HandlerPoster的采用的方法是将task放入queue里, 然后发送一个message, 在handler.handleMessage()里面,不停的从queue取出任务,然后执行,当总的时间超过一点的时间间隔,结束handleMessage.
如果队列里还有任务没完成,则会在退出前在发送一个message.如果任务都执行完了,在会在下次enqueue()调用,任务被加近期的时候再发送一个message.

具体如下:

1. 任务执行

1. 



代码如下:

```
final class HandlerPoster extends Handler {

    private final PendingPostQueue queue;
    private final int maxMillisInsideHandleMessage;
    private final EventBus eventBus;
    private boolean handlerActive;

    HandlerPoster(EventBus eventBus, Looper looper, int maxMillisInsideHandleMessage) {
        super(looper);
        this.eventBus = eventBus;
        this.maxMillisInsideHandleMessage = maxMillisInsideHandleMessage;
        queue = new PendingPostQueue();
    }

    void enqueue(Subscription subscription, Object event) {
        PendingPost pendingPost = PendingPost.obtainPendingPost(subscription, event);
        synchronized (this) {
            queue.enqueue(pendingPost);
            if (!handlerActive) {
                handlerActive = true;
                if (!sendMessage(obtainMessage())) {
                    throw new EventBusException("Could not send handler message");
                }
            }
        }
    }

    @Override
    public void handleMessage(Message msg) {
        boolean rescheduled = false;
        try {
            long started = SystemClock.uptimeMillis();
            while (true) {
                PendingPost pendingPost = queue.poll();
                if (pendingPost == null) {
                    synchronized (this) {
                        // Check again, this time in synchronized
                        pendingPost = queue.poll();
                        if (pendingPost == null) {
                            handlerActive = false;
                            return;
                        }
                    }
                }
                eventBus.invokeSubscriber(pendingPost);
                long timeInMethod = SystemClock.uptimeMillis() - started;
                if (timeInMethod >= maxMillisInsideHandleMessage) {
                    if (!sendMessage(obtainMessage())) {
                        throw new EventBusException("Could not send handler message");
                    }
                    rescheduled = true;
                    return;
                }
            }
        } finally {
            handlerActive = rescheduled;
        }
    }
}
```


