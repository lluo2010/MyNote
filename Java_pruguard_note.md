# Java progurd混淆

## 简介
ProGuard是一款免费的Java类文件压缩器、优化器和混淆器。它能发现并删除无用类、字段（field）、方法和属性值（attribut）。它也能优化字节码并删除无用的指令。它使用简单无意义的名字来重命名你的类名、字段名和方法名。经过以上操作的jar文件会变得更小，并很难进行逆向工程。

在Android应用程序也可以使用ProGuard来进行混洗打包，大大的优化Apk包的大小。但是注意ProGuard对文件路径的名有讲究，不支持括号，也不支持空格。在混淆过后，可以在工程目录的proguard中的mapping.txt看到混淆后的类名，方法名，变量名和混淆前的类名，方法名，变量名。

在使用Eclipse新建一个工程，都会在工程目录下生产配置project.properties和proguard-project.txt.

在Android studio中,配置文件在proguard-android.txt和proguard-rules.pro:

1. proguard-android.txt 是sdk中groguard 默认的文件，具体地址在：/opt/sdk/tools/proguard/proguard-android.txt.
1. proguard-rules.pro是android studio中专用的proguard配置文件，其实只是后缀名不同，与Eclipse中的 proguard-project.txt 是一样的，配置规则相同.

所以我们自己定义的可以放在proguard-rules.pro里.


我们在使用一些第三方的库,或者用到反射的代码时,需要注意保持某些类,接口等不被混淆.

所以,当混淆开启后,我们需要做的工作是指定哪些地方是不需要混淆,即不要改变名称,或者即使没有使用,也不要移除.

## Proguard主要功能
主要功能有三个:
缩减代码、优化代码、混淆代码。

可以在配置文件里配置不启用此功能,下面为如何指定不启用:

1. 不启用缩减代码  
-dontshrink
1. 不启用代码优化  
  -dontoptimize
1. 不启用混淆  
  -dontobfuscate 


## 有些代码是不允许混淆的
缺省情况下，proguard 会混淆所有代码，但是下面几种情况是不能改变Java元素的名称，否则代码就不能正常工作:

1. 用到反射的地方。
1. 我们代码依赖于系统的接口，比如被系统代码调用的回调方法，这种情况最复杂。
1. Java元素名称是在配置文件中配置好的。

简单的说其实就是如果别的地方需要用到它的名称的话,就不能混淆.否则混淆后就不知道它是什么名称了.


## 关于keep的一些知识
我们知道,ProGuard默认情况下会对类、字段（field）、方法和属性值（attribute)名称进行混淆,如果发现没有被使用,还会删除.

通过keep指定规则的就可以指定某些地方不去做名称混淆或者删除.

下面对keep的行为进行分类,列分为是只保持名称不改,还是保持名称不改也保证不删除的.

Keep	|不改名字不删除|不改名字
--|:--|:--
Classes and class members|-keep	|-keepnames
Class members only|-keepclassmembers|-keepclassmembernames
Classes and class members, if class members present|-keepclasseswithmembers|-keepclasseswithmembernames

*** 当我们不确定要用哪一个时, 就使用-keep, 它保证类及其成员的名称不会混淆,也保证即使没有使用也不会被移除. ***

具体详情如下如下:

### 关于class_specification
下面涉及到"class_specification",关于class_specification的官方描述如下:
>A class specification is a template of classes and class members (fields and methods). It is used in the various -keep options and in the -assumenosideeffects option. The corresponding option is only applied to classes and class members that match the template.
具体定义如下:

```
[@annotationtype] [[!]public|final|abstract|@ ...] [!]interface|class|enum classname
    [extends|implements [@annotationtype] classname]
[{
    [@annotationtype] [[!]public|private|protected|static|volatile|transient ...] <fields> |
                                                                      (fieldtype fieldname);
    [@annotationtype] [[!]public|private|protected|static|synchronized|native|abstract|strictfp ...] <methods> |
                                                                                           <init>(argumenttype,...) |
                                                                                           classname(argumenttype,...) |
                                                                                           (returntype methodname(argumenttype,...));
    [@annotationtype] [[!]public|private|protected|static ... ] *;
    ...
}]
```
注意: 上面[]表示可选.

这部分看起来内容比较多,可以先跳过.

可以理解为是keep针对的范围. 比如下面例子,keep后的都是class_specification.

```
-keep class android.support.v4.** { *; }
-keep interface android.support.v4.app.** { *; }
-keep public class * extends android.support.v4.**
-keep public class * extends android.app.Fragment
-keep public class * extends android.view.View {
    public <init>(android.content.Context);
    ...
    public <init>(android.content.Context, android.util.AttributeSet, int);
    public void set*(...);
}
```
上面例子中keep后class和interface的区别:
>The class keyword refers to any interface or class. The interface keyword restricts matches to interface classes.

class包括任何的类和接口,interface只表示接口.

另外上面的<init>表示构造函数, 还可以有下面:

名称|描述
--|--
\<init>|matches any constructor.
\<fields>|matches any field.
\<methods>|matches any method.
*|matches any field or method.

