# 单元测试之PowerMock note


## 简介

---

EasyMock 以及 Mockito 都因为可以极大地简化单元测试的书写过程而被许多人应用在自己的工作中，但是这 2 种 Mock 工具都不可以实现对静态函数、构造函数、私有函数、Final 函数以及系统函数的模拟，但是这些方法往往是我们在大型系统中需要的功能。

PowerMock 是在 EasyMock 以及 Mockito 基础上的扩展，通过定制类加载器等技术，PowerMock 实现了之前提到的所有模拟功能，使其成为大型系统上单元测试中的必备工具。

PowerMock 也是一个单元测试模拟框架，它是在其它单元测试模拟框架的基础上做出的扩展。通过提供定制的类加载器以及一些字节码篡改技巧的应用，PowerMock 现了对静态方法、构造方法、私有方法以及 Final 方法的模拟支持，对静态初始化过程的移除等强大的功能。因为 PowerMock 在扩展功能时完全采用和被扩展的框架相同的 API, 熟悉 PowerMock 所支持的模拟框架的开发者会发现 PowerMock 非常容易上手。PowerMock 的目的就是在当前已经被大家所熟悉的接口上通过添加极少的方法和注释来实现额外的功能，** 目前，PowerMock 仅支持 EasyMock 和 Mockito**。




## 配置

---

对于需要的开发包，PowerMock 网站提供了”一站式”下载 : 从 此页面中选择以类似 PowerMock 1.4.10 with Mockito and JUnit including dependencies 为注释的链接，该包中包含了最新的 JUnit 库，Mockito 库，PowerMock 库以及相关的依赖。

1. Maven配置  
JUnit 版本 4.4 以上请参考清单如下:

    ```
    <properties> 
        <powermock.version>1.4.10</powermock.version> 
    </properties> 
    <dependencies> 
    <dependency> 
        <groupId>org.powermock</groupId> 
        <artifactId>powermock-module-junit4</artifactId> 
        <version>${powermock.version}</version> 
        <scope>test</scope> 
    </dependency> 
    <dependency> 
        <groupId>org.powermock</groupId> 
        <artifactId>powermock-api-mockito</artifactId> 
        <version>${powermock.version}</version> 
        <scope>test</scope> 
    </dependency> 
    </dependencies>
    ```

1. gradlew配置:

    ```
    testCompile "org.powermock:powermock-module-junit4:1.6.4"
    testCompile "org.powermock:powermock-module-junit4-rule:1.6.4"
    testCompile "org.powermock:powermock-api-mockito:1.6.4"
    testCompile "org.powermock:powermock-classloading-xstream:1.6.4"
    ```


## mock对象的创建

---

todo...




## 普通Mock:Mock参数传递的对象

---

普通Mock不需要加@RunWith和@PrepareForTest注解。

例子如下:

使用的类:

```
public class powermockExample {

    public boolean callArgumentInstance(File file) {
        return file.exists();
    }
    ...

```

测试:

```
@Test
    public void testCallArgumentInstance() {
        File file = PowerMockito.mock(File.class);
        powermockExample test = new powermockExample();
        PowerMockito.when(file.exists()).thenReturn(true);
        assertTrue(test.callArgumentInstance(file));
    }
```



## mock构造函数

---

当要测试的方法里使用了通过new构造出来的对象, 我们也可以模拟它.
我们可以先通过PowerMockito.mock(XXX.class)mock一个对象出来,然后再使用PowerMockito.whenNew(XXX.class).withArguments(xxx)thenReturn(object)指定构造函数的返回为mock出来的对象.

其中XXX.class为要构造的类的class, withArguments(xx)里的参数为构造函数的参数,可以有一到多个参数,thenReturn(object)里的object为我们自己mock出来的方法. withAnyArguments()则表示任何的参数.

当使用PowerMockito.whenNew方法时，必须加注解@PrepareForTest和@RunWith。注解@PrepareForTest里写的类是需要mock的new对象代码所在的类。

使用的类:

```
public class powermockExample {

    public boolean checkFileExist(String strPath){
        File file = new File(strPath);
        return file.exists();
    }
    ...
}
```

测试:

```
@RunWith(PowerMockRunner.class)
@PrepareForTest(powermockExample.class)
public class powermockExampleTest {

    @Test
    public void mockConstructorTest(){
        final String directoryPath = "mocked path";
        File directoryMock = PowerMockito.mock(File.class);

        try {
            // This is how you tell PowerMockito to mock construction of a new File.
            PowerMockito.whenNew(File.class).withArguments(directoryPath).thenReturn(directoryMock);
            // Standard expectations
            PowerMockito.when(directoryMock.exists()).thenReturn(true);
            assertTrue(new powermockExample().checkFileExist(directoryPath));

            // Optionally verify that a new File was "created".
            verifyNew(File.class).withArguments(directoryPath);
        }catch (Exception e){
        }


    }
```

