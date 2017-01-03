# Javascript之数组

数组的类型为object.



## 定义

---

可以采用如下定义, 不需要像别的语言一样需要变量名后加"[]",而是直接变量名a:

```
    var a = [2,3,4,5];
```

数组内的元素可以是不同类型的值, 这和Java/C等语言是不一样的.

如:

```
var a1 = [1, "hello", [1,2,3], "world"];
console.log(a1);
```

输出:

```
[ 1, 'hello', [ 1, 2, 3 ], 'world' ]
```


## 访问元素

---

使用索引访问, 索引从0开始.  访问不存在的元素(越界),返回值为undefined, 不会crash.

如:

```
var a = [2,3,4,5];
var first = a[0];
var v100 = a[99]; //the value is undefined
```



## 增加/更新元素

---

直接对数组对应索引的元素赋值.

例子:

```
var array = [0, 1, 2, 3, 4];
array[0] = 100;
console.log("%s,len:%d", array, array.length);
array[7] = 7; //add new item
console.log("%s,len:%d", array, array.length);
console.log(array[6]);  //it's undefined
```

输出:

```
100,1,2,3,4,len:5
100,1,2,3,4,,,7,len:8
undefined
```

说明:

- 如果已经存在了, 为更新
- 如果不存在, 则添加新的元素.
- 如果插入位置的前几个元素是不存在的, 则都补上undefined.



## 元素删除

---

使用delete, 长度不会变,只是将对应的位置设为undefined.

例子:

```
var a = [3,4,5];
delete a[1]
```

结果变为[3, undefined,5]



## 数组中的数组

---

数组中可以存放数组, 访问数组中的数组元素可以使用[][].

```
var a1 = [1, "hello", [1, 2, 3], "world"]; //
console.log(a1); //[ 1, 'hello', [ 1, 2, 3 ], 'world' ]
var temp = a1[2][0]; //it's 1
```

可以使用访问数组元素的方式访问某个字符串中的某个特定字符.

如:

```
var str = "012";
console.log(str[0]); //it's '0'
console.log(str[1]); //it's '1'

```


## 总结

---

- 数组是一种数据存储形式, 类型为object.
- 数组元素是可以被索引的.
- 数组中元素的索引是从0开始.
- 数组能存放任何不同类型的数据.
- 数组元素的删除采用delete, 长度不会变,只是把删除的位置设为undefined.