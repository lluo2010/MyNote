# Python 入门基础 note


## 文件结构

----
一个大致的python程序如下:

```
#coding=utf-8

import sys 

import module1

'''
a log function.
 @param str:log message 
'''
def log(str):
    print str
    pass


#main entry
if __name__ == '__main__':
    log("hello world")
    sum = module1.add(10,12)
    print "sum:%d" %(sum)
    print ('\n The python path:',sys.path)
    pass
    
```

上面包含几个内容:

- 缩进敏感: Python对于缩进敏感，相同缩进的语句被视作一个代码块. 
- 文件编码: "#coding=utf-8"标明采用utf-8编码,这样不会使用中文时出现乱码.
- 导入的module: sys为系统的module,module1为自定义的module,本例子中它是另外一个module1.py,内部有一个add函数.
- 模块导入方法: 使用import引入其他module,然后调用该模块的方法.
- 自定义函数: log为函数, 函数定义以def开头.
- 运行主入口: 使用"if __name__ == '__main__':"判断该模块是否为主入口.
- 注释: 单行注释采用#,多行使用''' ''', 其他语言常用的//,/**/都不可以使用.
- 上面的module1是一个自定义模块,而有时候我们会把多个模块放在同一个目录里,组织测一个包,包是python模块文件所在的目录，且该目录下必须存在\__init__.py文件。
- todo:上面少了类,需要补充.....

## 基础

---
1. Python对于缩进敏感，相同缩进的语句被视作一个代码块.习惯用法是4个空格表示一个缩进制表符位，而不是使用Tab。不要空格和Tab混用
1. 单行注释使用#,python本身不带多行注释, 可以使用''' '''实现多行注释. 不支持#,/**/多行注释
1. 为了避免中文注释出错, 在文件的开头可以使用下面的注释开头,指定编码方式为utf-8

    ```
    # -*- coding: UTF-8 -*-
    ```
    或者

    ```
    #coding=utf-8
    ```
1. __name__的值为模块名字
    + 如果模块是被导入，__name__的值为模块名字
    + 如果模块是被直接执行，__name__的值为’__main__’
比如你执行：
     python test.py
此时在test.py里面的name就是main,如果你执行的其他的.py,其他的py再调用test.py里的方法，则test.py里的__name__就是test.
这就是下面表示该模块是主入口的情况:

            if __name__ == "__main__":

1. 真假判断: python的布尔运算符和C有相似也稍微有些差别:

    + 任何非零数字和非空对象都认为是真。
    + 数字零，空对象，对象None都被认为是假。

1. module:
通常模块为一个文件，直接使用import来导入就好了。可以作为module的文件类型有:
    - .py
    - .pyo
    - .pyc
    - .pyd
    - .so
    - .dll
1. package: 
通常包总是一个目录,通常它包含多个模块(多个文件)，可以使用import导入包，或者from + import来导入包中的部分模块。包目录下为首的一个文件便是 \__init__.py。然后是一些模块文件和子目录，假如子目录中也有 \__init__.py 那么它就是这个包的子包了。包的文件结构大致如下:

    ```
    sound/                          Top-level package
        __init__.py               Initialize the sound package
        formats/                  Subpackage for file format conversions
                __init__.py
                wavread.py
                wavwrite.py
                aiffread.py
                aiffwrite.py
                auread.py
                auwrite.py
                ...
        effects/                  Subpackage for sound effects
                __init__.py
                echo.py
                surround.py
                reverse.py
    ```

1. import xx & from xx import xx:
两者都是导入模块, 使用前者在调用时还需要显示指定模块名,使用后者from语句可以将模块中的对象直接导入到当前的名字空间,所以调用时不需要显示指定模块名,缺点是如果其他模块也有同名方法时,会冲突. 例子如下:

    ```
    //使用import
    import sys
    print ('\n The python path',sys.path)

    //使用from xx import xx
    from sys import argv,path #导入特定的成员
    print('path:',path)

    ```
1. None是一个特殊的常量，None有自己的数据类型NoneType



## 基本数据类型

---
Python有五种基本的数据类型:

