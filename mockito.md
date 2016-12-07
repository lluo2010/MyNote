# 单元测试之Mockito


## 几个实际的问题

1. 类的静态方法的测试
1. 回调方法的测试
1. 异步测试
1. 对象里面使用了另一对象,但是该独对象不是传进去的, 是在对象里面创建的, 怎么mock.


## 简介

---

Mockito是Java单元测试Mock开源框架.

大多Java Mock库如EasyMock或JMock都是expect-run-verify（期望-运行-验证）方式，而Mockito则使用更简单，更直观的方法：在执行后的互动中提问。使用Mockito，你可以验证任何你想要的。而那些使用expect-run-verify方式的库，你常常被迫查看无关的交互。

非expect-run-verify方式也意味着，Mockito无需准备昂贵的前期启动。他们的目标是透明的，让开发人员专注于测试选定的行为。

但是，Mockito 也并不是完美的，它不提供对静态方法、构造方法、私有方法以及 Final 方法的模拟支持。


## 配置

---

1. Android Gradle配置

    ```
    testCompile 'org.mockito:mockito-core:1.+'
    // required if you want to use Mockito for Android instrumentation tests
    androidTestCompile 'org.mockito:mockito-core:1.+'
    androidTestCompile "com.google.dexmaker:dexmaker:1.2"
    androidTestCompile "com.google.dexmaker:dexmaker-mockito:1.2"
    ```
1. Maven

    ```
    <dependencies>     
    <dependency>     
    <groupId>org.mockito</groupId>     
    <artifactId>mockito-all</artifactId>     
    <version>1.8.5</version>     
    <scope>test</scope>     
    </dependency>     
    </dependencies> 
    ```


## 标准创建mock方式 

---

### 一.使用 @Mock 注解

1. 步骤  
分两步骤:

    + 在基础类或者测试runner里面初始化mock，使用如下：
            MockitoAnnotations.initMocks(testClass);

    + 对需要mock的对象使用@Mock

1. 好处  
    + 最小化可重用mock创建代码
    + 使测试类更加可读性
    + 使验证错误更加易读，因为字段名称用于唯一识别mock

1. 例子:

    ```
    public class AnswerCallbackTest {
        private CallbackImpl m_callbackImp;

        @Mock
        private MyAsyncTask m_asyncTask;


        @Before
        public void setUp() {
            MockitoAnnotations.initMocks(this);
            m_callbackImp = new CallbackImpl(m_asyncTask);
        }

    ```

### 二.直接使用Mockito的mock方法

如下:

```
Iterator iterator = mock(Iterator.class);
//预设当iterator调用next()时第一次返回hello，第n次都返回world
when(iterator.next()).thenReturn("hello").thenReturn("world");
//使用mock的对象
String result = iterator.next() + " " + iterator.next() + " " + iterator.next();
assertEquals("hello world world",result)
```

## mock模拟类方法


1. 使用mock方法
Iterator iterator = mock(Iterator.class);

1. 


1. when().thenReturn()

1. 可对方法设定返回异常when().thenTrow();  
    when(list.get(1)).thenThrow(new RuntimeException("test excpetion"));  


## Mockito的几个重要方法

---

### mock

#### when

### doXXX

### 行为验证-verify



1. when
1. 


## OngoingStubbing

---

OngoingStubbing is an interface that allows you to specify an action to take in response to a call. You should never need to refer to OnglingStubbing directly; all calls to it should happen as chained method calls in the statement starting with when.

这个接口允许我们指定动作的响应(返回值或者异常等).

### 方法介绍

方法如下:

Modifier and Type	|Method | Description
-|:-|:-
<M> M	|getMock()|Returns the mock that was used for this stub.
OngoingStubbing<T>	|thenReturn(T value)|Sets a return value to be returned when the method is called.
OngoingStubbing<T>	|thenReturn(T value, T... values)| Sets consecutive return values to be returned when the method is called.
OngoingStubbing<T>	|thenThrow(Class<? extends Throwable> throwableType)| Sets a Throwable type to be thrown when the method is called.
OngoingStubbing<T>	|thenThrow(Class<? extends Throwable> toBeThrown, Class<? extends Throwable>... nextToBeThrown)| Sets Throwable classes to be thrown when the method is called.
OngoingStubbing<T>	|thenThrow(Throwable... throwables)| Sets Throwable objects to be thrown when the method is called.
OngoingStubbing<T>	|then(Answer<?> answer)|Sets a generic Answer for the method.
OngoingStubbing<T>	|thenAnswer(Answer<?> answer)| Sets a generic Answer for the method.
OngoingStubbing<T>	|thenCallRealMethod()| Sets the real implementation to be called when the method is called on a mock object.

