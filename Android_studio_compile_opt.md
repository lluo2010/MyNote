# 加速Android studio编译速度



## 1. 开启并行编译
在项目根目录下面的 gradle.properties中设置:  

```
org.gradle.parallel=true
```
 
## 2. 开启编译守护进程
该进程在第一次启动后回一直存在，当你进行二次编译的时候，可以重用该进程。同样是在gradle.properties中设置。  

```
org.gradle.daemon=true
```

## 3. 加大可用编译内存
在gradle.properties中添加如下配置:

```
org.gradle.jvmargs=-Xms256m -Xmx1024m
```

## 4. 减少APK文件大小
在编译的时候，我们可能会有很多资源并没有用到，此时就可以通过shrinkResources来优化我们的资源文件，除去那些不必要的资源。
可以build.gradle里做如下配置:

```
android {
    release {
            minifyEnabled true
            shrinkResources true
            ...
    }
}
```
如果我们需要查看该命令帮我们减少了多少无用的资源，我们也可以通过运行shrinkReleaseResources命令来查看log.
某些情况下，一些资源是需要通过动态加载的方式载入的，这时候我也需要像proguard一样对我们的资源进行keep操作。方法就是在res/raw/下建立一个keep.xml文件，通过如下方式 keep 资源：
res/raw/keep.xml:

```
    <?xml version="1.0" encoding="utf-8"?>
    <resources xmlns:tools="http://schemas.android.com/tools"
    tools:keep="@layout/l_used*_c,@layout/l_used_a,@layout/l_used_b*"
    />
```



## Reference
1. [Gradle 完整指南（Android）](http://www.jianshu.com/p/9df3c3b6067a)