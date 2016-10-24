# Python 入门基础 note


## 文件结构
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

- 锁紧敏感: Python对于缩进敏感，相同缩进的语句被视作一个代码块.
- 文件编码: "#coding=utf-8"标明采用utf-8编码,这样不会使用中文时出现乱码.
- 导入的module: sys为系统的module,module1为自定义的module,本例子中它是另外一个module1.py,内部有一个add函数.
- 模块导入方法: 使用import引入其他module,然后调用该模块的方法.
- 自定义函数: log为函数, 函数定义以def开头.
- 运行主入口: 使用"if __name__ == '__main__':"判断该模块是否为主入口.
- 注释: 单行注释采用#,多行使用''' ''', 其他语言常用的//,/**/都不可以使用.
- 上面的module1是一个自定义模块,而有时候我们会把多个模块放在同一个目录里,组织测一个包,包是python模块文件所在的目录，且该目录下必须存在__init__.py文件。
- todo:上面少了类,需要补充.....

## 基础
1. Python对于缩进敏感，相同缩进的语句被视作一个代码块.
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

        ```
            if __name__ == "__main__":
        ```    


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
通常包总是一个目录,通常它包含多个模块(多个文件)，可以使用import导入包，或者from + import来导入包中的部分模块。包目录下为首的一个文件便是 __init__.py。然后是一些模块文件和子目录，假如子目录中也有 __init__.py 那么它就是这个包的子包了。包的文件结构大致如下:

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
Python有五种基本的数据类型:

- int
- long
- bool
- float
- complex(复数)

## string
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


## 常用数据结构
### List（列表）
类似数组，但是元素仍然是动态类型,即同一个List中的元素可以是不同类型.

利用append()和pop()函数,它可以实现堆栈的功能.

利用append()和pop(0)函数,它可以实现队列的功能.

1. 创建List对象
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

        ```
        例子:

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

### Tuple(元组)

tuple相当于是不可修改的list，它的很多特性和list相同,可以使用一些和list一样的方法,比如count，index等查找方法.

但是因为它是不可修改的, 所以也不具有list的很多修改的方法，例如append，insert，remove等.

1. Tuple的创建:list使用[],tuple使用(),也可以使用list定义tuple

    ```
    t2 = (2,4,8)
    #Or:
    l = [1,2,3]
    t = tuple(l)

    ```

1. 单元素Tuple对象：为了消除歧义，必须在元素结尾添加英文逗号， 例：

    ```
    TupleName = (element,)
    ```
1. List类与Tuple类的区别
    + 核心：list对象对象创建、对象毁灭和访问三项操作，即对于Tuple对象中的元素只能执行访问.
    + 衍生：没有添加、删除和修改元素的方法
    + 本质：Tuple对象中每个元素指向不变，即指向内存空间不变，但内存空间中内容可变。即Tuple对象中实际存储的是（头）指针

### Dict(字典)
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








### Set




## 函数

## 类


## 文件读写

## 异常处理



## 单元测试
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

setUp()和tearDown()在每个test函数执行前和执行后调用


## 创建自己的模块


## 其他
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
2. [Learn Python The Hard Way](http://learnpythonthehardway.org/book/)
3. [Python天天美味](http://www.cnblogs.com/coderzh/archive/2008/07/08/pythoncookbook.html)