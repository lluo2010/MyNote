# JUnit4 note

## JUnit规范

---
总结的规范如下, 分必须的和建议的:

### 必须的
1. 测试方法必须使用@Test进行修饰
1. 测试方法必须使用public void进行修饰，不能带任何参数.

### 建议的
1. 新建一个源代码目录来存放我们的测试代码，一般使用test.
1. 测试类的包应该和被测试类包保持一致.
1. 测试类使用Test作为类名的后缀：被测试类名+Test.java.
1. 测试方法使用test作为方法名的前缀：test+被测试方法名()


## JUnit常用注解

---
常用注解如下:

1. @Test: 将一个普通方法修饰成为一个测试方法，其包括两个可选属性:
    + @Test(expected=异常类.class): 验证方法能否抛出预期的错误,如果没抛出,则失败.

            @Test(expected = ArithmeticException.class)
            public void zeroDivTest() throws Exception {
                int result = mCalculator.div(8,0);
            }
    + @Test(timeout=毫秒): 指定在规定时间内执行该测试方法, 时间超过了,方法结束,认为测试失败. 

            @Test(timeout = 2000)
            public void longTimeCal() throws Exception {
                boolean bResult = mCalculator.longTimeCal(3);
                Assert.assertTrue(bResult);
            }

1. @BeforeClass: 修饰static的方法，在整个测试类执行之前执行该方法一次.
1. @AfterClass: 修饰static的方法，在整个类执行结束前执行一次。如果你用@BeforeClass创建了一些资源现在是时候释放它们了。
1. @Before: 修饰public void的方法，在每个测试用例（方法）执行时都会执行。
1. @After: 修饰public void的方法，在每个测试用例执行结束后执行。
1. @Ignore: 对于已经表示为@Test的测试方法, 有时候我们想临时忽略它,即不去执行该测试用例, 则可以在@Test同时加上@Ignore.
1. @RunWith：可以更改测试运行器org.junit.runner.Runner,一般使用默认的,即BlockJUnit4ClassRunner.
1. @Parameters: 用于使用参数化功能。

## 断言Assert

---
Assert几种类型的方法,用来判断结果是否符合期望的,如果不符合期望,会抛出AssertionError,ArrayComparisonFailure等Error, 测试用例失败.

对于每个断言方法,都会提供一个对应的提供一个message的方法,这个message会加到AssertionError中.

方法中如果有一个期望值和一个实际值的,期望值在前,实际值在后.

常用方法如下:

1. assertEquals  
    断言对象中存的值是否是期待的值，与字符串比较中使用的equals()方法类似.如果两个都是null,也认为相同.
1. assertFalse()和assertTrue()  
    断言条件为false或者true.
1. assertSame()和assertNotSame()  
断言两个对象的引用是否相等和不相等，类似于通过“==”和“!=”比较两个对象. Asserts that two objects refer to the same object
1. assertNull()和assertNotNull()  
    用来查看对象是否为空和不为空。
1. AssertThat(T actual, org.hamcrest.Matcher<T> matcher)  
    使用Matcher做自定义的校验。
1. assertArrayEquals  
    断言两个比较的数组是否是相等的.
1. fail()/fail([String message,])  
    直接让测试用例失败,可以有消息，也可以没有消息。


## 几个术语

---

### 1.Runner
Runner是一个抽象类，是JUnit的核心组成部分。用于运行测试和通知Notifier运行的结果。JUnit使用@RunWith注解标注选用的Runner，由此实现不同测试行为。 

BlockJUnit4ClassRunner：这个是JUnit的默认Runner，平时我们编写的JUnit不添加@RunWith注解时使用的都是这个Runner。 

具体信息:

```
public class BlockJUnit4ClassRunner
extends ParentRunner<org.junit.runners.model.FrameworkMethod>
```
>Implements the JUnit 4 standard test case class model, as defined by the annotations in the org.junit package. Many users will never notice this class: it is now the default test class runner, but it should have exactly the same behavior as the old test class runner (JUnit4ClassRunner). BlockJUnit4ClassRunner has advantages for writers of custom JUnit runners that are slight changes to the default behavior, however:


### 2.Suit
Suit就是个Runner！用来执行分布在多个类中的测试用例，比如有DateUtilTest和CalculateTest类分别测试某种相关的功能，如果我们还有很多其他的测试类,如何只运行和这个功能相关的测试列呢? 可以使用Suit。

TestSuitMain就是一个Suit,它只运行DateUtilTest和CalculateTest中的测试用例. TestSuitMain使用@RunWith(Suite.class)修饰,用@SuiteClasses指明只包含之前的两个测试类, TestSuitMain本身不实现任何的方法:

```
@RunWith(Suite.class)
@SuiteClasses({DateUtilTest.class, CalculateTest.class})
public class TestSuitMain
{
}

public class CalculateTest {
    ...
    @Test
    public void normalDivTest() throws Exception {
        int result = mCalculator.div(8,4);
        Assert.assertEquals(2,result);
    }

    @Test(expected = ArithmeticException.class)
    public void zeroDivTest() throws Exception {
        int result = mCalculator.div(8,0);
    }
}

/*****************************DateUtilTest.java******************************/
public class DateUtilTest {
    ...

    @Test
    public void isSameDayTest() throws Exception {
        ...
    }
}

```

