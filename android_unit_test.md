# Android unite test note

## 测试类型分类

---
### 按运行的目标分类
按运行在设备/模拟器上,还是直接运行在JVM上,分为Local unit tests和Instrumented tests.

1. **Local unit tests**: 文件夹路径在module-name/src/test/java/.直接运行在本地JVM, 不能访问Android framework API,类似于Java的单元测试.
1. **Instrumented tests**: 文件夹路径module-name/src/androidTest/java. 需要运行在设备上或者模拟器上.

一般我们创建了项目后, 除了”main”文件夹外,还有一个”test”文件夹和一个”androidTest”文件夹, 这两个文件夹里都是测试用例, 区别如下:

+ ”androidTest”为AndroidJUnitRunner相关单元测试, 需要跑在设备或者模拟器里, 
+ “test”为JUnit单元测试目, 直接JVM跑.

### 按单位分类
可分为单元测试(Unit tests)和集成测试(Integration Tests).

#### 单元测试(Unit tests)
单元测试(Unit tests)又分:

1. **Local Unit Tests**: Unit tests that run locally on the Java Virtual Machine (JVM). Use these tests to minimize execution time when your tests have no Android framework dependencies or when you can mock the Android framework dependencies.
1. **Instrumented unit tests**: Unit tests that run on an Android device or emulator. These tests have access to Instrumentation information, such as the Context of the app you are testing. Use these tests when your tests have Android dependencies that mock objects cannot satisfy.

即运行在Jvm的本地单元测试和运行在设备或者虚拟机的instrumented unit tests.


#### 集成测试(Integration Tests)
又分为"Components within your app only"(App内组件)和"Cross-app Components"(跨App):

1. **Components within your app only(App内组件)**: This type of test verifies that the target app behaves as expected when a user performs a specific action or enters a specific input in its activities. For example, it allows you to check that the target app returns the correct UI output in response to user interactions in the app’s activities. UI testing frameworks like Espresso allow you to programmatically simulate user actions and test complex intra-app user interactions.
1. **Cross-app Components(跨App)**: This type of test verifies the correct behavior of interactions between different user apps or between user apps and system apps. For example, you might want to test that your app behaves correctly when the user performs an action in the Android Settings menu. UI testing frameworks that support cross-app interactions, such as UI Automator, allow you to create tests for such scenarios.



## 测试使用到的框架/工具

---

### JUnit
>You should write your unit or integration test class as a JUnit 4 test class. The framework offers a convenient way to perform common setup, teardown, and assertion operations in your test.
我们把单元测试或者集成测试类携程JUnit4测试类.

### Android Testing Support Library

>The Android Testing Support Library provides a set of APIs that allow you to quickly build and run test code for your apps, including JUnit 4 and functional UI tests.
包含下面几项:
1. AndroidJUnitRunner:A JUnit 4-compatible test runner for Android.
1. Espresso: A UI testing framework; suitable for functional UI testing within an app. 基于Android Instrumentation framework实现的Android UI自动化测试框架，不支持跨进程.
1. UI Automator: A UI testing framework suitable for cross-app functional UI testing between both system and installed apps.


### Monkey and monkeyrunner
#### Monkey
>This is a command-line tool that sends pseudo-random streams of keystrokes, touches, and gestures to a device. You run it with the Android Debug Bridge (adb) tool, and use it to stress-test your app, report back errors any that are encountered, or repeat a stream of events by running the tool multiple times with the same random number seed.
ey

Monkey是Android中的一个命令行工具，可以运行在模拟器里或实际设备中。它向系统发送伪随机的用户事件流(如按键输入、触摸屏输入、手势输入等)，实现对正在开发的应用程序进行压力测试。Monkey测试是一种为了测试软件的稳定性、健壮性的快速有效的方法。

#### monkeyrunner
>This tool is an API and execution environment for test programs written in Python. The API includes functions for connecting to a device, installing and uninstalling packages, taking screenshots, comparing two images, and running a test package against an app. Using the API, you can write a wide range of large, powerful, and complex tests. You run programs that use the API with the monkeyrunner command-line tool.

monkeyrunner工具提供了一个API，使用此API写出的程序可以在Android代码之外控制Android设备和模拟器。通过monkeyrunner，您可以写出一个Python程序去安装一个Android应用程序或测试包，运行它，向它发送模拟击键，截取它的用户界面图片，并将截图存储于工作站上。monkeyrunner工具的主要设计目的是用于测试功能/框架水平上的应用程序和设备，或用于运行单元测试套件，但您当然也可以将其用于其它目的。

