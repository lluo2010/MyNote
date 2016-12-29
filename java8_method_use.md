
# Java8中的方法引用

Java 8中方法也是一种对象，可以用名字来引用。不过方法引用的唯一用途是支持Lambda的简写，使用方法名称来表示Lambda。

方法引用分为4类，常用的是前两种。方法引用也受到访问控制权限的限制，可以通过在引用位置是否能够调用被引用方法来判断。

具体使用方法如下:


## 引用静态方法 

---

格式: ContainingClass::staticMethodName 

例子: String::valueOf，等价的Lambda：(s) -> String.valueOf(s).

比较容易理解，和静态方法调用相比，只是把.换为::.

例子:

```
Function<String,String> fun3 = TestInfo::staticToLowerCase;
System.out.println(fun3.apply("fuAAn3_abcH"));
```


## 引用特定对象的实例方法 

---

格式: containingObject::instanceMethodName 

例子: x::toString，等价的的Lambda：() -> this.toString() 

与引用静态方法相比，都换为实例的而已

例子:

```
TestInfo info = new TestInfo();
Function<String,String> fun4 = info::toUpperCase;
System.out.println(fun4.apply("fun3_abcH"));
```


## 引用特定类型的任意对象的实例方法 

---

格式:ContainingType::methodName 

例子: String::toString，对应的Lambda：(s) -> s.toString().

这种方式不好理解,也难以维护。建议还是不要用该种方法引用。 

实例方法要通过对象来调用，方法引用对应Lambda，Lambda的第一个参数会成为调用实例方法的对象。

例子:

```
 Function<String,String> fun2 = String::toUpperCase;
 System.out.println(fun2.apply("fun2_abcH"));
 或者:
 TestInfo info1 = new TestInfo();
 Function<TestInfo,String> fun5 = TestInfo::getDescription;
 System.out.println(fun5.apply(info1));

```

也就是说, 如果要引用的是类的一般方法,而不是特殊方法, 则Function的第一个类型一定与引用的方法类型相同, 否则只能引用静态的方法.


## 引用构造函数 

---

格式:ClassName::new 

例子: String::new，对应的Lambda：() -> new String() 

构造函数本质上是静态方法，只是方法名字比较特殊。
