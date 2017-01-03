# Dagger2 study note



## 简介

---

依赖注入：就是目标类（目标类需要进行依赖初始化的类，下面都会用目标类一词来指代）中所依赖的其他的类的初始化过程，不是通过手动编码的方式创建，而是通过技术手段可以把其他的类的已经初始化好的实例自动注入到目标类中。

Dagger2由谷歌开发, 起源于Dagger，是一款基于Java注解来实现的完全在编译阶段完成依赖注入的开源库，主要用于模块间解耦、提高代码的健壮性和可维护性。Dagger2在编译阶段通过apt利用Java注解自动生成Java代码，然后结合手写的代码来自动帮我们完成依赖注入的工作。

Jake Wharton 在对 Dagger 的介绍中指出，Dagger 即 DAG-er，这里的 DAG 即数据结构中的 DAG——有向无环图(Directed Acyclic Graph)。也就是说，Dagger 是一个基于有向无环图结构的依赖注入库，因此Dagger的使用过程中不能出现循环依赖。

Android开发从一开始的MVC框架，到MVP，到MVVM，不断变化。现在MVVM的data-binding还在实验阶段，传统的MVC框架Activity内部可能包含大量的代码，难以维护，现在主流的架构还是使用MVP（Model + View + Presenter）的方式。但是 MVP 框架也有可能在Presenter中集中大量的代码，引入DI框架Dagger2 可以实现 Presenter 与 Activity 之间的解耦，Presenter和其它业务逻辑之间的解耦，提高模块化和可维护性。


## 配置

---

### Maven

```
<dependencies>
  <dependency>
    <groupId>com.google.dagger</groupId>
    <artifactId>dagger</artifactId>
    <version>2.x</version>
  </dependency>
  <dependency>
    <groupId>com.google.dagger</groupId>
    <artifactId>dagger-compiler</artifactId>
    <version>2.x</version>
    <optional>true</optional>
  </dependency>
</dependencies>
```

### Java Gradle

```
// Add plugin https://plugins.gradle.org/plugin/net.ltgt.apt
plugins {
  id "net.ltgt.apt" version "0.5"
}

// Add Dagger dependencies
dependencies {
  compile 'com.google.dagger:dagger:2.x'
  apt 'com.google.dagger:dagger-compiler:2.x'
}
```

### Android Gradle

```
// Add Dagger dependencies
dependencies {
  compile 'com.google.dagger:dagger:2.x'
  annotationProcessor 'com.google.dagger:dagger-compiler:2.x'
}
```
If you're using the Android Databinding library, you may want to increase the number of errors that javac will print. When Dagger prints an error, databinding compilation will halt and sometimes print more than 100 errors, which is the default amount for javac. For more information, see #306.

```
gradle.projectsEvaluated {
  tasks.withType(JavaCompile) {
    options.compilerArgs << "-Xmaxerrs" << "500" // or whatever number you want
  }
}
```

android-apt , 提供 dagger2使用编译生成类 的功能.

```
buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8'
    }
}
apply plugin: 'com.neenbedankt.android-apt' // 注释处理
```



## 重要的注解@inject,@module,@component 

---

dagger2最主要的注解有inject,module,component.

dagger2中最重要的就是module和component了，module是生产我们需要的对象的地方，component是联系module和module生产出的对象的使用场景的桥梁。

说白了，dagger2就像是设计模式中的工厂模式，为我们生产各种我们需要的对象.


### @Inject
依赖注入dependency injection.

@Inject有两个作用:

1. 一是用来标记需要依赖的变量，以此告诉Dagger2为它提供依赖；
1. 二是用来标记构造函数，Dagger2通过@Inject注解可以在需要这个类实例的时候来找到这个构造函数并把相关实例构造出来，以此来为被@Inject标记了的变量提供依赖.

使用了@Inject的表示它是需要依赖注入的, 不需要手动创建.


### @Module

模块, 提供若干类, 在依赖注入中使用.

Module其实是一个简单工厂模式，Module里面的方法基本都是创建类实例的方法。 那些需要注入的类其实就是在这里创建的.

如果Module里面提供创建对象的方法, 则它需要提供一个inject方法,以需要注入对象所在的对象为参数, 这样component将module与需要的对象所在的对象(我们称之为目标类对象)发生关联.

当然,如果采用在构造函数上面加@inject产生对象, 则module可以不提供创建对象的方法了,module本身也不需要提供inject方法了.


### @Component

@Component用于标注接口，是依赖需求方和依赖提供方之间的桥梁。

被Component标注的接口在编译时会生成该接口的实现类（如果@Component标注的接口为CarComponent，则编译期生成的实现类为DaggerCarComponent）,我们通过调用这个实现类的方法完成注入.