### 例子

1. thenReturn-指定单个返回  
Sets a return value to be returned when the method is called. 所有调用该方法始终返回指定值. E.g:

        when(mock.someMethod()).thenReturn(10);

1. thenReturn-指定多个返回   
Sets consecutive return values to be returned when the method is called. 指定多个返回,超过指定次数的都返回最后一个指定的值. E.g:

        when(mock.someMethod()).thenReturn(1, 2, 3);


## 模拟我们所期望的结果

---
使用when(xx).thenReturn(yy).

1. 相同函数相同参数  
对于相同函数相同参数的预设,前面几次thenReturn指定前面几次调用分别返回的值, 之后所有的调用返回最后一个预设的值. 如果始终只返回一个值, 指定一个thenReturn就可以了.

    + 始终返回相同的指定的值:

        ```
        when(mock.someMethod()).thenReturn(10);
        ```
    + 根据调用顺序指定不同的返回值, 超过指定次数的调用返回最后一个指定的返回值

        ```

        @Test  
        public void when_thenReturn(){  
            //mock一个Iterator类  
            Iterator iterator = mock(Iterator.class);  
            //预设当iterator调用next()时第一次返回hello，第n次都返回world  
            when(iterator.next()).thenReturn("hello").thenReturn("world");  
            //使用mock的对象  
            String result = iterator.next() + " " + iterator.next() + " " + iterator.next();  
            //验证结果  
            assertEquals("hello world world",result);  
        }  

        ```
        当然, 也可以直接采用thenReturn(T value, T... values)预设多个返回值,比如下面, 第一次调用返回1,第二次2,后面都返回3.

            when(mock.someMethod()).thenReturn(1, 2, 3);

        另外相同的方法和参数多次使用when(x).thenReturn(y),最后一个有效,例子如下:

        ```
        @Test(expected = RuntimeException.class)  
        public void consecutive_calls(){  
            //模拟连续调用返回期望值，如果分开，则只有最后一个有效  
            when(mockList.get(0)).thenReturn(0);  
            when(mockList.get(0)).thenReturn(1);  
            when(mockList.get(0)).thenReturn(2);  
            when(mockList.get(1)).thenReturn(0).thenReturn(1).thenThrow(new RuntimeException());  
            assertEquals(2,mockList.get(0));  
            assertEquals(2,mockList.get(0));  
            assertEquals(0,mockList.get(1));  
            assertEquals(1,mockList.get(1));  
            //第三次或更多调用都会抛出异常  
            mockList.get(1);  
        ```

1. 相同函数不同参数  
    根据不同的参数预设返回不同的结果,对于没有预设的情况会返回默认值.

    ```
    Comparable comparable = mock(Comparable.class);  
    //预设根据不同的参数返回不同的结果  
    when(comparable.compareTo("Test")).thenReturn(1);  
    when(comparable.compareTo("Omg")).thenReturn(2);  
    assertEquals(1, comparable.compareTo("Test"));  
    assertEquals(2, comparable.compareTo("Omg"));  
    //对于没有预设的情况会返回默认值  
    assertEquals(0, comparable.compareTo("Not stub"));  
    ```


## 参数匹配(Argument matchers)

---

上面提到的都是调用的函数的参数是特定的(指定值,或者无值)情况下指定返回值, 参数匹配则提供了更灵活的参数范围控制.使用anyXXX能指定任何值,使用ArgumentMatcher可以定制更灵活的条件.

ArgumentMatchers提供了一种灵活的方式让我们指定某种我们期望指定的值.

*** 下面提供的方法都是期望提供一个符合某种条件的值,所以它的返回值的类型都是期望的值的类型. ***

### 指定类型的任意值-anyXXX()