- int
- long
- bool
- float
- complex(复数)

## string

---
1. 在python中，字符串是不可改变的。
1. 引号
    + 单双引号字符串是一样的，是可以互换的，一般情况下，我们喜欢用‘’。 唯一区别是，单引号字符串里有双引号时，不需要转义，反之亦然。
    + 三重引号块主要用来编写多行文本数据，即块字符串.
1. Raw字符串:它用来关闭字符串内的转义功能。下面\t不会转成tab:

    ```
    r"C:\new\test.spm"
    ```     
1. 支持索引和分片
1. 转化
    + string转int:直接int("36")
    + string转float: float("1.5")
    + int转string:str(36)
1. 合并:有三种方式
     + 使用+:性能低，不适合大量的。
     + 字符串格式化操作符 %,如下：

        ```
        a = "hello %s, I'm %s" %("louis", "bruce")
        name = "Louis";
        b = "just a test from %s" %(name);
        c = "just a test from %s" %name;
        ```
     + join: 性能较高。如下：

        ```
        list = ["a","b","a","hjell"];
        a = "".join(list);
        ```

## 条件

---
### if
if语句后接表达式，然后用:表示代码块开始:

```
if <LogicExpression>:
    <SentenceBlock>
<OthersSetence>
```

### if-else结构
二重分支, 当条件表达式值为True时，执行if下面的语句块；否者，执行else下面的语句块.

```
if <LogicExpression>:
    <SentenceBlock_1>
else:
    <SentenceBlock_2>
<OthersSetence>
````

### if-elif-else结构
多重分支,类比C/C++语系的if-else-if结构和==switch结构.

```
if <LogicExpression_1>:
  <SentenceBlock_1>
elif <LogicExpression_2>:
  <SentenceBlock_1>
……
elif <LogicExpression_n>:
  <SentenceBlock_n>    
else:
  <SentenceBlock_default>
<OthersSetence>
```
和其他常用语言的区别是elseif变成了elif,少了se.

*** 另外不管是if还是else,后面都会多一个":". ***


## 循环

---
### for循环
1. 基本结构如下:

    ```
    for <NameCyclicVariable> in <List or Tuple>:
        <LoopBlock>
    else：
        <DefaultBlock>
    <Others>
    ````
1. 相关的:
+ 循环变量自动迭代.
+ List和Tuple可以使用已经创建的对象，或者现场创建.
+ range(<start>,<end>,<step>)可以自动生成固定步长的数列，默认步长为1.
+ else分支可选，当控制权离开循环并且未遇到break语句时执行.
+ 基本应用：对List对象和Tuple对象进行遍历.

### while循环
基本结构：

```
while <LogicExperssion>:
    <LoopBlock>
else:
    <ElseBlock>
<Others>
```

在每次进入循环时，检验逻辑表达式，当值为True时，进入循环；否则，退出循环
else分支可选，当控制权离开循环并且未遇到break语句时执行

### break语句
break语句：直接退出当前循环，主要用于非循环变量的循环终止条件判断，或特例检验。

### continue语句
continue语句：直接中止本次循环，进入下一次循环，主要用于非循环变量的循环终止条件判断，或特例检验。

### 多重循环
 + 多重嵌套
 + 注意缩进
 + 类比其他语系

*** 和其他常用语言相比, for或者while后面都多一个":". ***

## 常用数据结构

---
### list（列表）
类似数组，但是元素仍然是动态类型,即同一个List中的元素可以是不同类型.

利用append()和pop()函数,它可以实现堆栈的功能.

利用append()和pop(0)函数,它可以实现队列的功能.

1. 创建list对象
元素是动态类型,即一个List中的元素可以是不同类型. 例：

    ```
    ListName = ['a', 1, TRUE]
    ```
    空List：ListName = []

1. 正向索引与逆向索引:
索引访问List中各个元素，类比C的数组下标，即如a[0]的形式:
    + 正向：从0开始，最大为List长度减1。索引0表示List第一个元素。索引越大，表示越靠后的元素
    + 逆向：从-1开始，到List长度的负值。索引-1表示List最后一个元素，索引越小，表示越靠前的元素

