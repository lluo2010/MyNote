# UML之时序图 note

## 时序图的元素

---

1. 角色（Actor）
    系统角色，可以是人、及其甚至其他的系统或者子系统。
1. 对象（Object）
    每条生命线上都关联着一个对象. 对象有三种命名方式：

    - 显示实例名和类名，方式：实例名:类名.
    - 只显示类名，方式：:类名.
    - 只显示实例名，方式：实例名.

1. 生命线（Lifeline）
    生命线在顺序图中表示为从对象图标向下延伸的一条虚线，表示对象存在的时间
1. 控制焦点（Focus of Control）
    控制焦点是顺序图中表示时间段的符号，在这个时间段内对象将执行相应的操作。用小矩形表示
1. 消息（Message）
    消息一般分为同步消息（Synchronous Message），异步消息（Asynchronous Message）和返回消息（Return Message）
1. 自关联消息（Self-Message）
    表示方法的自身调用以及一个对象内的一个方法调用另外一个方法



## 效果图

---

![时序图效果图](http://upload-images.jianshu.io/upload_images/657598-c04faf08eb79ba70.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

todo...XXX


## Reference

---

1. [UML时序图总结](http://blog.csdn.net/vipygd/article/details/9285653)
1. [StarUML时序图总结](http://blog.csdn.net/achuo/article/details/47448313)
1. [UML系列——时序图（顺序图）](http://www.jianshu.com/p/94d971dc5994)