## 创建测试类

---
在代码中选中某个类的名字,右键,”Go To”—>”Test”,然后弹出如下界面,在下面选中要进行单元测试的方法, 确定后会自动创建对应测试类并生成相应的测试方法: 

[图--------xxxx]()

在上面的UI中我们选择测试类是创建在androidTest中还是test中.上面已经描述过,前者的用例需要在模拟器或者设备中运行,后者直接在Jvm中就可以运行.但是我们查看通过UI创建的测试类,没发现什么区别.区别在于.idea目录下的workspace.xml中的configuration项.

如果测试类叫CalculatorTest, 从workspace.xml中看到, 在test中, configuration如下, 它的type为"JUnit":

```
<configuration default="false" name="CalculatorTes" type="JUnit" factoryName="JUnit" temporary="true" nameIsGenerated="true">
      <extension name="coverage" enabled="false" merge="false" sample_coverage="true" runner="idea">
        <pattern>
          <option name="PATTERN" value="com.example.lluo.optimizetest.*" />
          <option name="ENABLED" value="true" />
        </pattern>
      </extensio
```

在androidTest中, type为AndroidTestRunConfigurationType, workspace.xml如下:

```
<configuration default="false" name="CalculatorTes" type="AndroidTestRunConfigurationType" factoryName="Android Tests" temporary="true" nameIsGenerated="true">
      <module name="app" />
      <option name="TESTING_TYPE" value="2" />
      <option name="INSTRUMENTATION_RUNNER_CLASS" value="android.support.test.runner.AndroidJUnitRunner" />
      <option name="METHOD_NAME" value="" />
      <option name="CLASS_NAME" value="com.example.lluo.optimizetest.CalculatexxxxxTest" />      
```


## 运行测试用例

---
### 运行某个测试类
直接在类里右键, 然后选择"Run xxx"运行该测试用例类
也可以通过命令行直接在执行"bash gradlew test" 执行所有的测试用例.
另外一种方法是通过菜单"Run"--"Edit Configurations",弹出如下对话框,然后在UI里选择,然后执行. 注意这里的测试type有很多种,比如class,Category, All in package等等

[图图图xxxxx]()




