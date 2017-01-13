# Javascript 原型 study


## 几个概念

---

1. 什么是对象

    若干属性的集合

1. 什么是原型

    原型是一个对象，其他对象可以通过它实现继承。

1. 哪些对象有原型

    所有的对象在默认情况下都有一个原型，因为原型本身也是对象，所以每个原型自身又有一个原型（只有一种例外，默认的对象原型在原型链的顶端）,任何一个对象都可以成为原型.


除了创建对象，构造函数(constructor) 还做了另一件有用的事情—自动为创建的新对象设置了原型对象(prototype object) 。原型对象存放于 ConstructorFunction.prototype 属性中。


ECMAScript标准中规定,一个对象的内部属性[[Prototype]]指向自己的原型.在ECMAScript 5中,该属性是不能被直接读取或修改的,但是可以通过Object.getPrototypeOf()间接的读取到它.



## constructor 属性

---

constructor属性始终指向创建当前对象的构造函数。

例子:

```
var arr=[1,2,3];
.console.log(arr.constructor); //输出 function Array(){}
.var a={};
.console.log(arr.constructor);//输出 function Object(){}
.var bool=false;
.console.log(bool.constructor);//输出 function Boolean(){}
.var name="hello";
.console.log(name.constructor);//输出 function String(){}
.var sayName=function(){}
.console.log(sayName.constrctor)// 输出 function Function(){}
.
.//接下来通过构造函数创建instance
.function A(){}
.var a=new A();
.console.log(a.constructor); //输出 function A(){}

```
以上部分即解释了任何一个对象都有constructor属性，指向创建这个对象的构造函数



## prototype属性

---

注意：prototype是每个函数对象都具有的属性，被称为原型对象，而非函数的普通对象无法读到prototype(读出来为undefined), 而__proto__属性才是每个对象才有的属性。一旦原型对象被赋予属性和方法,那么由相应的构造函数创建的实例会继承prototype上的属性和方法.

```
//constructor : A
//instance : a
function A(){}
var a=new A();

A.prototype.name="xl";
A.prototype.sayName=function(){
    console.log(this.name);
}

console.log(a.name);// "xl"
a.sayName();// "xl"
    
```
那么由constructor创建的instance会继承prototype上的属性和方法


每个函数都有一个默认的属性prototype，而这个prototype的constructor默认指向这个函数


## constructor属性和prototype属性

---

每个函数都有prototype属性，而这个prototype的constructor属性会指向这个函数。

每个函数都有一个默认的属性prototype，而这个prototype的constructor默认指向这个函数, 见下面列子:

```
var Person = function(name)  
{  
    this.name = name ;  
};  
  
var p = new Person("Ben");  

console.log(p.constructor === Person);//true  
console.log(Person.prototype.constructor === Person);  //true  
console.log(Person.prototype instanceof  Object);  //true  
console.log(Person.prototype instanceof  Person);  //false  
```

当通过XXXX.prototyp.xxx=yyy去设置prototype里面的各项属性时, XXXX.prototype就是它自己,所以下面输出(4)就是它自己, 而XXXX.prototype.constructor(输出(5))就指向了它自己的构造函数, 代码如下:

```
function Person(name){
       this.name=name;
}
Person.prototype.sayName=function(){
    console.log(this.name);
}

var person=new Person("xl");

console.log(person.constructor);        //输出(1) function Person(){}
console.log(Person.constructor);        //输出(2):function Function(){}
console.log(person.prototype);          //输出(3):undefined
console.log(Person.prototype);          //输出(4):Person { sayName: [Function] }
console.log(Person.prototype.constructor);  //输出(5):function Person(){}
```

如果通过XXXX.prototype={a:"tt"}这样的方式设置prototype, Person.prototype输出为"{ sayName: [Function: sayName] }",Person.prototype.constructor为Function: Object, 具体见下面:

```
Person.prototype = {
    sayName: function () {
        console.log(this.name);
    }
}
console.log(person.prototype);              //输出(1):undefined
console.log(Person.prototype);              //输出(2):{ sayName: [Function: sayName] }
console.log(Person.prototype.constructor);  //输出(3)[Function: Object]
```
其实上面的原型赋值相当于下面,这样就好理解了:

```
Person.prototype=new Object(){
    sayName:function(){
        console.log(this.name);
    }
}
```

当设置函数的原型(prototype)后, 再去new XXX(), 它的construtor不再指向它的构造函数,而是指向它的原型的构造函数.

