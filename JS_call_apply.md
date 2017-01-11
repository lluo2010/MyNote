# JS call & apply区别


call和apply都是为了改变某个函数运行时的context即上下文而存在的，换句话说，就是为了改变函数体内部 this 的指向。因为 JavaScript 的函数存在「定义时上下文」和「运行时上下文」以及「上下文是可以改变的」这样的概念。

## 作用

---

call和apply是为了动态改变this而出现的, 通过call/apply将一个方法的上下文指定为另外一个对象.

比如下面:

```
function cat() {
}
cat.prototype = {
    food: "fish",
    say: function () {
        console.log("I love "+this.food);
    }
}

var blackCat = new cat;
blackCat.say();

whiteDog = {food:"bone"}  //does not have funciton say
blackCat.say.call(whiteDog);
```

但是如果我们有一个对象whiteDog = {food:"bone"},我们不想对它重新定义say方法，那么我们可以通过call或apply用blackCat的say方法：blackCat.say.call(whiteDog).

所以，可以看出call和apply是为了动态改变this而出现的，当一个object没有某个方法，但是其他的有，我们可以借助call或apply用其它对象的方法来操作。



## call和apply区别

---

call和apply的作用完全一样，只是接受参数的方式不太一样。除第一参数外, call是有几个参数接收几个, apply是接收一个数组, 如下:

```
obj.call(thisObj, arg1, arg2, ...);
obj.apply(thisObj, [arg1, arg2, ...]);
```

例如，有一个函数 func1 定义如下：

```
var func1 = function(arg1, arg2) {};
```

就可以通过下面两种方式调用:

```
func1.call(this, arg1, arg2)
```
或者

```
func1.apply(this, [arg1, arg2])
```

其中this是你想指定的上下文，它可以是任何一个JavaScript对象(JavaScript 中一切皆对象)，call 需要把参数按顺序传递进去，而 apply 则是把参数放在数组里。



## 例子

---

1. 

    ```
    function add(a,b)
    {
        alert(a+b);
    }
    function reduce(a,b)
    {
        alert(a-b);
    }
    add.call(reduce,1,3) //将add方法运用到reduce,结果为4
    ```

2. call/apply实现继承

    ```
    var Parent = function(){
        this.name = "yjc";
        this.age = 22;
    }

    var child = {};

    console.log(child);//Object {} ,空对象

    Parent.call(child);

    console.log(child); //Object {name: "yjc", age: 22}
    ```

以上实现了对象的继承。


## Reference

---

1. [如何理解和熟练运用js中的call及apply？](https://www.zhihu.com/question/20289071)