# Android handler内存泄露

## 泄露原因

---

当使用内部类来创建handler,Handler对象会隐式地持有一个外部类对象（通常是一个Activity）的引用.

如下:

```
Handler mHandler = new Handler() {
    @Override
    public void handleMessage(Message msg) {
        mImageView.setImageBitmap(mBitmap);
    }
}
```

那么,内存泄露会出现在下面几种场景:

1. 使用Handler的postDelayed()方法,Activity退出但是时间未到  
    该方法会将你的Handler装入一个Message，并把这条Message推到MessageQueue中，那么在你设定的delay到达之前，会有一条MessageQueue -> Message -> Handler -> Activity的链，导致你的Activity被持有引用而无法被回收。
1. Handler执行未结束但是Activity已经退出  
    比如执行后台进程, 后台线程结束后要通知Handler,所有后台线程持有Handler, 在后台线程结束前,Handler一直被持有, 所有Activity也被持有.


## 解决方案

---

1. 方法一：通过程序逻辑来进行保护, 对于下面不同的场景采用不同的方法:

    - 在关闭Activity的时候结束后台线程。线程停掉了，就相当于切断了Handler和外部连接的线，Activity自然会在合适的时候被回收。
    - 如果Handler是被delay的Message持有了引用，那么在Activity结束前使用相应的Handler的removeCallbacks()方法，把消息对象从消息队列移除就行了。

1. 将Handler声明为静态类。

    静态类不持有外部类的对象，所以Activity可以随意被回收。但是这时候需要将需要的Activity传进来, 可以采用WeakReference.

    代码如下：

    ```
    static class MyHandler extends Handler {
        WeakReference<Activity > mActivityReference;

        MyHandler(Activity activity) {
            mActivityReference= new WeakReference<Activity>(activity);
        }

        @Override
        public void handleMessage(Message msg) {
            final Activity activity = mActivityReference.get();
            if (activity != null) {
                mImageView.setImageBitmap(mBitmap);
            }
        }
    }
    ```


## Reference

---

1. [Android中使用Handler造成内存泄露的分析和解决](http://www.linuxidc.com/Linux/2013-12/94065.htm)