1. 添加操作
    + append用来在结尾增加一个元素:

        ```
        a = [10, 20, 100, 3, 60]
        a.append(79) 
        #结果:[10, 20, 100, 3, 60, 79]
        ```
    + extend用来在结尾增加一个list:

        ```
        a = [1,2,3]
        b = [44, 88]
        a.extend(b)
        #结果:[1, 2, 3, 44, 88]  
        ```
    + insert在指定的index处插入一个元素:

        ```
        a = [10, 20, 100, 3, 60, 79, 44, 88]
        a.insert(0, 500)
        #结果:[500, 10, 20, 100, 3, 60, 79, 44, 88]    
        ```

1. 删除操作
    + 使用remove删除指定操作,如果指定值不存在,会报异常.
    + 使用pop实现删除: pop定义如下,参数可选:

        ```
        ListName.pop(<PopIndex>)
        ```
        参数两种情况:
        + 不带参数的:直接移除最后的元素.
        + 有带参数的:参数为下标,移除下标,如果该下标不存在,报异常.

        例子:

        ```

        a = [10, 20, 100, 3, 60, 79, 44, 88]
        a.pop()  #[10, 20, 100, 3, 60, 79, 44]        
        print a
        a.pop(1) 
        print a  #[10, 100, 3, 60, 79, 44]    
        ```

1. 更新操作:直接向对应位置赋值即可，例如ListName[AlterIndex] = NewElement        
1. 其他操作:

    + count(x): 获得给定值在list重复的个数。
    + sort: 排序。
    + reverse: 反序。

### tuple(元组)

tuple相当于是不可修改的list，它的很多特性和list相同,可以使用一些和list一样的方法,比如count，index等查找方法.

但是因为它是不可修改的, 所以也不具有list的很多修改的方法，例如append，insert，remove等.

1. Tuple的创建:list使用[],tuple使用(),也可以使用list定义tuple

    ```
    t2 = (2,4,8)
    #Or:
    l = [1,2,3]
    t = tuple(l)

    ```

1. 单元素tuple对象：为了消除歧义，必须在元素结尾添加英文逗号， 例：

    ```
    TupleName = (element,)
    ```
1. list类与tuple类的区别
    + 核心：list对象对象创建、对象毁灭和访问三项操作，即对于Tuple对象中的元素只能执行访问.
    + 衍生：没有添加、删除和修改元素的方法
    + 本质：tuple对象中每个元素指向不变，即指向内存空间不变，但内存空间中内容可变。即Tuple对象中实际存储的是（头）指针

### dict(字典)
用于生成一个类似字典的一一映射表,用以检索的称为key(键)，被检索的值称为value(值),如果我们平常的HashMap.

1. 创建:可以使用下面两种方法创建:
    + 基本形式

        ```
            DictName = {
            <Key_1>: Value_1>,
            <Key_2>: Value_2>,
            ……
            <Key_n>: Value_n>
            }
        ```    
    + 利用元素为tuple的list创建:

        ```
        list1 = [("louis",4),("lin",2),("peter",6)];
        dict1  = dict(list1)
        ```
        注意: 上面的list1内的元素必须是tuple.

1. 元素访问
    + 判断元素是否在dict中: 使用if key in dict1.
    + 使用dict1[key]获取value, 如果不包含key会出错.
    + 使用dict1.get(key)方法,如果不存在，则返回None.
    + 使用dict1.values()可以获取所有的元素.

1. 元素更新: 直接使用dict1[key] = value，如果这一key不存在，则自动新建这一组key-value；如果key已经存在，则更新.

1. 元素遍历:有2种方法:
    + 使用dict.keys()

        ```
        for key in dict1.keys():
            print "%s=%d" %(key,dict1[key])
        ```
    + 使用dict.items()

        ```
        for key,value in dict1.items():
            print "%s=%d" %(key,value)
        ```


### set
set具有如下特性:

+ 一种无序集合，集合中元素必须唯一. 传入元素冲突时，只计一个，系统自动去除重复.
+ 可以用in来判断指定元素是否存在于set中。
+ set中元素必须是不可变类型