如下, person1.constructor为Person(),但是person2.constructor已经变为原型的构造函数Object():

```
function Person() {
        console.log("Person");
    }
    var person1 = new Person();

    Person.prototype = {
        sayName: function () {
            console.log("sayName");
        }
    }
    var person2 = new Person();

    console.log(person1.constructor == Person); //输出true
    console.log(person2.constructor == Person); //输出false   
    console.log("person1 constructor:"+person1.constructor); //输出:function Person() { console.log("Person"); } 
    console.log("person2 constructor:"+person2.constructor); //输出:function Object()
}
```

因为person实例继承了Person.prototype原型对象的所有的方法和属性，包括constructor属性。当Person.prototype的constructor发生变化的时候，相应的person实例上的constructor属性也会发生变化.



## \__proto__

---

简单来说，在 javascript 中每个对象都会在内部初始化一个属性__proto__, 它指向改对象构造器的prototype.

当我们访问一个对象的属性时，如果这个对象内部不存在这个属性，那么他就会去 __proto__ 里找这个属性，这个 __proto__ 又会有自己的 __proto__，于是就这样一直找下去，也就是我们平时所说的原型链的概念。

__proto__最终的指向都是Object.prototype，这也就是js中的原型链。

按照标准，__proto__ 是不对外公开的，也就是说是个私有属性，但是Firefox的的引擎却将他暴露了出来成为了一个公有属性，我们可以对其进行访问和赋值。但 ie 浏览器是不能访问这个属性的，所以不推荐大家直接操作这个属性，以免造成浏览器兼容问题。


下面两段代码是等价的, 第二段代码使用"__proto__"将p和Person关联.

```
var Person = function() {};
Person.prototype.say = function() {
    alert("Person say");
}
var p = new Person();
p.say(); // Person say
```

```
var Person = function() {};
Person.prototype.say = function() {
    alert("Person say");
}
var p = {};
p.__proto__ =  Person.prototype;
Person.call(p);
p.say(); // Person say
```

对于上面的代码, 当我们调用 p.say() 时，p 中是没有 say 属性，于是到 p 的 __proto__ 属性中去找，也就是 Person.prototype，而我们定义了 Person.prototype.say=function(){}; 于是，p 在 Person.prototype 中就找到了这个方法。 



## 为什么会继承原型对象的方法

---

因为ECMAscript的发明者为了简化这门语言，同时又保持继承性，采用了链式继承的方法。

由constructor创建的每个instance都有个__proto__属性，它指向constructor.prototype。那么constrcutor.prototype上定义的属性和方法都会被instance所继承. 也就是说通过构造方法创建的对象, 都包含属性__proto__,它指向构造函数的prototype, 即constructor.prototype, 所以constrcutor.prototype上的属性和方法都会被该对象继承.

在 javascript 中每个对象都会有一个 __proto__ 属性，当我们访问一个对象的属性时，如果这个对象内部不存在这个属性，那么他就会去 __proto__ 里找这个属性，这个 __proto__ 又会有自己的 __proto__，于是就这样一直找下去，也就是我们平时所说的原型链的概念。



## 总结

---

The following table lists properties of the Object Object:

Property|Description
-|-
\__proto__ Property  | Specifies the prototype for an object.
constructor Property| Specifies the function that creates an object.
prototype Property  | Returns a reference to the prototype for a class of objects.


Javascript objects treasure map:

![Javascript objects treasure map](http://pic002.cnblogs.com/images/2012/322503/2012072014243377.png)



## Reference

---

1. [constructor, prototype, __proto__ 详解](https://segmentfault.com/a/1190000003017751)
1. [JavaScript: __proto__](http://www.cnblogs.com/ziyunfei/archive/2012/10/05/2710955.html)
1. [简单介绍 javascript 中 __proto__ 属性的原理](http://blog.csdn.net/shi0090/article/details/45008595)
1. [JavaScript探秘：强大的原型和原型链](http://www.nowamagic.net/librarys/veda/detail/1648)
1. [js原型链原理看图说明](http://www.jb51.net/article/30750.htm)
1. [理解JavaScript原型](http://blog.jobbole.com/9648/)
1. [JavaScript类和继承：constructor属性](http://developer.51cto.com/art/200907/134913.htm)
1. [JavaScript探秘：构造函数 Constructor](http://www.nowamagic.net/librarys/veda/detail/1642)
1. [Javascript中的__proto__、prototype、constructor](http://blog.csdn.net/lmj623565791/article/details/24423881)