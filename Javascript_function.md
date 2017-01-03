# Javascript之函数


## 函数定义

---

以function开头, 后面不用跟返回值类型,直接跟函数名称, 函数的参数列表不需要写类型,只要名称就可以了. 如下:

```
function XXX(xxx){
    xxxx
    return a; //返回可选
}
```

比如:

```
function sum(a,b){
    return a+b;
}

function show(a,b){
    console.log(a+":"+b);
}
```

和C++/Java等的区别是:

- 必须已function开头.
- 声明上没有指明返回值类型.
- 参数列表只写名字,不加类型.

函数的返回值是可选的, 如果没有返回值, 默认返回值为undefined.



## 函数调用

---

和C++/Java等没区别. 

如下:

```
function sum(a,b){
    return a+b;
}

function show(a,b){
    console.log(a+":"+b);
}

var a = sum(1,2);
console.log("result:"+a);   //3,
var b = show(1,2);
console.log("result:"+b);   //undefined
```


## 函数参数

---

对于函数的参数,在调用时,可以参数可以多传几个,也可以少传几个.少传的会自动被设置为undefined,多传的也能进来.js其实是不管传进来的参数是否和定义的时候是否一致的.

对于下面的函数:

```
function testArg(a,b,c){
    console.log("first:"+a);
    console.log("second:"+b);
    console.log("third:"+c);

    var actualLength = arguments.length;
    console.log("actual len:"+actualLength);
}

function testArgsFunction(){
    testArg(1,2);
    testArg(1,2,3);
    testArg(1,2,3,4,5);
}
```

1. 参数少传了,没传的参数自动设为undefined了.

    如下, 则最后一个参数为undefined:

    ```
    testArg(1,2);
    ```

1. 参数多传了, 多传的参数也能进来.
    如下, 4,5也都能传进来:

    ```
    testArg(1,2,3,4,5);
    ```

1. arguments: 这是一个类似数组的对象,它包含了函数被调用是接收到的所有参数.  
    通过上面的例子可以看出来, arguments包含的是全部传进来的参数. 所以比如下面,就可以通过arguments对所有传进来的参数进行统计:

    ```
    function sumEx(a,b,c){
        var len = arguments.length;
        var result = 0;
        for (var i=0; i<len; i++){
            result += arguments[i];
        }

        return result;
    }

    var result = sumEx(1,2,3,4,5,6);
    console.log("result:"+result);
    ```

    尽管声明时时三个参数,但是传进来是6个,实际也把后面都算进来了.



## 预定义函数

---

1. parseInt()

    parseInt会将输入的任何值(通常为字符串)转化为整数类型, 如果转化失败, 则返回NaN.

    例子:

    ```
    var reslt = parseInt("123");        //123
    console.log("result:"+reslt);

    var reslt = parseInt("abc123");     //NaN
    console.log("result:"+reslt);

    var reslt = parseInt("12a34");      //12
    console.log("result:"+reslt);
    ```

    在转化的过程中, 遇到转化异常就停止, 之前的值还会留下来, 所以上面的"12a34"的结果为12.

    parseInt还有第二个可选参数radix, 表示的是第一个参数期望的数字类型-十进制,十六进制还是二进制.

    如:

    ```
    var reslt = parseInt("FF",16);      //255
    console.log("result:"+reslt);
    ```

    如果没有第二个参数, 默认按十进制来, 但是下面两种情况例外:

    - 如果字符串参数以0x开头,则默认为十六进制.
    - 如果字符串参数以0开头,则默认为八进制.

1. parseFloat()  
    parseFloat与parseInt()类似, 只是它转化为十进制的float数.

    例子:

    ```
    var result = parseFloat("123"); //123
    console.log("result:"+result);

    var result = parseFloat("1.23");//1.23
    console.log("result:"+result);

    var result = parseFloat("123.4abc");//123.4
    ```

1. isNaN()  
    判断输入参数是否是一个可以参与算数运算的数字, 不是数字就返回false. 因此,该函数也可以用来判断parseInt和parseFloat的调用成功与否.

    例子:

    ```
    var result = isNaN(1.9);
    console.log("result:" + result); //false

    var result = isNaN(3);
    console.log("result:" + result);//false

    var result = isNaN(parseInt("abc1"));//true
    console.log("result:" + result);
    ```

    函数也始终将接收到的输入转化为数字, 如:

    ```
    result = isNaN("123");
    console.log("result:" + result);    //false
    ```
    输入字符串"123"会先被转化为123, 所以最后的输出为false.

1. isFinite()  
    用来检查输入是否是一个既非infinity,又非NaN的数字.

    例子:

    ```
    var result = isFinite(4);       //true
    console.log("result:"+resul
    var result = isFinite(Infinity);    //false
    console.log("result:"+result);
    var result = isFinite(-Infinity);   //false
    console.log("result:"+resul
    var result = isFinite(NaN);     //false
    console.log("result:"+result);
    ```

1. URI的编码与反编码  
    encodeURI和encodeURIComponent用来编码, decodeURI和decodeURIComponent用来反编码. encodeURI返回一个完整的可用的URL,encodeURIComponent则认为我们传递的仅仅是URL的一部分.

1. eval()  
    eval是一个函数, eval(xx)会将输入字符串当做javascript代码来执行.

    如下, "var i=10;"会动态执行, 后面的日志能打印出i:

    ```
    eval("var i=10;");
    console.log("testEval, value:"+i);
    ```

    从性能方面讲, 它是一种由函数执行的"动态"代码, 效率显然要低一些.

1. alert()  
    它不是ECMA标准中的函数,而是由宿主环境浏览器所提供,用于显示文本消息的对话框.


## 变量的作用域

---

变量没办法指定块作用域,但是可以定义其函数作用域.

如下, for中定义的变量,除了for还可以用,所以说没有块作用域:

```
function testVar(){
    for (var i=0; i<10; i++){
        var a = 100;
    }
    console.log(i); //不是undefined,是10
    console.log(a); //不是undefined,是100
}
```

*** 所以,对于变量,最好在函数的一开头就定义它. ***


## 匿名函数

---

todo...XXX



## 回调函数

---

todo...XXX



## 自调函数

---

todo...XXX



## 闭包

---

todo...XXX

