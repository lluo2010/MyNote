
# Javascript之基本数据类型


## 变量的使用

---

和其他编程语言一样，可以先声明后初始化，也可以声明的同时也初始化.

如：

```
var v1;
v1 = 2;

var v2 = 2;
var f1 = 13.8;
```

*** 注意：变量区分大小写. ***



## 基础数据类型

---

数据类型分为基本的数据类型和非基本的数据类型（对象）.

基础数据类型有如下五种：

- 数字：包括浮点数和整数
- 字符串
- 布尔值: true or false
- undefined: 访问未初始化或者不存在的变量都会返回undefined
- null: 表示有值，空值，不表示任何东西。与undefined的最大区别是，被赋以null的变量通常被认为是已经定义的，只不过不代表任何东西。

任何不属于上述五种基本类型的变量的值都被认为是一个对象。有时候我们也把null视为对象。

## 类型操作符-typeof

---

返回一个表示数据类型的字符串。它的值包括"number"，"string"，"boolean","undefined","object","function".


## 字符串

---

使用双引号“”或者单引号‘’将其他字符包含在中间。 使用加号'+'可以将几个字符串连接起来。


### 字符串转化
当使用带有数字的字符串作为运算操作符的操作数时，该字符串会被当作数字操作。

比如：

```
 var str1 = "100";
 var result = str1*1;
 console.log("type:%s, value:%d", typeof(result), result)
```

结果如下， 可以看出用于运算操作符中的操作数后， 得到的结果为数字，而不是字符串：

```
type:number, value:100
```

*** 于是字符串转数字有了一种偷懒的方法， 字符串*1. ***

当然，如果转化失败，则会返回NaN.

对于特殊字符串， 比如“\n”,"\t", 则需要使用转义字符‘\’.


## 布尔值

---

布尔值包含true和false.

对于其他非布尔值,除了下面列出的6个特殊的值外， 其他值在转为布尔值时都为true:

- 空字符串""
- 数字0
- 数字NaN
- null
- undefined
- 布尔值false


## 其他

---

1. Infinity表示最大数，-Infinity表示最小数。typeof(Infinity)返回“number”.
2. NaN:表示特殊类型的数字， 当对数字类型执行操作失败时，返回NaN.
1. null和undefined在转为其他类型时的区别:

    - 转为数字时， null为0， undefined为NaN
    - 转为布尔值时都为false.
    - 转为字符串时，null为"null",undefined为"undefined"
