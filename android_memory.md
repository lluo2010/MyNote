浅谈java反射机制
http://zhan.renren.com/h5/entry/3602888498001413964

应用内存优化之OnLowMemory&OnTrimMemory
http://www.cnblogs.com/xiajf/p/3993599.html

Android开发:性能最佳实践-管理应用内存
http://www.pocketdigi.com/20140607/1334.html

优化Android应用内存的若干方法
http://www.open-open.com/lib/view/open1392013992317.html

Android Binder机制（超级详尽）
http://blog.csdn.net/coding_glacier/article/details/7520199

1.OnLowMemory
	This is called when the overall system is running low on memory, and would like actively running process to try to tighten their belt. While the exact point at which this will be called is not defined, generally it will happen around the time all background process have been killed, that is before reaching the point of killing processes hosting service and foreground UI that we would like to avoid killing.

Applications that want to be nice can implement this method to release any caches or other unnecessary resources they may be holding on to. The system will perform a gc for you after returning from this method.

2.public void onTrimMemory (int level)
Added in API level 14
Called when the operating system has determined that it is a good time for a process to trim unneeded memory from its process. This will happen for example when it goes in the background and there is not enough memory to keep as many background processes running as desired. You should never compare to exact values of the level, since new intermediate values may be added -- you will typically want to compare if the value is greater or equal to a level you are interested in.

To retrieve the processes current trim level at any point, you can use ActivityManager.getMyMemoryState(RunningAppProcessInfo).

Parameters
level	The context of the trim, giving a hint of the amount of trimming the application may like to perform. May be TRIM_MEMORY_COMPLETE, TRIM_MEMORY_MODERATE, TRIM_MEMORY_BACKGROUND, TRIM_MEMORY_UI_HIDDEN, TRIM_MEMORY_RUNNING_CRITICAL, TRIM_MEMORY_RUNNING_


OnLowMemory()和OnTrimMemory()的比较
1，OnLowMemory被回调时，已没有后台进程；而onTrimMemory被回调时，还有后台进程。
2，OnLowMemory是在最后1个后台进程被杀时调用，一般情况是low memory killer 杀进程后触发；而OnTrimMemory的触发更频繁，每次计算进程优先级时，只要满足条件，都会触发。
3，通过1键清算后，OnLowMemory不会被触发，而OnTrimMemory会被触发1次。


Context.registerComponentCallbacks 

除了上述系统提供的API，还可以自己实现ComponentCallbacks，通过API注册，这样也能得到OnLowMemory回调。例如：
public static class MyCallback implements ComponentCallbacks {
@Override
public void onConfigurationChanged(Configuration arg) {
}