1. 创建set对象:

    + setObj = set(<List>)        #其中set()函数的参数为一个List对象，可以是一个List对象名或一个List对象.  
    + setObj = {1,2,3}

1. 访问set对象: set对象为一个无序集合，不能通过索引进行访问。访问set对象中某个元素就是判断该元素是否在该set对象中.
使用Element in SetName判断是否set包含某个值，返回值为Bool类型的True或False.

1. set遍历: 基本结构如下,element获取得到Set中的元素:

    ```
    for element in SetName
    <LoopBlock>
    ```

1. set更新
    + 添加元素: SetName.add(NewElement). 如果元素已经存在，不会报错，但不会生效.
    + 删除元素: SetName.remove(SetElement). 删除之前应当检查元素是否存在在set中。如果待删除的元素不在set中则会报KeyError

1. set可以进行|，&，^,-操作。



## 函数

---
### 函数的定义
在某些编程语言当中，函数声明和函数定义是区分开的（在这些编程语言当中函数声明和函数定义可以出现在不同的文件中，比如C语言），但是在Python中，函数声明和函数定义是视为一体的, 和Java等差不多.

在Python中，函数定义的基本形式如下：

```
def function(params):
    block
    return expression/value
```

三点说明：
1. 在Python中采用def关键字进行函数的定义，后接函数标识符名称和圆括号(),后面以":"结尾,不用指定返回值的类型。
1. 函数参数params可以是零个、一个或者多个，任何传入参数和自变量必须放在圆括号中间.同样的，函数参数也不用指定参数类型，因为在Python中变量都是弱类型的，Python会自动根据值来维护其类型。
1. return语句是可选的，它可以在函数体内任何地方出现，表示函数调用执行到此结束；如果没有return语句，会自动返回NONE，如果有return语句，但是return后面没有接表达式或者值的话也是返回NONE。

### 函数的使用
在定义了函数之后，就可以使用该函数了，但是在Python中要注意一个问题，就是在Python中不允许前向引用，即在函数定义之前，不允许调用该函数。

如下,先定义printHello(),然后调用printHello. 如果先调用printHello(),然后定义,则会报错.

```
def printHello():
    print 'hello'

print printHello()
```

函数使用中的参数传递是按引用传递的.果在函数里修改了参数，那么在调用这个函数的函数里，原始的参数也被改变了。例如：

```
#!/usr/bin/python
# -*- coding: UTF-8 -*-
 
# 可写函数说明
def changeme( mylist ):
   "修改传入的列表"
   mylist.append([1,2,3,4]);
   print "函数内取值: ", mylist
   return
 
# 调用changeme函数
mylist = [10,20,30];
changeme( mylist );
print "函数外取值: ", mylist

```
最后不管是函数外还是函数内, mylist都为[10, 20, 30, [1, 2, 3, 4]].


### 参数

以下是调用函数时可使用的正式参数类型：
+ 必备参数
+ 关键字参数
+ 默认参数
+ 不定长参数

1. 必备参数: 必备参数须以正确的顺序传入函数。调用时的数量必须和声明时的一样. 它就是我们平常一般的参数传递.  
如下, 调用printme()函数，你必须传入一个参数，不然会出现语法错误：

        #!/usr/bin/python
        # -*- coding: UTF-8 -*-
        
        #可写函数说明
        def printme( str ):
        "打印任何传入的字符串"
        print str;
        return;
        
        #调用printme函数
            printme();
    以上实例输出结果：

        Traceback (most recent call last):
        File "test.py", line 11, in <module>
            printme();
        TypeError: printme() takes exactly 1 argument (0 given)

1. 关键字参数: 关键字参数和函数调用关系紧密，函数调用使用关键字参数来确定传入的参数值。  
使用关键字参数允许函数调用时参数的顺序与声明时不一致，因为 Python 解释器能够用参数名匹配参数值。
如下例:

        #!/usr/bin/python
        # -*- coding: UTF-8 -*-
        
        #可写函数说明
        def printinfo( name, age ):
        "打印任何传入的字符串"
        print "Name: ", name;
        print "Age ", age;
        return;
        
        #调用printinfo函数
        printinfo( age=50, name="miki" );
    以上实例输出结果：

        Name:  miki
        Age  50

