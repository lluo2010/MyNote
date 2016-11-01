EventBus reading


## 对象池PendingPost
PendingPost即是一个对象,又是一个对象池.
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