比如any(),anyInt(),anyDouble(),anyByte(),anyBoolean(),anyString(),anyCollection(),anyIterable(),anyList(),anyMap(),anySet()等等.

如下:

```
//stubbing using anyInt() argument matcher
 when(mockedList.get(anyInt())).thenReturn("element");

 //following prints "element"
 System.out.println(mockedList.get(999));

 //you can also verify using argument matcher
 verify(mockedList).get(anyInt());
```

Warning:  
If you are using argument matchers, all arguments have to be provided by matchers. E.g: (example shows verification but the same applies to stubbing):

需要注意的是, 如果使用参数匹配,这所有的参数都需要使用参数匹配, 如下, 第二种方式是有问题的, 因为最后一个参数不是使用参数匹配:

```
 verify(mock).someMethod(anyInt(), anyString(), eq("third argument"));
 //above is correct - eq() is also an argument matcher

 verify(mock).someMethod(anyInt(), anyString(), "third argument");
 //above is incorrect - exception will be thrown because third argument is given without argument matcher.
```


### 指定类型的指定的值-eq(xx)
可以使用eq(xx). eq有一个参数和一个返回值, 表示返回一个和指定的参数相等的值. 

提供的eq有eq(boolean),eq(byte),eq(char),eq(int),eq(float),eq(T)等. 其中eq(T)为泛型.

比如:

```
verify(mock).someMethod(anyInt(), anyString(), eq("third argument"));
```

### 指定空值或者非空值
isNull()表示是空值, notNull()表示为非空值, 声明如下:

```
public static <T> T isNull() {
public static <T> T notNull() {
```

例子如下:

```
// stubbing using anyBoolean() argument matcher
 when(mock.dryRun(anyBoolean())).thenReturn("state");

 // below the stub won't match, and won't return "state"
 mock.dryRun(null);

 // either change the stub
 when(mock.dryRun(isNull())).thenReturn("state");
 mock.dryRun(null); // ok

 // or fix the code ;)
 when(mock.dryRun(anyBoolean())).thenReturn("state");
 mock.dryRun(true); // ok
```

### 符合条件的string

包含如下:

```
public static String contains(String substring);
public static String matches(String regex);
public static String endsWith(String suffix);
public static String startsWith(String prefix);
```


### 使用自定义匹配器-xxThat(ArgumentMatcher<T>)
我们可以使用anyInt()指定参数为任意的int型, 但是我们无法指定更复杂的要求的类型, 比如自定义的类型,并且还包含其他的条件. 这时候我们可以派生ArgumentMatcher接口,实现这些匹配规则.

ArgumentMatcher<T>为泛型接口, 定义如下, 包含一个matches方法, 当给定参数匹配时, 返回true, 通过ArgumentMatcher来验证调用时传进来的参数是否满足指定时的要求,是才返回指定的值:

```
public interface ArgumentMatcher<T> {
    boolean matches(T argument);
}

```
自定义ArgumentMatcher后, 然后采用下面的xxxThat(matcher)指定参数要符合的条件,返回值为各自的类型变量:

```
public static <T> T argThat(ArgumentMatcher<T> matcher);
public static char charThat(ArgumentMatcher<Character> matcher);
public static boolean booleanThat(ArgumentMatcher<Boolean> matcher);
public static byte byteThat(ArgumentMatcher<Byte> matcher);
...
public static double doubleThat(ArgumentMatcher<Double> matcher);
```

下面自定义的参数匹配器是匹配size大小为2的List： 

```
class IsListOfTwoElements extends ArgumentMatcher<List> {  
    public boolean matches(Object list) {  
        return ((List) list).size() == 2;  
    }  
}  
  
@Test  
public void argumentMatchersTest(){  
    List mock = mock(List.class);  
    when(mock.addAll(argThat(new IsListOfTwoElements()))).thenReturn(true);  
       
    mock.addAll(Arrays.asList("one", "two", "three"));  
    verify(mock).addAll(argThat(new IsListOfTwoElements()));  
}  
```
由于参数的List大小为3, 所以失败.


下面是另一个例子, 验证是否往list加入两次长度小于5的字符串:

