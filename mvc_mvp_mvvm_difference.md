
# MVC, MVP和MVVM比较


## 概述

---

Model用于封装与应用程序的业务逻辑相关的数据以及对数据的处理方法。 View视图层负责数据的展示。

M－V－X的本质是差不多的的，重点在于X作为桥梁来连通M,V来使得整个系统良好运转。 X的模式之间的不同主要是所确定的M与V的数据传递流程不同。流程的不同主要体现在：

1. 传统的MVC中三者之间都有交互，View(界面)触发事件—>Controller(业务)处理了业务，然后触发了数据更新—>更新了Model的数据—>Model(带着数据)回到了View—>View更新数据，形成闭环，这样关系维护略显复杂；
1. MVP则是使用P截断了M与V之间的联系，降低了复杂程度，指责结构简单明了；
1. MVVM则是不仅简化了业务与界面的依赖关系，还优化了数据频繁更新的解决方案，甚至可以说提供了一种有效的解决模式，但是这必须依赖于特有的情景，更多的是框架提供的一种能力，例如Android平台最近支持的Data Binding。


## MVC

---

MVC，Model View Controller，是软件架构中最常见的一种框架，简单来说就是通过controller的控制去操作model层的数据，并且返回给view层展示.

MVC中View和Modle没有解耦,可以相互通讯.Controller是M和V之间的连接器，用于控制应用程序的流程。它处理事件并作出响应。“事件”包括用户的行为和数据模型上的改变。

大致过程如下:

1. View接受用户的交互请求
1. View将请求转交给Controller
1. Controller操作Model进行数据更新
1. 数据更新之后，Model通知View数据变化
1. View显示更新之后的数据 

![MVC](http://zjutkz.net/images/%E9%80%89%E6%8B%A9%E6%81%90%E6%83%A7%E7%97%87%E7%9A%84%E7%A6%8F%E9%9F%B3%EF%BC%81%E6%95%99%E4%BD%A0%E8%AE%A4%E6%B8%85MVC-MVP%E5%92%8CMVVM/mvc.png)



## MVP

---

MVP 模式将 Controller 改名为 Presenter，同时改变了通信方向。View和Model的通讯被切断了,都是通过Presenter传递的.

这样view层和model层不再相互可知，完全的解耦.

![MVP](http://image.beekka.com/blog/2015/bg2015020109.png)

1. 各部分之间的通信，都是双向的。
1. View 与 Model 不发生联系，都通过 Presenter 传递。
1. View 非常薄，不部署任何业务逻辑，称为"被动视图"（Passive View），即没有任何主动性，而 Presenter非常厚，所有逻辑都部署在那里。



## MVVM

---

MVVM 模式将 Presenter 改名为 ViewModel，Modle与View的通讯业是切断的.

view-model 一词的确不能充分表达我们的意图. 一个更好的术语可能是 “View Coordinator”(感谢Dave Lee提的这个 “View Coordinator” 术语, 真是个好点子)。你可以认为它就像是电视新闻主播背后的研究人员和作家团队。它从必要的资源(数据库, 网络服务调用, 等)中获取原始数据, 运用逻辑, 并处理成 view (controller) 的展示数据. 它(通常通过属性)暴露给视图控制器需要知道的仅关于显示视图工作的信息(理想地你不会暴漏你的 data-model 对象)。 它还负责对上游数据的修改(比如更新模型/数据库, API POST 调用)。


![MVVM](http://zjutkz.net/images/%E9%80%89%E6%8B%A9%E6%81%90%E6%83%A7%E7%97%87%E7%9A%84%E7%A6%8F%E9%9F%B3%EF%BC%81%E6%95%99%E4%BD%A0%E8%AE%A4%E6%B8%85MVC-MVP%E5%92%8CMVVM/mvvm.png)

它采用双向绑定（data-binding）：View的变动，自动反映在 ViewModel，反之亦然。

## 区别

---

1. MVC与MVP比较

    MVP与MVC之间最主要的区别在控制层上:
    - 在MVP框架中，View与Model并不直接交互，所有的交互放在Presenter中. 而在MVC里，View与Model会直接产生一定的交互。
    - MVP的Presenter是框架的控制者，承担了大量的逻辑操作，而MVC的Controller更多时候承担一种转发的作用。因此在App中引入MVP的原因，是为了将此前在Activty中包含的大量逻辑操作放到控制层中，避免Activity的臃肿。

1. MVP和MVVM比较

    MVVM比MVP更升级一步，在MVP中，V是接口IView, 解决对于界面UI的耦合; 而MVVM干脆直接使用ViewModel和UI无缝结合, ViewModel内数据的变化直接触发UI上的变化. 但是MVVM做到这点是要依赖具体的平台和技术实现的(比如双向数据绑定)，比如WPF和knockoutjs, 这也就是为什么ViewModel不需要实现接口的原因，因为对于具体平台和技术的依赖，本质上使用MVVM模式就是不能替换UI的使用平台的.

## 各自缺点

---


### MVC缺点

M和V是直接联系的,存在耦合.

像在android/ios中, Activity或者iOS的UIController,既是View又是Control. 结果导致这一层很厚,逻辑很复杂, 不易测试.


### MVP缺点

如果View页面复杂, 会导致View和P直接交互的接口很巨大, View上的回调也特别多, 这样导致Presenter很大.


### MVVM缺点
todo...XXX


## Reference

---

1. [选择恐惧症的福音！教你认清MVC，MVP和MVVM](http://zjutkz.net/2016/04/13/%E9%80%89%E6%8B%A9%E6%81%90%E6%83%A7%E7%97%87%E7%9A%84%E7%A6%8F%E9%9F%B3%EF%BC%81%E6%95%99%E4%BD%A0%E8%AE%A4%E6%B8%85MVC%EF%BC%8CMVP%E5%92%8CMVVM/)
1. [MVC, MVP, MVVM比较以及区别-Android](http://blog.csdn.net/shareus/article/details/50814054)
1. [MVC，MVP 和 MVVM 的图示](http://www.ruanyifeng.com/blog/2015/02/mvcmvp_mvvm.html)