它连接依赖的提供者@Module和请求他们的类(标记@Inject的类).

一个Component可以依赖多个Module，如:

```
@Component(modules = {AModule.class,BModule.class,...}) 
```

同样，Component也可以依赖另一个Component，如:

```
@Component(dependencies = BComponent.class,modules = {AModule.class,BModule.class,...}) 
```

甚至多个Component，如:

```
@Component(dependencies = {BComponent.class,CComponent.class,...} ,modules = {AModule.class,BModule.class,...}) 
```

有个需要注意的地方是被依赖的Component需要将对象暴露出来，依赖的Component才能够获取到这个对象，如:

```
//被依赖Component
    @Component(modules = {...})
    public interface BComponent {
        A a（）；
        B b（）；
    }
//依赖Component
    @Component(dependencies = BComponent.class,modules = {...})
    public interface CComponent {

    }
```

CComponent可以通过BComponent获取A，B的对象，其他BComponent的对象对CComponent来说是透明的，不可见。 



上面的Module不是接口, Inject定义时可以是接口也可以是一个一般的类, Component也是个接口或者抽象类. Dagger最终会根据我们提供的Component接口自动生成相应的类.

## 几个问题总结

---

1. Component如何创建?



1. Module如何创建?

1. 需要注入的对象什么时候创建?


也就是说, 真正需要的对象是在module中产生的,

XxxComponent是使用XXXComponent.builder().xxx(new xxx()).build()产生的, 大致代码如下:

```
ActivityComponent component=DaggerActivityComponent.builder()
                .aMedule(new AMedule())
                .build();
```


## 依赖对象的产生

---

### 对象有两种产生方式
如下:

1. 一种是在Module中通过@Provides注解加上providexxx方法.
例子:

    ```
    todo...XXX
    ```

    如果使用module方式, 则moduel中需要有inject方法,将需要的对象所在的方法作为参数传进去, 这样component才能将需要注入的对象所在的对象和产生对象的moduel对象关联起来.

1. 另一种就是直接在要生产的对象的类的构造方法加上@inject注解。
例子:

    ```
    todo...XXX
    ```

那么什么时候用Module，什么时候用@Inject呢？

如果使用在构造函数前加@inject, 首先我们需要有代码, 所以如果采用的是系统或者SDK或者第三方库提供的类的对象, 没有代码, 则只好用module了.

所以如果没有构造函数的代码, 采用module, 有的话随便用module或者inject. 如果要生成的对象是需要有参数构造的, 使用Module生成会方便一些.




### 查找规则

那如果我们即在构造函数上通过标记@Inject提供依赖，有通过@Module提供依赖Dagger2会如何选择呢？具体规则如下：

1. 步骤1：首先查找@Module标注的类中是否存在提供依赖的方法。
1. 步骤2：若存在提供依赖的方法，查看该方法是否存在参数:

    - 若存在参数，则按从步骤1开始依次初始化每个参数；
    - 若不存在，则直接初始化该类实例，完成一次依赖注入。
1. 步骤3：若不存在提供依赖的方法，则查找@Inject标注的构造函数，看构造函数是否存在参数:

    - 若存在参数，则从步骤1开始依次初始化每一个参数
    - 若不存在，则直接初始化该类实例，完成一次依赖注入。

即先到Module里找, 如果没有,再到标记有@Inject的构造函数里找,构造函数如果有参数, 再按规则找参数的创建.

## 其他注解

---

### @Provides

@Provides用于标注Module所标注的类中的方法，该方法在需要提供依赖时被调用，从而把预先提供好的对象当做依赖给标注了@Inject的变量赋值.


### @Scope注解

@Scope同样用于自定义注解，我能可以通过@Scope自定义的注解来限定注解作用域，实现局部的单例.


### @Singleton

@Singleton其实就是一个通过@Scope定义的注解，我们一般通过它来实现全局单例。但实际上它并不能提前全局单例，是否能提供全局单例还要取决于对应的Component是否为一个全局对象。


### @Qualifier

@Qualifier用于自定义注解，也就是说@Qualifier就如同Java提供的几种基本元注解一样用来标记注解类。

我们在使用@Module来标注提供依赖的方法时，方法名我们是可以随便定义的（虽然我们定义方法名一般以provide开头，但这并不是强制的，只是为了增加可读性而已）。那么Dagger2怎么知道这个方法是为谁提供依赖呢？答案就是返回值的类型，Dagger2根据返回值的类型来决定为哪个被@Inject标记了的变量赋值。

但是问题来了，一旦有多个一样的返回类型Dagger2就懵逼了。@Qualifier的存在正式为了解决这个问题，我们使用@Qualifier来定义自己的注解，然后通过自定义的注解去标注提供依赖的方法和依赖需求方（也就是被@Inject标注的变量），这样Dagger2就知道为谁提供依赖了。