1. 缺省参数
调用函数时，缺省参数的值如果没有传入，则被认为是默认值。下例会打印默认的age，如果age没有被传入：
        #!/usr/bin/python
        # -*- coding: UTF-8 -*-
        
        #可写函数说明
        def printinfo( name, age = 35 ):
        "打印任何传入的字符串"
        print "Name: ", name;
        print "Age ", age;
        return;
        
        #调用printinfo函数
        printinfo( age=50, name="miki" );
        printinfo( name="miki" );
    以上实例输出结果：

        Name:  miki
        Age  50
        Name:  miki
        Age  35

1. 不定长参数:不定长参数又分有名字参数和无名字参数:
    + \* 用来传递任意个无名字参数，这些参数会一个Tuple的形式访问:

            def fun1(*keys):
                print "keys type=%s" % type(keys)
                print "keys=%s" % str(keys)
                for i in range(0, len(keys)):
                print "keys[" + str(i) + "]=%s" % str(keys[i])
            fun1(2,3,4,5)
        输出：
     
            keys type=<type 'tuple'>
            keys=(2, 3, 4, 5)
    + \**用来处理传递任意个有名字的参数，这些参数用dict来访问:

            def fun2(**keys):
                print "keys type=%s" % type(keys)
                print "keys=%s" % str(keys)
                print "name=%s" % str(keys['name'])
            fun2(name="vp", age=19)
        输出以下结果：

            keys type=<type 'dict'>
            keys={'age': 19, 'name': 'vp'}

### 匿名函数
python使用lambda来创建匿名函数:

+ lambda只是一个表达式，函数体比def简单很多。
+ lambda的主体是一个表达式，而不是一个代码块。仅仅能在lambda表达式中封装有限的逻辑进去。
+ lambda函数拥有自己的命名空间，且不能访问自有参数列表之外或全局命名空间里的参数。
+ 虽然lambda函数看起来只能写一行，却不等同于C或C++的内联函数，后者的目的是调用小函数时不占用栈内存从而增加运行效率。

语法:

lambda函数的语法只包含一个语句，如下：

    lambda [arg1 [,arg2,.....argn]]:expression

如下实例：

    #!/usr/bin/python
    # -*- coding: UTF-8 -*-
    
    # 可写函数说明
    sum = lambda arg1, arg2: arg1 + arg2;
    
    # 调用sum函数
    print "相加后的值为 : ", sum( 10, 20 )
    print "相加后的值为 : ", sum( 20, 20 )

## 类

---

### 定义类
使用class语句来创建一个新类，class之后为类的名称并以冒号结尾，如下实例:

```
class ClassName:
    '类的帮助信息'   #类文档字符串
    class_suite  #类体
```

类的帮助信息可以通过ClassName.__doc__查看。 class_suite 由类成员，方法，数据属性组成。

***其实如果类没有继承关系,让它从object继承***, 如下:

```
class ClassName(object):
    '类的帮助信息'   #类文档字符串
    class_suite  #类体
```

例子:

```
#!/usr/bin/python
# -*- coding: UTF-8 -*-

class Employee:
   '所有员工的基类'
   empCount = 0

   def __init__(self, name, salary):
      self.name = name
      self.salary = salary
      Employee.empCount += 1
   
   def displayCount(self):
     print "Total Employee %d" % Employee.empCount

   def displayEmployee(self):
      print "Name : ", self.name,  ", Salary: ", self.salary
```

+ 类的成员函数都带一个self的参数,类似C++的this.
+ self.xx表示成员变量.
+ empCount变量是一个类变量，它的值将在这个类的所有实例之间共享。可以在内部类或外部类使用Employee.empCount访问。
+ \__init__()方法是一种特殊的方法，被称为类的构造函数或初始化方法，当创建了这个类的实例时就会调用该方法