```
// verify a list only had strings of a certain length added to it
 // note - this will only compile under Java 8
 verify(list, times(2)).add(argThat(string -> string.length() < 5));

 // Java 7 equivalent - not as neat
 verify(list, times(2)).add(argThat(new ArgumentMatcher(){
     public boolean matches(String arg) {
         return arg.length() < 5;
     }
 }));

```

说白了, xxThat(ArgumentMatcher)表示参数要满足某种条件.


## 验证行为

---

即验证确切的调用次数(具体的调用次数、至少一次，一次也没有...).

一旦创建mock将会记得所有的交互。你可以选择验证你感兴趣的任何交互.

使用verify方法可以验证调用次数的情况. verify函数声明如下:

```
public static <T> T verify(T mock);
public static <T> T verify(T mock, VerificationMode mode);

```
上面第一个方法验证指定方法是否调用一次,第二个可以指定次数.

VerificationMode: Allows verifying that certain behavior happened at least once / exact number of times / never. E.g:

```
verify(mock, times(5)).someMethod()  //5times
verify(mock, never()).someMethod();  //never
verify(mock, atLeastOnce()).someMethod();
verify(mock, atLeast(2)).someMethod();
verify(mock, atMost(3)).someMethod();
```

表示次数的方法:

方法|说明
-|:-
static VerificationMode times(int wantedNumberOfInvocations)| x次
static VerificationMode never()| 没调用过,等价于time(0)
static VerificationMode atLeastOnce()|至少一次
static VerificationMode atLeast(int minNumberOfInvocations)|至少指定次数
static VerificationMode atMost(int maxNumberOfInvocations)|最多指定次数
static VerificationMode only()|只被调用一次

例子:

```
@Test  
public void verifyInvocate(){  
      
    List<String> mockedList = mock(List.class);  
    //using mock   
     mockedList.add("once");  
     mockedList.add("twice");  
     mockedList.add("twice");  
       
     mockedList.add("three times");  
     mockedList.add("three times");  
     mockedList.add("three times");  
       
     /** 
      * 基本的验证方法 
      * verify方法验证mock对象是否有没有调用mockedList.add("once")方法 
      * 不关心其是否有返回值，如果没有调用测试失败。 
      */  
     verify(mockedList).add("once");   
     verify(mockedList, times(1)).add("once");//默认调用一次,times(1)可以省略  
       
     verify(mockedList, times(2)).add("twice");  
     verify(mockedList, times(3)).add("three times");  
       
     //never()等同于time(0),一次也没有调用  
     verify(mockedList, times(0)).add("never happened");  
     verify(mockedList, never()).add("never happened");
       
     //atLeastOnece/atLeast()/atMost()  
     verify(mockedList, atLeastOnce()).add("three times");  
     verify(mockedList, atLeast(2)).add("twice");  
     verify(mockedList, atMost(5)).add("three times");  
  
}  

```


## 模拟方法体抛出异常

---


```
@Test(expected = RuntimeException.class)  
public void doThrow_when(){  
    List list = mock(List.class);  
    doThrow(new RuntimeException()).when(list).add(1);  
    list.add(1);  
}  

```

## 验证执行顺序

---

```
@Test  
public void verification_in_order(){  
    List list = mock(List.class);  
    List list2 = mock(List.class);  
    list.add(1);  
    list2.add("hello");  
    list.add(2);  
    list2.add("world");  
    //将需要排序的mock对象放入InOrder  
    InOrder inOrder = inOrder(list,list2);  
    //下面的代码不能颠倒顺序，验证执行顺序  
    inOrder.verify(list).add(1);  
    inOrder.verify(list2).add("hello");  
    inOrder.verify(list).add(2);  
    inOrder.verify(list2).add("world");  
}  

```


## mock模拟回调

---

### 一. Answer接口

对mock对象的方法进行调用预期的设定，可以通过thenReturn()来指定返回值，thenThrow()指定返回时所抛异常，通常来说这两个方法足以应对一般的需求。

但有时我们需要自定义方法执行的返回结果，Answer接口就是满足这样的需求而存在的。另外，创建mock对象的时候所调用的方法也可以传入Answer的实例mock(java.lang.Class<T> classToMock, Answer defaultAnswer)，它可以用来处理那些mock对象没有stubbing的方法的返回值。

Answer接口如下:

```
public interface Answer<T> {
    /**
     * @param invocation the invocation on the mock.
     *
     * @return the value to be returned
     *
     * @throws Throwable the throwable to be thrown
     */
    T answer(InvocationOnMock invocation) throws Throwable;
}
```

关于InvocationOnMock:

利用InvocationOnMock提供的方法可以获取mock方法的调用信息。下面是它提供的方法：

方法|作用
-|:-
Object[] getArguments()|调用后会以Object数组的方式返回mock方法调用的参数。
Method getMethod()|返回java.lang.reflect.Method 对象
Object getMock()|返回mock对象
Object callRealMethod()|真实方法调用，如果mock的是接口它将会抛出异常


Answer使用例子:

```
public class CustomAnswer implements Answer<String> {  
    public String answer(InvocationOnMock invocation) throws Throwable {  
        Object[] args = invocation.getArguments();  
        Integer num = (Integer)args[0];  
        if( num>3 ){  
            return "yes";  
        } else {  
            throw new RuntimeException();  
        }  
    }  
}  
List<String> mock = mock(List.class);
//指定方法的返回处理类CustomAnswer，因为参数为4大于3所以返回字符串”yes”,如果参数小于4就异常而失败了.
when(mock.get(4)).thenAnswer(new CustomAnswer());

```

除了使用when(xx).thenAnswer(yy),也可以使用doAnswer:

```
doAnswer(new CustomAnswer()).when(mock.get(4));
```


### 二. 使用Answer实现回调
采用doAnswer(xxx).when(xx)或者when(xx).thenAnswer(yy)方式实现.相关代码如下:

```
 doAnswer(new Answer() {
     @Override
     public Object answer(InvocationOnMock invocation) throws Throwable {
         MyCallback callback = (MyCallback) invocation.getArguments()[0];
         callback.onSuccess(results);
         return null;
     }
 }).when(m_asyncTask).execute(any(MyCallback.class));

```
上面的基本思路是:

1. mock一个调用callback的对象.
1. 对该对象, 当执行某个方法时返回一个Answer.
1. 在Answer.answer里面, 通过参数拿到对应的callback,然后直接直接调用callback,将指定的参数传进去.当然除了通过参数拿到callback,也可以通过其他方式拿到callback.

完整例子:

```
/*****************************测试代码***********************************/
    private CallbackImpl m_callbackImp;

    @Mock
    private MyAsyncTask m_asyncTask;


    @Before
    public void setUp() {
        MockitoAnnotations.initMocks(this);
        m_callbackImp = new CallbackImpl(m_asyncTask);
    }


    @Test
    public void callbackTest(){
        Utils.printlnHead("AnswerCallbackTest::callbackTest");

        final List<String> results = Arrays.asList("One", "Two", "Three");
        doAnswer(new Answer() {
            @Override
            public Object answer(InvocationOnMock invocation) throws Throwable {
                MyCallback callback = (MyCallback) invocation.getArguments()[0];
                callback.onSuccess(results);
                return null;
            }
        }).when(m_asyncTask).execute(any(MyCallback.class));

        m_callbackImp.doAsync();

        verify(m_asyncTask, times(1)).execute(any(MyCallback.class));
        Assert.assertEquals(results, m_callbackImp.getResult());
    }
}



/*****************************实际代码***********************************/
interface MyCallback{
    void onSuccess(List<String> result);
    void onFail(int code);
}


class CallbackImpl implements  MyCallback{
    private MyAsyncTask m_task;
    private List<String> m_result;

    public CallbackImpl(MyAsyncTask task){
        m_task = task;
    }

    public List<String> getResult(){
        return m_result;
    }

    public void doAsync(){
        m_task.execute(this);
    }

    @Override
    public void onSuccess(List<String> result) {
        m_result = result;
        Utils.println("onSuccess");
    }

    @Override
    public void onFail(int code) {
        Utils.println("onFail");
    }
}


class MyAsyncTask{

    public void execute(final MyCallback callback){
        new Thread(new Runnable() {
            @Override
            public void run() {
                Utils.sleepSilent(3000);
                callback.onSuccess(Arrays.asList("1","2"));
            }
        }).start();
    }
}
```




### 三. 回调总结
todo...



## mock类的成员变量

---