一个更为精简的定义：当类型不足以鉴别一个依赖的时候，我们就可以使用@Qualifier注解标示；


### @Named注解 
这个注解是用来设置别名的，用来区分同一个类型的不同对象。

比如我们都知道Android 中的Context有两种，一种是全局的，一种是Activity中的，为了区分他们，我们就可以加上别名；又比如有一个Person类，他有两个实例，一个是小李，一个是小美，我们也可以用别名区分他们。

```
//Person类
public class Person {
    private String name;
    private int age;

    public Person() {
    }

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

   @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}

//我们先要构造一个Module
@Module
public class ActivityMedule {
   @Named（"小李"）
   @Provides
    Person  providePerson(){
        return  new Person("小李"，18);
    }
   @Named（"小美"）
   @Provides
    Person  providePerson(){
        return  new Person("小美"，24);
    }

}
//然后需要一个Component
@Component(modules = ActivityMedule.class)
public interface ActivityComponent {
    void inject(MainActivity mainActivity);
}
//在MainActivity类中使用
public class MainActivity extends AppCompatActivity {
    @Named（"小李"）
    @Inject
    Person p1；

    @Named（"小美"）
    @Inject
    Person p2；

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

       DaggerActivityComponent.builder()
                .activityMedule(new ActivityMedule())
                .build()
                .inject(this);

        Log.i("person",p1.toString());
        Log.i("person",p2.toString());
    }
}
```

当然，如果你觉得@Named注解满足不了你的需求，你也可以使用@Qualifier来自定义自己的别名注解


### @Lazy

todo...Xxx


## 同类型的对象的创建

---

todo...Xxx

使用@Named或者@@Qualifier.



## 一个app中应该根据什么来划分Component？

---

假如一个app（app指的是Android app）中只有一个Component，那这个Component是很难维护、并且变化率是很高，很庞大的，就是因为Component的职责太多了导致的。所以就有必要把这个庞大的Component进行划分，划分为粒度小的Component。那划分的规则这样的：

1. 要有一个全局的Component(可以叫ApplicationComponent),负责管理整个app的全局类实例（全局类实例整个app都要用到的类的实例，这些类基本都是单例的，后面会用此词代替）
1. 每个页面对应一个Component，比如一个Activity页面定义一个Component，一个Fragment定义一个Component。当然这不是必须的，有些页面之间的依赖的类是一样的，可以公用一个Component。
1. 第一个规则应该很好理解，具体说下第二个规则，为什么以页面为粒度来划分Component？

一个app是由很多个页面组成的，从组成app的角度来看一个页面就是一个完整的最小粒度了。

一个页面的实现其实是要依赖各种类的，可以理解成一个页面把各种依赖的类组织起来共同实现一个大的功能，每个页面都组织着自己的需要依赖的类，一个页面就是一堆类的组织者。

当然,多个页面可以共享一个Component, 不是说Component就一定要对应一个或多个Module，Component也可以不包含Module.

划分粒度不能太小了。假如使用mvp架构搭建app，划分粒度是基于每个页面的m、v、p各自定义Component的，那Component的粒度就太小了，定义这么多的Component，管理、维护就很非常困难。
所以以页面划分Component在管理、维护上面相对来说更合理。


## Dagger2原理

---

todo...XXX


## Reference

---

1. [神兵利器Dagger2***](http://www.open-open.com/lib/view/open1482201981550.html)
1. [Dagger2 最清晰的使用教程***](http://www.open-open.com/lib/view/open1474442495481.html)
1. [Dagger2使用](http://www.jianshu.com/p/c2feb21064bb)
1. [从Dagger2基础到Google官方架构MVP+Dagger2架构详解 **](http://blog.csdn.net/u014315849/article/details/51566388)
1. [安卓开发 第一篇 关于依赖注入框架dagger2的使用和理解](http://blog.csdn.net/naivor/article/details/51155741)
1. [Dagger2 使用初步](http://www.cnblogs.com/zhuyp1015/p/5119727.html)
1. [Tasting Dagger 2 on Android](http://fernandocejas.com/2015/04/11/tasting-dagger-2-on-android/)
1. [Dagger2 document](https://google.github.io/dagger/)
1. [Dagger2 API](https://google.github.io/dagger/api/2.0/)
1. [Android：dagger2让你爱不释手-基础依赖注入框架篇*](http://www.jianshu.com/p/cd2c1c9f68d4)
1. [Android：dagger2让你爱不释手-重点概念讲解、融合篇*](http://www.jianshu.com/p/1d42d2e6f4a5)
1. [Android：dagger2让你爱不释手-终结篇](http://www.jianshu.com/p/65737ac39c44)