### 3.Category

Category同样继承自Suit，Category似乎是Suit的加强版，它和Suit一样提供了将若干测试用例类组织成一组的能力，除此以外它可以对各个测试用例进行分组，使你有机会只选择需要的部分用例。


todo:xxx...

## Rule

---
todo:xxx...


## Assume

---
todo:xxx...

## JUnit测试套件Suit使用

---
可以将所有被测试方法放到一个测试类中，进行批量运行测试类
1）测试套件就是组织测试类一起运行的
a)写一个作为测试套件的入口类，这个类不包含其他的方法，
b)更改测试运行期Suite.class
c)将要测试的类作为数组传入到Suite.SuiteClasses({})中

例子:

```
@RunWith(Suite.class)
@Suite.SuiteClasses({CalculateTest.class, DateUtilTest.class})
public class CalDateSuiteTest {
}
```

## 参数化设置@RunWith(Parameterized.class）
在JUnit中, Parameterized（参数化）的测试运行器允许你使用不同的参数多次运行同一个测试.

---
todo:xxx


## 总结

---
1. 测试方法使用@Test标注,方法必须是public void的,而且没有参数.
1. 几种注解:
    + @Test: 标明该方法是一个测试方法.
    + @Test(Timeout=毫秒): 规定该测试方法在规定时间内完成,如果超时了,就失败了.
    + @Test(expected=异常类):断言方法会抛出指定的异常,如果未抛出,则失败.
    + @Ignore: 临时忽略测试方法, 使标注为@Test的方法不运行.
    + @RunWith: 做一些指定的工作.
    + @BeforeClass/@AfterClass修饰的方法为public static的,在整个测试类执行之前/之后只执行该方法一次.
    + @Before/@After:方法为public void, 在每个测试方法执行之前/之后执行一次.
    + @fail()/fail([String message,]): 直接让测试用例失败,可以有消息，也可以没有消息。
    + @RunWith(Suite.class): 指明该类为Suite类,指明运行哪些测试类, 该类本身为空.
    + 参数化设置@RunWith（Parameterized.class）
1. Assert断言:
    + assertEquals: 断言对象中存的值是否是期待的值，与字符串比较中使用的equals()方法类似
    + assertFalse()和assertTrue(): 断言条件为false或者true.
    + assertSame()和assertNotSame(): 断言两个对象的引用是否相等和不相等，类似于通过“==”和“!=”比较两个对象.
    + assertNull()和assertNotNull(): 用来查看对象是否为空和不为空。
    + AssertThat(T actual, org.hamcrest.Matcher<T> matcher): 使用Matcher做自定义的校验。
    + assertArrayEquals: 断言两个比较的数组是否是相等的.
    + fail()/fail([String message,]): 直接让测试用例失败,可以有消息，也可以没有消息。

1. Suite测试套件类: 类本身为空, 指定运行哪些测试类. 用@RunWith(Suite.class)修饰,用@Suite.SuiteClasses指定运行哪些测试类.

## 例子

```
/*****************************CalculateTest.java******************************/
public class CalculateTest {
    private Calculate mCalculator;

    @Before
    public void setUp() throws Exception {
        System.out.println("CalculateTest setup()");
        mCalculator = new Calculate();
    }

    @After
    public void tearDown() throws Exception {
        System.out.println("CalculateTest tearDown()");
    }

    @Test
    public void add() throws Exception {
        int result = mCalculator.add(1,2);
        Assert.assertEquals(3,result);
    }

    @Test
    public void sub() throws Exception {
        int result = mCalculator.sub(9,1);
        Assert.assertEquals(8,result);
    }

    @Test
    public void normalDivTest() throws Exception {
        int result = mCalculator.div(8,4);
        Assert.assertEquals(2,result);
    }

    @Test(expected = ArithmeticException.class)
    public void zeroDivTest() throws Exception {
        int result = mCalculator.div(8,0);
    }

    @Test(timeout = 2000)
    public void longTimeCal() throws Exception {
        boolean bResult = mCalculator.longTimeCal(3);
        Assert.assertTrue(bResult);
    }

}



/*****************************DateUtilTest.java******************************/
public class DateUtilTest {
    private DateUtil mDateUtil;
    @Before
    public void setUp() throws Exception {
        mDateUtil = new DateUtil();
    }

    @After
    public void tearDown() throws Exception {

    }

    @Test
    public void isSameDay() throws Exception {
        System.out.println("isSameDay test");
        long nTime = Calendar.getInstance().getTimeInMillis();
        long nTime2 = nTime + 24*60*60*1000+1;

        boolean bSameDay = mDateUtil.isSameDay(nTime,nTime2);
        Assert.assertFalse(bSameDay);

    }

}



/*****************************CalDateSuiteTest.java******************************/
@RunWith(Suite.class)
@Suite.SuiteClasses({CalculateTest.class, DateUtilTest.class})
public class CalDateSuiteTest {
}
```


## Reference

---
1. [JUnit框架功能详细——JUnit学习（一）](https://my.oschina.net/pangyangyang/blog/144495)
1. [JUnit4使用教程-快速入门](http://blog.csdn.net/chenleixing/article/details/44259453)
1. [JUnit 参数化测试](http://www.cnblogs.com/zhangweihui/articles/4641063.html)