### 创建实例对象和使用

如上面的类Employee, 可以使用下面方法创建:

```
"创建 Employee 类的第一个对象"
emp1 = Employee("Zara", 2000)
"创建 Employee 类的第二个对象"
emp2 = Employee("Manni", 5000)
```

上面构造函数\__init__会被调用.


### 属性
1. 访问属性  
可以使用点(.)来访问对象的属性。使用如下类的名称访问类变量,其中empCount为类成员:

    ```
    emp1.displayEmployee()
    emp2.displayEmployee()
    print "Total Employee %d" % Employee.empCount
    ```

1. Python内置类属性
    + \__dict__ : 类的属性（包含一个字典，由类的数据属性组成）
    + \__doc__ :类的文档字符串
    + \__name__: 类名
    + \__module__: 类定义所在的模块（类的全名是'__main__.className'，如果类位于一个导入模块mymod中，那么className.__module__ 等于 mymod）
    + \__bases__ : 类的所有父类构成元素（包含了一个由所有父类组成的元组）

    例子:

        #!/usr/bin/python
        # -*- coding: UTF-8 -*-

        class Employee:
        '所有员工的基类'
        empCount = 0

        def __init__(self, name, salary):
            self.name = name
            self.salary = salary
            Employee.empCount += 1
        
        def displayCount(self):
            print "Total Employee %d" % Employee.empCount

        def displayEmployee(self):
            print "Name : ", self.name,  ", Salary: ", self.salary

        print "Employee.__doc__:", Employee.__doc__
        print "Employee.__name__:", Employee.__name__
        print "Employee.__module__:", Employee.__module__
        print "Employee.__bases__:", Employee.__bases__
        print "Employee.__dict__:", Employee.__dict__

    执行以上代码输出结果如下：

        Employee.__doc__: 所有员工的基类
        Employee.__name__: Employee
        Employee.__module__: __main__
        Employee.__bases__: ()
        Employee.__dict__: {'__module__': '__main__', 'displayCount': <function displayCount at 0x10a939c80>, 'empCount': 0, 'displayEmployee': <function displayEmployee at 0x10a93caa0>, '__doc__': '\xe6\x89\x80\xe6\x9c\x89\xe5\x91\x98\xe5\xb7\xa5\xe7\x9a\x84\xe5\x9f\xba\xe7\xb1\xbb', '__init__': <function __init__ at 0x10a939578>}


### 类的继承
1. 语法:

        class SubClassName (ParentClass1[, ParentClass2, ...]):
            'Optional class documentation string'
            class_suite

    例子:

        #!/usr/bin/python
        # -*- coding: UTF-8 -*-

        class Parent:        # 定义父类
        parentAttr = 100
        def __init__(self):
            print "调用父类构造函数"

        def parentMethod(self):
            print '调用父类方法'

        def setAttr(self, attr):
            Parent.parentAttr = attr

        def getAttr(self):
            print "父类属性 :", Parent.parentAttr

        class Child(Parent): # 定义子类
        def __init__(self):
            print "调用子类构造方法"

        def childMethod(self):
            print '调用子类方法 child method'

        c = Child()          # 实例化子类
        c.childMethod()      # 调用子类的方法
        c.parentMethod()     # 调用父类方法
        c.setAttr(200)       # 再次调用父类的方法
        c.getAttr()          # 再次调用父类的方法

1. 继承中的一些特点:

    + 在继承中基类的构造（__init__()方法）不会被自动调用，它需要在其派生类的构造中亲自专门调用。
    + 在调用基类的方法时，需要加上基类的类名前缀，且需要带上self参数变量。区别于在类中调用普通函数时并不需要带上self参数
    + Python总是首先查找对应类型的方法，如果它不能在派生类中找到对应的方法，它才开始到基类中逐个查找。（先在本类中查找调用的方法，找不到才去基类中找）。