更多关于class_specification可以查阅http://proguard.sourceforge.net/manual/usage.html#classspecification


### -keep [,modifier,...] class_specification
>Specifies classes and class members (fields and methods) to be preserved as entry points to your code. 

对指定的类以及类的成员(字段,方法)都有效,保证名称不被更改,也不被移除(即使没有被使用).

例子:

```
-keep public class * extends com.umeng.**
-keep class com.umeng.** {*; }
```

### -keepnames class_specification
>Specifies classes and class members whose names are to be preserved, if they aren't removed in the shrinking phase. For example, you may want to keep all class names of classes that implement the Serializable interface, so that the processed code remains compatible with any originally serialized classes. Classes that aren't used at all can still be removed.

和keep差不多,唯一区别是如果没有被使用,可以被proguad自动移除掉.

### -keepclassmembers [,modifier,...] class_specification
>Specifies class members to be preserved, if their classes are preserved as well.

对指定的类的成员进行保护(不改名,不移除).
比如下面是对实现Serializable要求指定的4个和序列化相关的函数不能被移除也不改名:

```
-keepclassmembers class * implements java.io.Serializable {
    private static final java.io.ObjectStreamField[] serialPersistentFields;
    private void writeObject(java.io.ObjectOutputStream);
    private void readObject(java.io.ObjectInputStream);
    java.lang.Object writeReplace();
    java.lang.Object readResolve();
}
```

下面是对指定webview里的所有public的成员进行保护:

```
-keepclassmembers class fqcn.of.javascript.interface.for.webview {  
   public *;  
}  
```

下面资源类变量需要保留:

  ```
  -keepclassmembers class com.cheweishi.android.R$* {
      public static <fields>;
  }
  ```

### -keepclassmembernames class_specification
>Specifies class members whose names are to be preserved, if they aren't removed in the shrinking phase.
假如类的成员没有被移除,保证它的名称不被更改. 该项不保证文件不被删除.


### -keepattributes [attribute_filter]
>Specifies any optional attributes to be preserved. The attributes can be specified with one or more -keepattributes directives.

保护指定属性不被更改. 关于Optional attributes可以查阅http://proguard.sourceforge.net/manual/attributes.html.


## -keep的用法区别

1. 保留某个类名不被混淆  
  -keep public class com.ebt.app.common.bean.Customer
2. 保留类及其所有成员不被混淆  

    ```
      -keep public class com.ebt.app.common.bean.Customer { *;}
    ```

3. 只保留类名及其部分成员不被混淆

    ```
    -keep public class com.ebt.app.common.bean.Customer { 
        static final<fields>;
        private void get*();

    }
    ```

4. 保留包路径下所有的类及其属性和方法

    ```
    -keep class cn.jpush.** { *; }
    ```
5. 保留指定接口的属性和方法

    ```
    -keep interface android.support.v4.app.** { *; }
    ```

## 例子
用来测试的两个类如下:

```
public class TestObj1 {
    private static final String TAG = "TestObj1";
    private int value1;
    public int value2;

    public void show(int number){
        value1 = 1;
        value2 = 2;
        number = 1;
        privateMethod();
        Log.i(TAG,"TestObj1 value:"+value1+",value2:"+value2);
    }


    private void privateMethod(){
        Log.i(TAG, "privageMethod");
    }

}

public class TestObj2 {
    private static final String TAG = "TestObj2";
    private int value1;
    public int value2;

    public void show(int number){
        value1 = 1;
        value2 = 2;
        number = 1;
        privateMethod();
        Log.i(TAG,"TestObj2 value:"+value1+",value2:"+value2);
    }


    private void privateMethod(){
        Log.i(TAG, "privageMethod");
    }
}
```
两个测试类TestObj1和TestObj2除了类名外其他都一样, 下面的测试是要对TestObj1进行保护,然后和TestObj2对比看区别.

代码编译后会产生mapping文件,通过mapping文件可以看到哪些被混淆了,而且可以看到混淆前后的映射关系.


### -keep+{*;}对类名和成员变量(方法和字段)保护
混淆规则:

```
-keep public class com.example.lluo.optimizetest.TestObj1 {*;}
```

结果: 类名和成员变量(方法和字段)都不混淆

具体的混淆结果如下:

```
com.example.lluo.optimizetest.TestObj1 -> com.example.lluo.optimizetest.TestObj1:
    java.lang.String TAG -> TAG
    int value1 -> value1
    int value2 -> value2
    9:9:void <init>() -> <init>
    15:20:void show(int) -> show
    24:25:void privateMethod() -> privateMethod
com.example.lluo.optimizetest.TestObj2 -> com.example.lluo.optimizetest.a:
    int value1 -> b
    int value2 -> a
    9:9:void <init>() -> <init>
    15:20:void show(int) -> a
    24:25:void privateMethod() -> a
```

### 使用-keep+public \<methods>对类名和public的方法进行保护

混淆规则:

```
-keep public class com.example.lluo.optimizetest.TestObj1{
    public <methods>;
}
```

结果: 类名和public的方法不混淆, 其他都混淆.

