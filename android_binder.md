
# Android Binder note


## 什么是Binder

---

Binder是一种进程间通信机制，它是一种类似于COM和CORBA分布式组件架构，通俗一点，其实是提供远程过程调用（RPC）功能。从英文字面上意思看，Binder具有粘结剂的意思，

那么它把什么东西粘结在一起呢？在Android系统的Binder机制中，由一系统组件组成，分别是Client、Server、Service Manager和Binder驱动程序，其中Client、Server和Service Manager运行在用户空间，Binder驱动程序运行内核空间。Binder就是一种把这四个组件粘合在一起的粘结剂了，其中，核心组件便是Binder驱动程序了，Service Manager提供了辅助管理的功能，Client和Server正是在Binder驱动和Service Manager提供的基础设施上，进行Client-Server之间的通信。Service Manager和Binder驱动已经在Android平台中实现好，开发者只要按照规范实现自己的Client和Server组件就可以了。



## 在Android框架中的位置

---

Android整体框架如下图所示:

![Android整体框架图](http://upload-images.jianshu.io/upload_images/2628445-566420f14d7f4c79.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从下往上依次为:

- 内核层：Linux 内核和各类硬件设备的驱动，这里需要注意的是，Binder IPC 驱动也是在这一层实现，比较特殊
- 硬件抽象层：封装「内核层」硬件驱动，提供可供「系统服务层」调用的统一硬件接口
- 系统服务层：提供核心服务，并且提供可供「应用程序框架层」调用的接口
- Binder IPC 层：作为「系统服务层」与「应用程序框架层」的 IPC 桥梁，互相传递接口调用的数据，实现跨进层的通讯
- 应用程序框架层：这一层可以理解为 Android SDK，提供四大组件，View 绘制体系等平时开发中用到的基础部件

每一个系统服务在应用层序框架层都有一个 Manager 与之对应，方便开发者调用其相关的功能，具体关系大致如下:

![应用程序框架层和系统务车层关系图](http://upload-images.jianshu.io/upload_images/2628445-69da14ad31916a03.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


** IPC的方式有很多种，例如 socket、共享内存、管道、消息队列等等，Android这里使用的是Binder. **


小结:

- Android 从下而上分了内核层、硬件抽象层、系统服务层、Binder IPC 层、应用程序框架层
- Android 中「应用程序框架层」以 SDK 的形式开放给开发者使用，「系统服务层」中的核心服务随系统启动而运行，通过应用层序框架层提供的 Manager 实时为应用程序提供服务调用。系统服务层中每一个服务运行在自己独立的进程空间中，应用程序框架层中的 Manager 通过 Binder IPC 的方式调用系统服务层中的服务。



## Binder IPC 的架构

---

BinderIPC结构如下图所示:

![Binder IPC架构图](http://upload-images.jianshu.io/upload_images/2628445-8e398eb2bcd029ba.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Binder IPC 属于 C/S 结构，Client 部分是用户代码，用户代码最终会调用 Binder Driver 的 transact 接口，Binder Driver 会调用 Server，这里的 Server 与 service 不同，可以理解为 Service 中 onBind 返回的 Binder 对象，请注意区分下:

- Client：用户需要实现的代码, 也可以是AIDL自动生成的接口类.
- Binder Driver：在内核层实现的 Driver, 用户不需要实现.
- Server：这个 Server 就是 Service 中 onBind 返回的 IBinder 对象.

蓝色部分是系统去实现了,绿色的色块部分都是属于用户需要实现的部分.Server端接收到Client的调用请求后, 会开启一个线程池去处理客户端调用。 优化服务端是使用线程池处理请求的,所以我们需要自己去处理线程安全的事情.

另外注意上面Client或者Server中的蓝色部分, 它们是或的关系, 对于client, 可以使用remote.transact, 也可以使用aidl, 它的内部是调用proxy.transact.

对于调用 Binder Driver 中的 transact 接口，客户端可以手动调用，也可以通过 AIDL 的方式生成的代理类来调用，服务端可以继承 Binder 对象，也可以继承 AIDL 生成的接口类的 Stub 对象。


小结:

- Binder IPC 属于 C/S 架构，包括 Client、Driver、Server 三个部分
- Client 可以手动调用 Driver 的 transact 接口，也可以通过 AIDL 生成的 Proxy 调用.
- Server 中会启动一个「线程池」来处理 Client 的调用请求，处理完成后将结果返回给 Driver，Driver 再返回给 Client


## 手动实现 Binder IPC

---

### Server实现

需要做下面三件事:

1. 首先需要定义需要提供的接口, 比如下面, 接口名称为IReporter.
1. 然后实现该接口, 同时从Binder派生, 重写Binder.onTransact()方法.
1. 通过Service的onBind()返回该接口.

相应的代码如下:

```
public interface IReporter {

    int report(String values, int type);
}
```

```
public class BinderService extends Service {

    public static final int REPORT_CODE = 0;

    public interface IReporter {
        int report(String values, int type);
    }

    public final class Reporter extends Binder implements IReporter {
        @Override
        protected boolean onTransact(int code, Parcel data, Parcel reply, int flags) throws RemoteException {
            switch (code) {
            case REPORT_CODE:
                data.enforceInterface("reporter");
                String values = data.readString();
                Log.i("IReporter", "data is '" + values + "'");
                int type = data.readInt();
                int result = report(values, type);
                reply.writeInterfaceToken("reporter");
                reply.writeInt(result);
                return true;
            }
            return super.onTransact(code, data, reply, flags);
        }

        @Override
        public int report(String values, int type) {
            return type;
        }
    }

    private Reporter mReporter;

    public BinderService() {
        mReporter = new Reporter();
    }

    @Override
    public IBinder onBind(Intent intent) {
        return mReporter;
    }
}

```

从上面我们发现Reporter从Binder派生并实现了onTransact(), 在onTransact()里面,通过解包data,获取相应的参数,然后调用Reporter的方法report()将参数传进去, 获取的结果又重新组包将返回值放入reply. super.onTransact应该是将结果返回给client端.


### Driver实现

该部分已经被Binder类给封装了，暴露给开发者的已经是很简单的使用方式了，即继承 Binder，实现 onTransact 即可。


### Client实现

Client端需要做下面两件事:

- Client端通过ServiceConnection建立连接获取IBinder接口.
- 将信息组包,调用mReporterBind.transact()获取结果,然后解包后去结果.

相应代码如下:

```
private IBinder mReporterBind;

private class BindConnection implements ServiceConnection {

    @Override
    public void onServiceConnected(ComponentName name, IBinder service) {
        mReporterBind = service;
    }

    @Override
    public void onServiceDisconnected(ComponentName name) {
        mReporterBind = null;
    }
}

...

@Override
protected void onCreate(Bundle savedInstanceState) {

    ...

    intent = new Intent(this, BinderService.class);
    bindService(intent, new BindConnection(), BIND_AUTO_CREATE);
}
```

```
Parcel data = Parcel.obtain();
Parcel reply = Parcel.obtain();
data.writeInterfaceToken("reporter");
data.writeString("this is a test string.");
data.writeInt(type);

mReporterBind.transact(BinderService.REPORT_CODE, data, reply, 0);
reply.enforceInterface("reporter");
int result = reply.readInt();

data.recycle();
reply.recycle();
```

从上面看调用Bind.transact(xxx)前先将参数组包,transact第一个参数REPORT_CODE是用来表示请求的,transact函数返回后,对reply解包,后去结果.


小结:

- 一切复杂的逻辑均已经被封装在实现了 IBinder 接口的 Binder 类中
- Activity 中通过 bindService 拿到 Binder Driver 中的 mRemote 对象（IBinder 的实例），然后「组包」，然后「调用 transact 接口」按序发送数据包
- Service 中继承 Binder 类，「重载 onTransact 函数」，实现参数的「解包」，发送返回包等，在 onBind 中返回具体的实现类 如上文中的 Reporter

总的说来就是Client组包，调用 transact 发送数据，Server 接到调用，解包，返回，下面使用 AIDL 的流程本质也是一样。


## 使用 AIDL 实现 Binder IPC

---

需要做的事情如下:

### 定义aidl接口

比如下面为IReporter.aidl:

```
package com.android.binder;

interface IReporter {

    int report(String values, int type);
}
```

### Server端

1. 实现一个Service.
1. 从IReporter.Stub派生一个Binder, 并在相应的接口函数里实现自己的逻辑.
1. onBind()里返回相应的Binder.

对应的代码如下:

```
public class AidlService extends Service {

    public static final class Reporter extends IReporter.Stub {

        @Override
        public int report(String values, int type) throws RemoteException {
            return type;
        }
    }

    private Reporter mReporter;

    public AidlService() {
        mReporter = new Reporter();
    }

    @Override
    public IBinder onBind(Intent intent) {
        return mReporter;
    }
}
```

自动生成的IReporter接口如下, 其实就是做了我们手工实现IPC接口里Server端做的事情, 重写了onTransact(),并进行解包,调用相应函数等:

```
public interface IReporter extends android.os.IInterface
{

    public static abstract class Stub extends android.os.Binder implements com.android.binder.IReporter {
        ...

        @Override
        public boolean onTransact(int code, android.os.Parcel data, android.os.Parcel reply, int flags) throws android.os.RemoteException
        {
            switch (code)
            {
                case INTERFACE_TRANSACTION:
                {
                    reply.writeString(DESCRIPTOR);
                    return true;
                }
                case TRANSACTION_report:
                {
                    data.enforceInterface(DESCRIPTOR);
                    java.lang.String _arg0;
                    _arg0 = data.readString();
                    int _arg1;
                    _arg1 = data.readInt();
                    int _result = this.report(_arg0, _arg1);
                    reply.writeNoException();
                    reply.writeInt(_result);
                    return true;
                }
            }
            return super.onTransact(code, data, reply, flags);
        }
    }
...

}
```


### Driver

与上文一致，还是 Binder 的内部封装, 用户无需实现.


### Client实现

通过ServiceConnection建立连接, 获取Binder.

相应代码如下:

```
private IReporter mReporterAidl;

private class AidlConnection implements ServiceConnection {

    @Override
    public void onServiceConnected(ComponentName name, IBinder service) {
        mReporterAidl = IReporter.Stub.asInterface(service);
    }

    @Override
    public void onServiceDisconnected(ComponentName name) {
        mReporterAidl = null;
    }
}

...

@Override
protected void onCreate(Bundle savedInstanceState) {

    ...

    Intent intent = new Intent(this, AidlService.class);
    bindService(intent, new AidlConnection(), BIND_AUTO_CREATE);
}
```
上面的mReporterAidl通过"IReporter.Stub.asInterface(service)"获取. asInterface实现如下:

```
public static com.android.binder.IReporter asInterface(android.os.IBinder obj)
{
    if ((obj==null)) {
        return null;
    }
    android.os.IInterface iin = obj.queryLocalInterface(DESCRIPTOR);
    if (((iin!=null)&&(iin instanceof com.android.binder.IReporter))) {
        return ((com.android.binder.IReporter)iin);
    }
    return new com.android.binder.IReporter.Stub.Proxy(obj);
}
```
如果不是本地的, binder是通过Stub.proxy获取的.

Proxy的实现如下,其实也是自动实现了原来Client端手工实现的功能, 将参数组包, 调用binder.transact(xx)函数,然后再解包获取返回参数.

```
private static class Proxy implements com.android.binder.IReporter
{
    private android.os.IBinder mRemote;
    Proxy(android.os.IBinder remote)
    {
        mRemote = remote;
    }
    @Override public android.os.IBinder asBinder()
    {
        return mRemote;
    }
    public java.lang.String getInterfaceDescriptor()
    {
        return DESCRIPTOR;
    }
    @Override public int report(java.lang.String values, int type) throws android.os.RemoteException
    {
        android.os.Parcel _data = android.os.Parcel.obtain();
        android.os.Parcel _reply = android.os.Parcel.obtain();
        int _result;
        try {
            _data.writeInterfaceToken(DESCRIPTOR);
            _data.writeString(values);
            _data.writeInt(type);
            mRemote.transact(Stub.TRANSACTION_report, _data, _reply, 0);
            _reply.readException();
            _result = _reply.readInt();
        }
        finally {
            _reply.recycle();
            _data.recycle();
        }
        return _result;
    }
}
```

小结:

1. AIDL 自动生成了 Stub 类
1. 在 Service 端继承 Stub 类，Stub 类中实现了 onTransact 方法实现了「解包」的功能.
1. 在 Client 端使用 Stub 类的 Proxy 对象，该对象实现了「组包」并且调用 transact 的功能.
1. 有了 AIDL 之后，IReporter 接口就变得有意义了，Client 调用接口，Server 端实现接口，一切「组包」、「解包」的逻辑封装在了 Stub 类中.



## Reference

---

1. [轻松理解 Android Binder，只需要读这一篇](http://www.jianshu.com/p/bdef9e3178c9)