## mock静态方法

---

采用方法PowerMockito.mockStatic(StaticClass.class)mock静态类.

当需要mock静态方法的时候，必须加注解@PrepareForTest和@RunWith。注解@PrepareForTest里写的类是静态方法所在的类。

相关例子如下, ClassHasStaticMethod包含静态方法, powermockExample使用ClassHasStaticMethod静态方法,powermockExampleTest里mock静态方法:

```
/*************ClassHasStaticMethod包含静态方法***************/
public class ClassHasStaticMethod {


    public static boolean hasValue(){
        return false;
    }


    public static boolean isEmpty(String strValue){
        return strValue.isEmpty();
    }
}


/**********powermockExample使用ClassHasStaticMethod静态方法**********/
public class powermockExample {
    public boolean checkWithNoArgStaticMethod(){
        return ClassHasStaticMethod.hasValue();
    }

    public boolean checkWithArgStaticMethod(String strValue){
        return ClassHasStaticMethod.isEmpty(strValue);
    }
}

/******************下面mock静态方法********************/
@RunWith(PowerMockRunner.class)
@PrepareForTest({powermockExample.class, ClassHasStaticMethod.class})
public class powermockExampleTest {
    //无参数的静态方法的mock
    @Test
    public void staticWithNoArgsTest(){
        powermockExample test = new powermockExample();
        PowerMockito.mockStatic(ClassHasStaticMethod.class);
        PowerMockito.when(ClassHasStaticMethod.hasValue()).thenReturn(true);
        Assert.assertTrue(test.checkWithNoArgStaticMethod());
    }

    //有参数的静态方法的mock
    @Test
    public void staticWithArgsTest(){
        powermockExample test = new powermockExample();
        PowerMockito.mockStatic(ClassHasStaticMethod.class);
        PowerMockito.when(ClassHasStaticMethod.isEmpty(anyString())).thenReturn(true);
        Assert.assertTrue(test.checkWithArgStaticMethod("hasxxx"));
    }

}

```


## mock私有方法

---

使用spy(obj)模拟出对象,然后使用when指定私有函数的名称,对应参数以及返回值. 相关代码如下:

```
powermockExample test = PowerMockito.spy(new powermockExample());
 
try {
 
    /*
 
     * Setup the expectation to the private method using the method name
 
     */
 
    PowerMockito.when(test, strPrivateMethodName, strParameter).thenReturn(false);
```

完整代码如下:

```
public class powermockExample {
    public boolean callPrivateMethod(String str){
        return privateMethod(str);
    }

    private boolean privateMethod(String str){
        System.out.println("call private method,parameter:"+str);
        return str.isEmpty();
    }


}


@RunWith(PowerMockRunner.class)
@PrepareForTest({powermockExample.class, ClassHasStaticMethod.class})
public class powermockExampleTest {

    @Test
    public void callPrivateMethodTest(){
        String strParameter = "hello";
        String strPrivateMethodName = "privateMethod";
        powermockExample test = PowerMockito.spy(new powermockExample());
        try {
            /*
             * Setup the expectation to the private method using the method name
             */
            PowerMockito.when(test, strPrivateMethodName, strParameter).thenReturn(false);
            Assert.assertFalse(test.callPrivateMethod(strParameter));
        } catch (Exception ex) {
        }

    }

}
```


## mock返回值为void的方法

---

todo...


## partialmock

---

todo...



## PowerMock简单实现原理

---

1. 某个测试方法被注解@PrepareForTest标注以后，在运行测试用例时，会创建一个新的org.powermock.core.classloader.MockClassLoader实例，然后加载该测试用例使用到的类（系统类除外）。
1. PowerMock会根据你的mock要求，去修改写在注解@PrepareForTest里的class文件（当前测试类会自动加入注解中），以满足特殊的mock需求。例如：去除final方法的final标识，在静态方法的最前面加入自己的虚拟实现等。
1. 如果需要mock的是系统类的final方法和静态方法，PowerMock不会直接修改系统类的class文件，而是修改调用系统类的class文件，以满足mock需求。



## 其他

---

1. 关于@PrepareForTest  
    + 单个类使用@PrepareForTest(Class1.class)
    + 多个类使用@PrepareForTest({Class1.class, Class2.class})


## Reference

---

1. [PowerMock github](https://github.com/powermock/powermock)
1. [PowerMock API](https://static.javadoc.io/org.powermock/powermock-api-mockito/1.6.6/overview-summary.html)
1. [PowerMock介绍](http://blog.csdn.net/jackiehff/article/details/14000779)