具体的混淆结果如下,私有成员函数privateMethod()被混淆了,但是公有的show()没有混淆:

```
com.example.lluo.optimizetest.TestObj1 -> com.example.lluo.optimizetest.TestObj1:
    int value1 -> b
    int value2 -> a
    9:9:void <init>() -> <init>
    15:20:void show(int) -> show
    24:25:void privateMethod() -> a
com.example.lluo.optimizetest.TestObj2 -> com.example.lluo.optimizetest.a:
    int value1 -> b
    int value2 -> a
    9:9:void <init>() -> <init>
    15:20:void show(int) -> a
    24:25:void privateMethod() -> a
```

### -keep只对类名保护
混淆规则:

```
-keep public class com.example.lluo.optimizetest.TestObj1
```
结果: 只有类名不混淆,所有成员变量(方法和字段)都混淆了.

具体的混淆结果如下,除了类名没有混淆,其他都混淆了:

```
com.example.lluo.optimizetest.TestObj1 -> com.example.lluo.optimizetest.TestObj1:
    int value1 -> b
    int value2 -> a
    9:9:void <init>() -> <init>
    15:20:void show(int) -> a
    24:25:void privateMethod() -> a
com.example.lluo.optimizetest.TestObj2 -> com.example.lluo.optimizetest.a:
    int value1 -> b
    int value2 -> a
    9:9:void <init>() -> <init>
    15:20:void show(int) -> a
    24:25:void privateMethod() -> a

```


### -keepclassmembers+*;保护所有成员变量
混淆规则:

```
-keepclassmembers public class com.example.lluo.optimizetest.TestObj1{
    *;
}
```

结果: 类名混淆了, 所有的成员变量(方法或者字段).

具体的混淆结果如下:

```
com.example.lluo.optimizetest.TestObj1 -> com.example.lluo.optimizetest.a:
    java.lang.String TAG -> TAG
    int value1 -> value1
    int value2 -> value2
    9:9:void <init>() -> <init>
    15:20:void show(int) -> show
    24:25:void privateMethod() -> privateMethod
com.example.lluo.optimizetest.TestObj2 -> com.example.lluo.optimizetest.b:
    int value1 -> b
    int value2 -> a
    9:9:void <init>() -> <init>
    15:20:void show(int) -> a
    24:25:void privateMethod() -> a
```

### -keepclassmembers+public *;保护public的成员变量
混淆规则:

```
-keepclassmembers public class com.example.lluo.optimizetest.TestObj1{
public *;
}
```
结果: 类名混淆了, public的成员变量(方法或者字段)不混淆,其他的都混淆了(比如privateMethod).

具体的混淆结果如下:

```
com.example.lluo.optimizetest.TestObj1 -> com.example.lluo.optimizetest.a:
    int value1 -> a
    int value2 -> value2
    9:9:void <init>() -> <init>
    15:20:void show(int) -> show
    24:25:void privateMethod() -> a
com.example.lluo.optimizetest.TestObj2 -> com.example.lluo.optimizetest.b:
    int value1 -> b
    int value2 -> a
    9:9:void <init>() -> <init>
    15:20:void show(int) -> a
    24:25:void privateMethod() -> a
```


### -keepclassmembers+\<methods> 保护方法
混淆规则:

```
-keepclassmembers public class com.example.lluo.optimizetest.TestObj1{
    <methods>;
}
```
结果: 类名会混淆, 类内字段也会混淆, 方法不会混淆. 当然也可以使用\<fields>指定字段不被混淆,同时也可以指定访问权限.

具体的混淆结果如下:

```
com.example.lluo.optimizetest.TestObj1 -> com.example.lluo.optimizetest.a:
    int value1 -> b
    int value2 -> a
    9:9:void <init>() -> <init>
    15:20:void show(int) -> show
    24:25:void privateMethod() -> privateMethod
com.example.lluo.optimizetest.TestObj2 -> com.example.lluo.optimizetest.b:
    int value1 -> b
    int value2 -> a
    9:9:void <init>() -> <init>
    15:20:void show(int) -> a
    24:25:void privateMethod() -> a
```

### 总结:
1. -keep针对类名和类内部的成员(方法和字段),-keepclassmembers只这对类内部的成员(方法和字段).
1. -keep如果不使用{}, 则只针对类的名称做保护.
1. 不管是-keep还是-keepclasseswithmembers,都可以通过指定一些条件(比如访问权限,比如\<method>,\<init>,\<fields>类别)对保护的范围进行限制.

## References
1. [Proguard](http://proguard.sourceforge.net/index.html#/manual/examples.html)
1. [Overview of keep Options](http://proguard.sourceforge.net/manual/usage.html#keepoverview)
1. [JAVA之代码混淆proguard基础](http://blog.csdn.net/fastthinking/article/details/39155733)


//----------

-dontwar

-keepclassmembers 
-keepattributes *Annotation*
-keep class
-keep public interface com.umeng.socialize.sensor.**

-keep class com.appkefu.** { *;}
-keep class com.idea.fifaalarmclock.entity.***
-keep public class com.umeng.fb.ui.ThreadView {
}