有时候我们需要mock类的成员变量, 有两种方式可以mock类的成员变量, 一种是通过使用继承, 一种是使用反射.

### 一.通过使用继续的方式 
不过这种方式会受限于被Mock的类成员变量的变量定义范围，只有public及protected的才可以使用这种方式，如以下是一个第三方库的一个类：

```
public class OneClass{  
    protected TestA testA;  
    //......  
}  
```

我们这个时候可以在Mock的测试类中使用一个类来继承这个类，然后把变量通过子类的super调用传给父类：

```
private class OneClassChild extends OneClass{  
        public OneClassChild(TestA testA){  
            super.testA = testA;  
        }  
    }  

```

我们的Mock代码就可以写成这样了：

```
@Test  
public void testMock(){  
    TestA testA = new TestA();  
    OneClass oneClass = new OneClassChild(testA);  
    //假设需要调用方法callOneClassMethod，Return根据实际情况进行返回了  
    when(oneClass.callOneClassMethod()).thenReturn(0);  
    //......  
   
}  
```

这样就达到Mock了OneClass中的类成员变量testA的目的，用我们需要的返回结果，替代了真实的返回结果。

### 二.通过使用反射的方式 
如这个时候第三方类库中的类的成员变量为私有的：

```
public class OneClass{  
    private TestA testA;  
    //......  
}  
```

这个时候通过反射的方式将这个类中的成员变量给替换掉，这个时候的Mock代码就会如下：

```
@Test  
public void testMock(){  
    TestA testA = new TestA();  
    OneClass oneClass = new OneClass();  
    Field testAField = oneClass.getClass().getDeclaredField("testA");  
    testAField.setAccessible(true);  
    testAField.set(oneClass, testA);  
    //假设需要调用方法callOneClassMethod，Return根据实际情况进行返回了  
    when(oneClass.callOneClassMethod()).thenReturn(0);  
    //......  
   
}  
```

*** 使用继承需要成员变量是public或者protected, 使用反射没这个限制 ***



## 其他技巧

---

1. Stubbing 连续调用（迭代器式的 stubbing）  

    ```
    when(mock.someMethod("some arg"))
    .thenThrow(new RuntimeException())
    .thenReturn("foo");

    //First call: throws runtime exception:
    mock.someMethod("some arg");

    //Second call: prints "foo"
    System.out.println(mock.someMethod("some arg"));

    //Any consecutive call: prints "foo" as well (last stubbing wins).
    System.out.println(mock.someMethod("some arg"));
    ```

    下面是一个精简版本：

    ```
    when(mock.someMethod("some arg"))
    .thenReturn("one", "two", "three");
    ```


## mock静态类

---

使用PowerMock.

Mockito不可以实现对静态函数、构造函数、私有函数、Final 函数以及系统函数的模拟，但是这些方法往往是我们在大型系统中需要的功能。PowerMock 是在 EasyMock 以及 Mockito 基础上的扩展，通过定制类加载器等技术，PowerMock 实现了之前提到的所有模拟功能，使其成为大型系统上单元测试中的必备工具。
可以参考[PowerMock 简介-使用 PowerMock 以及 Mockito 实现单元测试](http://www.ibm.com/developerworks/cn/java/j-lo-powermock/).

## 待看的函数

---

1. when(list.get(0)).thenReturn("helloworld");  
1. doReturn(Object) )
1. doThrow(Throwable...) )
1. doThrow(Class) )
1. doAnswer(Answer) )
1. doNothing() )
1. doCallRealMethod() )



## Reference

---

1. [Unit tests with Mockito - Tutorial](http://www.vogella.com/tutorials/Mockito/article.html)
1. [API文档](http://docs.mockito.googlecode.com/hg/org/mockito/Mockito.html)
1. [学习mockito](http://wenku.baidu.com/view/8def451a227916888486d73f.html)
1. [Java Mock Frameworks Comparison](http://www.sizovpoint.com/2009/03/java-mock-frameworks-comparison.html)
1. [Mockito 简明教程](http://www.tuicool.com/articles/J7BFr2A)
1. [mockito简单教程](http://blog.csdn.net/sdyy321/article/details/38757135/)
1. [Mockito content](https://static.javadoc.io/org.mockito/mockito-core/2.2.26/org/mockito/Mockito.html)