1. 方法重写  
如果父类方法的功能不能满足需求，可以在子类重写你父类的方法. 实例：

    ```
    #coding=utf-8

    class Animal(object):
        def __init__(self, name,des):
            print "Animal::__init__";
            self.name = name;
            self.des = des;


        def fun1(self):
            print "Animal::fun1";
            pass

        def fun2(self):
            print "Animal::fun2";
            pass
        
        def __str__(self):
            print "Animal,name:%s,des:%s" %(name, des)
            pass


    class Dog(Animal):
        def __init__(self, name,des):
            print "Dog::__init__";
            super(Dog,self).__init__(name,des);
            #Animal.__init__(self, name,des);
            pass

        def fun1(self):
            Animal.fun1(self);
            print "Dog::fun1";
            pass

        def fun2(self):
            print "Dog::fun2";
            pass
        
        def __str__(self):
            print "Dog,name:%s,des:%s" %(name, des)
            pass
    ```

1. 子类调用父类的方法
    + 父类名.方法名(self,XXX)
    + super(本类名,self).方法名(XXX),子类调用父类的方法.

    使用super的好处是如果继承的父类名字变了，不用改这里，只需要改继承的地方，即改一个地方。

1. 基础重载方法

    方法|描述|简单调用
    -|:-|:-
    \__init__ ( self [,args...] )|构造函数| obj = className(args)
    \__del__( self )| 析构方法, 删除一个对象| dell obj
    \__repr__( self )| 转化为供解释器读取的形式|repr(obj)
    \__str__( self )| 用于将值转化为适于人阅读的形式|str(obj)
    \__cmp__ ( self, x )| 对象比较| cmp(obj, x)

1. 类成员的访问权限
    + "单下划线" 开始的成员变量叫做保护变量，意思是只有类对象和子类对象自己能访问到这些变量；
    + "双下划线" 开始的是私有成员，意思是只有类对象自己能访问，连子类对象也不能访问到这个数据。
    + 不以"_"或"__"开头的为public成员,都可以访问.

    Python不允许实例化的类访问私有数据，但你可以使用 object._className__attrName 访问属性. "_"和"__"为我们添加的.
  
1. 其他
xx

## 文件读写

---
xx

## 切片

---
xx


## 异常处理

---
xx



## 单元测试

---
### 实现
1. 测试类从unittest.TestCase派生
1. test开始函数名以test开头
1. main函数里调用unittest.main()执行test函数

### 例子

```
import unittest
class Test(unittest.TestCase):
    def setUp(self):
        print "setup"
    def tearDown(self):
        print "tearDown"
    def testName(self):
        self.failIf(1!=1,"test name")
    def test2(self):
        self.assertEqual(1, 2, "test2")
if __name__ == "__main__":
    unittest.main()
```

setUp()和tearDown()在每个test函数执行前和执行后


## 创建自己的模块

---


## 其他

---
1. lambda表达式
    + 表达式格式:

        ```
        lambda argument1,argument2,...argumentN:expression using arguments
        ```          
    + lambda的主体是一个单个的表达式，而不是一个代码块，连if都不能包括, 这和c++不同。

        ```
        fun = lambda x,y:x+y+10
        ```          
    + 嵌套作用域:能够使用上层函数作用域中的变量，不需要像c++一样需要做说明。

1. with as: 将类似善后工作在try finally里做的做了,如下:

    ```
    with open("/tmp/foo.txt") as file:
        data = file.read()
    ```         
    close会自动调用.





## Basic Demo

---

```
#coding=utf-8

#import sys;
#import os;
import unittest

def fun1():     #global function
    pass

class Test(unittest.TestCase): #unit test class
    def setUp(self):
        pass

    def tearDown(self):
        pass

    def testName(self):
        pass


if __name__ == '__main__':
    #     unittest.main()     #start unit test
    print "finish"

```


## Reference
1. [Python Module of the Week](http://pymotw.com/2/contents.html)
1. [Learn Python The Hard Way](http://learnpythonthehardway.org/book/)
1. [Python天天美味](http://www.cnblogs.com/coderzh/archive/2008/07/08/pythoncookbook.html)
1. [Python 函数](http://www.runoob.com/python/python-functions.html)
1. [Python 面向对象](http://www.runoob.com/python/python-object.html)