android {
    compileSdkVersion 22
    buildToolsVersion "22.0.1"

    defaultConfig {
        ...
        //ADD THIS LINE:
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
    }


dependencies {
    ...
    testCompile 'junit:junit:4.12'
    androidTestCompile('com.android.support.test.espresso:espresso-core:2.2.2', {
        exclude group: 'com.android.support', module: 'support-annotations'
    })
    androidTestCompile 'com.android.support.test:runner:0.2'
    androidTestCompile 'com.android.support.test:rules:0.2'
}

## Instrumented Unit Tests

---
>Instrumented unit tests are tests that run on physical devices and emulators, and they can take advantage of the Android framework APIs and supporting APIs, such as the Android Testing Support Library. You should create instrumented unit tests if your tests need access to instrumentation information (such as the target app's Context) or if they require the real implementation of an Android framework component (such as a Parcelable or SharedPreferences object).

即Instrumented Unit Tests是只那些需要跑在设备或者模拟器上的test.它的好处是可以访问Android相关的API.如果我们要测试的或者测试过程需要用到的和Android相关的东西,则需要采用这种方式.

### 需要的配置
配置如下:

```
dependencies {
    androidTestCompile 'com.android.support:support-annotations:24.0.0'
    androidTestCompile 'com.android.support.test:runner:0.5'
    androidTestCompile 'com.android.support.test:rules:0.5'
    // Optional -- Hamcrest library
    androidTestCompile 'org.hamcrest:hamcrest-library:1.3'
    // Optional -- UI testing with Espresso
    androidTestCompile 'com.android.support.test.espresso:espresso-core:2.2.2'
    // Optional -- UI testing with UI Automator
    androidTestCompile 'com.android.support.test.uiautomator:uiautomator-v18:2.1.2'
}
```

Caution: If your build configuration includes a compile dependency for the support-annotations library and an androidTestCompile dependency for the espresso-core library, your build might fail due to a dependency conflict. To resolve, update your dependency for espresso-core as follows:

    androidTestCompile('com.android.support.test.espresso:espresso-core:2.2.2', {
        exclude group: 'com.android.support', module: 'support-annotations'
    })

To use JUnit 4 test classes, make sure to specify AndroidJUnitRunner as the default test instrumentation runner in your project by including the following setting in your app's module-level build.gradle file:

    android {
        defaultConfig {
            testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
        }
    }
即上面的设置指定AndroidJUnitRunner为项目默认的runner.

### 创建Instrumented Unit Test class
To create an instrumented JUnit 4 test class, we should:

1. add the @RunWith(AndroidJUnit4.class) annotation at the beginning of your test class definition. 
1. also need to specify the AndroidJUnitRunner class provided in the Android Testing Support Library as your default test runner. 

即一需要在测试类前面加@RunWith(AndroidJUnit4.class),二需要在配置(build.gradle的defaultConfig中)中指定默认的test runner为AndroidJUnitRunner.

下面是一个例子:

```
@RunWith(AndroidJUnit4.class)
@SmallTest
public class LogHistoryAndroidUnitTest {

    public static final String TEST_STRING = "This is a string";
    public static final long TEST_LONG = 12345678L;
    private LogHistory mLogHistory;

    @Before
    public void createLogHistory() {
        mLogHistory = new LogHistory();
    }

    @Test
    public void logHistory_ParcelableWriteRead() {
        // Set up the Parcelable object to send and receive.
        mLogHistory.addEntry(TEST_STRING, TEST_LONG);

        // Write the data.
        Parcel parcel = Parcel.obtain();
        mLogHistory.writeToParcel(parcel, mLogHistory.describeContents());

        // After you're done with writing, you need to reset the parcel for reading.
        parcel.setDataPosition(0);

        // Read the data.
        LogHistory createdFromParcel = LogHistory.CREATOR.createFromParcel(parcel);
        List<Pair<String, Long>> createdFromParcelData = createdFromParcel.getData();

        // Verify that the received data is correct.
        assertThat(createdFromParcelData.size(), is(1));
        assertThat(createdFromParcelData.get(0).first, is(TEST_STRING));
        assertThat(createdFromParcelData.get(0).second, is(TEST_LONG));
    }
}
```

## 手工让android项目支持自动化测试

---
一般情况下, 一个android项目创建后,会在src目录下创建test和androidTest两个目录,可以创建测试类添加到这两个目录里,然后可以运行测试类.

但是有些项目因为历史原因是没有相关的配置,所以不支持单元测试, 这时候需要做如下的手工配置:
1. module下build.gradle的配置:

        android {
            compileSdkVersion 22
            buildToolsVersion "22.0.1"

            defaultConfig {
                ...
                //ADD THIS LINE:
                testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
            }

        dependencies {
            ...
            testCompile 'junit:junit:4.12'
            androidTestCompile('com.android.support.test.espresso:espresso-core:2.2.2', {
                exclude group: 'com.android.support', module: 'support-annotations'
            })
            androidTestCompile 'com.android.support.test:runner:0.2'
            androidTestCompile 'com.android.support.test:rules:0.2'

    如果在编译过程中碰到如下问题:

        Error:Conflict with dependency 'com.android.support:support-annotations'. Resolved versions for app (22.0.0) and test app (23.1.1) differ. See http://g.co/androidstudio/app-test-app-conflict for details.
    可以在module的build.gradle里加上如下配置:

        configurations.all {
            resolutionStrategy {
                force 'com.android.support:support-annotations:23.1.1'
            }
        }

1. 在module-name/src目录下创建test和androidTest目录  
一般该目录下已经有main文件夹.




## 一些问题

---
1. 运行测试用例碰到下面的错误:

        !!! JUnit version 3.8 or later expected:
        
        java.lang.RuntimeException: Stub!
        at junit.runner.BaseTestRunner.&lt;init&gt;(BaseTestRunner.java:5)
        at junit.textui.TestRunner.&lt;init&gt;(TestRunner.java:54)
        at junit.textui.TestRunner.&lt;init&gt;(TestRunner.java:48)
        at junit.textui.TestRunner.&lt;init&gt;(TestRunner.java:41)
    解决方法:

        The problem is that the Android SDK already includes an older version of Junit, while Robolectric uses features from junit4. The solution is to modify the module dependency order in your project. 
        Go to Project Structure -> modules -> android-gradle-test, choose the Dependencies-tab and move the Android API Platform to the bottom of the list. Unfortunately, this has to be done everytime you change the gradle config (and reload the project), hopefully a better solution will be found soon.



## Reference:

---
1. [Getting Started with Testing](https://developer.android.com/training/testing/start/index.html)
1. [每个开发者都应该懂一点单元测试]( http://www.jianshu.com/p/5c8cde7ab54e)
1. [Unit Testing With Android Studio](http://www.jianshu.com/p/1f130e8ea188)
1. [Android – Basic testing with unit test, android test and roboguice, robolectric, mockito](http://hintdesk.com/android-basic-testing-with-unit-test-android-test-and-roboguice-robolectric-mockito/)
1. [Test Your App](https://developer.android.com/studio/test/index.html#test_types_and_location)
1. [蘑菇街支付金融Android单元测试实践](http://www.infoq.com/cn/articles/mogujie-android-unit-testing)
1. [Developing Android unit and instrumentation tests - Tutorial](http://www.vogella.com/tutorials/AndroidTesting/article.html)
1. [Building Instrumented Unit Tests](https://developer.android.com/training/testing/unit-testing/instrumented-unit-tests.html)

## JUnit Reference
1. [JUnit框架功能详细——JUnit学习（一）](https://my.oschina.net/pangyangyang/blog/144495)
1. [JUnit4使用教程-快速入门](http://blog.csdn.net/chenleixing/article/details/44259453)



What's new in Android Testing Droidcon Italy 2015
https://docs.google.com/presentation/d/1EtFKPluGiuxZcr4W_cAziEY_--wbY_1otw44XEBv7JA/edit#slide=id.g98a986571_0_0

Getting Started with Testing
https://developer.android.com/training/testing/start/index.html

Testing activity in Android Studio. Part 1.
http://evgenii.com/blog/testing-activity-in-android-studio-tutorial-part-1/



Junt4 默认提供BlockJUnit4ClassRunner，如果不填写注解默认会使用BlockJUnit4ClassRunner。AndroidJUnit继承自BlockJUnit4ClassRunner。同时根据不同的需要，还提供了Suit，用来执行多个单元测试用例类。Parameterized继承自Suit,提供参数化;Category同样也继承自Suit...


@RunWith(Categories.class)
@SuiteClasses(PersonTest.class)
public class CategoryTest



使用RxJava构造Android清晰框架
http://www.imooc.com/article/2028



InstrumentationTestCase
AndroidJUnit4
    @RunWith(AndroidJUnit4.class)
@RunWith

JUnit:
@Before: Use this annotation to specify a block of code that contains test setup operations. The test class invokes this code block before each test. You can have multiple @Before methods but the order in which the test class calls these methods is not guaranteed.
@After: This annotation specifies a block of code that contains test tear-down operations. The test class calls this code block after every test method. You can define multiple @After operations in your test code. Use this annotation to release any resources from memory.
@Test: Use this annotation to mark a test method. A single test class can contain multiple test methods, each prefixed with this annotation.
@Rule: Rules allow you to flexibly add or redefine the behavior of each test method in a reusable way. In Android testing, use this annotation together with one of the test rule classes that the Android Testing Support Library provides, such as ActivityTestRule or ServiceTestRule.
@BeforeClass: Use this annotation to specify static methods for each test class to invoke only once. This testing step is useful for expensive operations such as connecting to a database.
@AfterClass: Use this annotation to specify static methods for the test class to invoke only after all tests in the class have run. This testing step is useful for releasing any resources allocated in the @BeforeClass block.
@Test(timeout=): Some annotations support the ability to pass in elements for which you can set values. For example, you can specify a timeout period for the test. If the test starts but does not complete within the given timeout period, it automatically fails. You must specify the timeout period in milliseconds, for example: @Test(timeout=5000).



//---------
AndroidJUnit4
public final class AndroidJUnit4 
extends BlockJUnit4ClassRunner 


Developers wanting to explicitly tag a class as an Android JUnit 4 class should use @RunWith(AndroidJUnit4.class)



Runner是JUnit的核心，它封装了一个测试类中所有的测试方法.
在JUnit中有很多个Runner，他们负责调用你的测试代码，每一个Runner都有各自的特殊功能，你要根据需要选择不同的Runner来运行你的测试代码。
JUnit中有一个默认Runner，即BlockJUnit4ClassRunner，如果你没有指定，那么系统自动使用默认Runner来运行你的代码。
public class BlockJUnit4ClassRunner
extends ParentRunner<org.junit.runners.model.FrameworkMethod>
Implements the JUnit 4 standard test case class model, as defined by the annotations in the org.junit package. Many users will never notice this class: it is now the default test class runner, but it should have exactly the same behavior as the old test class runner (JUnit4ClassRunner). BlockJUnit4ClassRunner has advantages for writers of custom JUnit runners that are slight changes to the default behavior, however:

It has a much simpler implementation based on Statements, allowing new operations to be inserted into the appropriate point in the execution flow.
It is published, and extension and reuse are encouraged, whereas JUnit4ClassRunner was in an internal package, and is now deprecated.∫