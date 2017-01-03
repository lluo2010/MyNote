# Javascritp之条件与循环


## 条件

---

可以使用if, switch, 也支持三元运算符.

### if 

语法与C++/Java一样:

```
if (xx){
    ...
}else if (xx){
    ...
}else{
    ...
}
```

一个用处: 判断变量是否是没定义的,或者已经定义了但没有初始化或者是初始化为undefined了:

```
if (typeof someVar ==="undefined"){
    return false;
}
```

*** javascript也支持三元运算符 ?:  ***


### switch

当条件太多时,可以选择使用switch语句.

它的语法和C++差不多,但是可以支持多种类型.

例子:

```
var c = "abc"
 switch (c) {
     case 1:
         console.log("it's number 1");
         break;
     case "abc":
         console.log("it's string abc");
         break;
 }
```



## 循环

---

循环主要有下面四种类型:

- while
- do while
- for
- for-in


### while

和C++相同.

例子:

```
var i = 0;
while (i<10){
    i++;
    //todo something...
}
```


### do while

和C++相同.

```
var i =0; 
do{
    i++;
    //todo something...
}while(i<10)
```


### for 

和C++相同.

```
for (var i0; i<10;i++){
    //todo something...
}
```

### for-in

for-in用来遍历某个数组或者对象中的元素. 改循环不能直接使用for或者while代替.

它的语法和Java类似,只是在for中,Java使用":",这里使用"in".

例子:

```
var a = [3, 4, 5, 6, 100];
for (var temp in a) {
    console.log("value:" + temp);
}
```




