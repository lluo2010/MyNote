# 优化之电量优化

## 网络请求过程和电量相关

发起一个网络请求时设备的状态过程:
![过程](http://hukai.me/images/android_perf_2_network_request_mode.png)

上面过程蜂窝网络(mobile)下的电量消耗差异如下图所示:
![耗电情况](http://hukai.me/images/android_perf_2_battery_drain_mode.png)

从图示中可以看到，激活瞬间，发送数据的瞬间，接收数据的瞬间都有很大的电量消耗.



## 获取电量消耗

Battery Historian是Android 5.0开始引入的新API。通过下面的指令，可以得到设备上的电量消耗信息：

```
$ adb shell dumpsys batterystats > xxx.txt  //得到整个设备的电量消耗信息
$ adb shell dumpsys batterystats > com.package.name > xxx.txt //得到指定app相关的电量消耗信息
```

得到了原始的电量消耗数据之后，我们需要通过Google编写的一个python脚本把数据信息转换成可读性更好的html文件:

```
$ python historian.py xxx.txt > xxx.html
```

## 获取手机的当前充电状态

可以通过下面的代码来获取手机的当前充电状态：

```
// It is very easy to subscribe to changes to the battery state, but you can get the current
// state by simply passing null in as your receiver.  Nifty, isn't that?
IntentFilter filter = new IntentFilter(Intent.ACTION_BATTERY_CHANGED);
Intent batteryStatus = this.registerReceiver(null, filter);
int chargePlug = batteryStatus.getIntExtra(BatteryManager.EXTRA_PLUGGED, -1);
boolean acCharge = (chargePlug == BatteryManager.BATTERY_PLUGGED_AC);
if (acCharge) {
    Log.v(LOG_TAG,“The phone is charging!”);
}

```

我们可以判断只有当前手机为AC充电状态时 才去执行一些非常耗电的操作。

## Wakelock和耗电(Wakelock and Battery Drain)
不恰当的使用WakeLock会导致严重错误。例如网络请求的数据返回时间不确定，导致本来只需要10s的事情一直等待了1个小时，这样会使得电量白白浪费了。这也是为何使用带超时参数的wakelock.acquice()方法是很关键的。

也可以使用AlarmManager或者JobScheduler API.

## 网络和耗电(Network and Battery Drain)

1. 为了减少电量的消耗，在蜂窝移动网络下，最好做到批量执行网络请求，尽量避免频繁的间隔网络请求。
1. 另外WiFi连接下，网络传输的电量消耗要比移动网络少很多，应该尽量减少移动网络下的数据传输，多在WiFi环境下传输数据。
1. 避免频繁连接.
1. 避免过多的无线信号(radio on)引起的电量消耗。


## 总结
### 电量和哪些相关
1. 屏幕亮暗相关
1. 设备awake,sleep的切换,尤其是唤醒.
1. CPU运行相关
1. 网络

### 如何优化
1. 针对屏幕亮暗以及CPU:
	+ 我们应该尽量减少唤醒屏幕的次数与持续的时间.
	+ 使用WakeLock来处理唤醒的问题，能够正确执行唤醒操作并根据设定及时关闭操作进入睡眠状态。
1. 针对网络: 
	+ 批处理:触发网络请求的操作，每次都会保持无线信号持续一段时间，我们可以把零散的网络请求打包进行一次操作，避免过多的无线信号引起的电量消耗。批处理的手段包括提前和推迟. 关于网络请求引起无线信号的电量消耗，还可以参考这里http://hukai.me/android-training-course-in-chinese/connectivity/efficient-downloads/efficient-network-access.html
	+ 缓存
	+ 压缩
	+ 可以用Wifi的,不用移动网络,推迟到wifi进行.
	+ 避免频繁连接
	+ 避免过多的无线信号(radio on)引起的电量消耗。
1. 某些非必须马上执行的操作，例如上传歌曲，图片处理等，可以等到设备处于充电状态或者电量充足的时候才进行。这个可以使用JobScheduler.

*** 也就说, 我们通过尽量减少屏幕亮屏时间,使用缓存,压缩, 对网络请求进行提前或者推迟,批量处理,避免频繁连接断开,尽量在wifi下使用网络,可以在充电执行的采用JobScheduler尽量在充电情况下执行等手段达到省电.
需要注意的是Wakelock的使用要合理. ***

### 关于省电需要注意的
1. 合理利用WakeLock,不要出现Wakelock长期不释放的场景.

## 关于延迟任务

很多场景下的可以采用延迟任务达到优化的目的, 通常有下面三种方式达到优化：

1. AlarmManager: 使用AlarmManager设置定时任务，可以选择精确的间隔时间，也可以选择非精确时间作为参数。除非程序有很强烈的需要使用精确的定时唤醒，否者一定要避免使用它，我们应该尽量使用非精确的方式。
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