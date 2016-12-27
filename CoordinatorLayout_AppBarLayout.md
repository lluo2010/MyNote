# CoordinatorLayout & AppBarLayout note

## AppBarLayout

---

public class AppBarLayout   
extends LinearLayout 

AppBarLayout is a vertical LinearLayout which implements many of the features of material designs app bar concept, namely scrolling gestures.

This view depends heavily on being used as a direct child within a CoordinatorLayout. If you use AppBarLayout within a different ViewGroup, most of it's functionality will not work.

AppBarLayout 是继承LinerLayout实现的一个ViewGroup容器组件，它是为了Material Design设计的App Bar，支持手势滑动操作。
默认的AppBarLayout是垂直方向的，它的作用是把AppBarLayout包裹的内容都作为AppBar。
AppBarLayout需要做为CoordinatorLayout的子窗体，并且它里面的元素需要设置app:layout_scrollFlags属性，滑动后的导致的行为就是由这个属性决定的。

不是说AppBarlayout里面的所有子窗体一定都要设置layout_scrollFlags，是哪个如果需要在滑动时有效果的需要设置。

AppBarLayout also requires a separate scrolling sibling in order to know when to scroll. The binding is done through the AppBarLayout.ScrollingViewBehavior behavior class, meaning that you should set your scrolling view's behavior to be an instance of AppBarLayout.ScrollingViewBehavior. A string resource containing the full class name is available.

```
 <android.support.design.widget.CoordinatorLayout
         xmlns:android="http://schemas.android.com/apk/res/android"
         xmlns:app="http://schemas.android.com/apk/res-auto"
         android:layout_width="match_parent"
         android:layout_height="match_parent">

     <android.support.v4.widget.NestedScrollView
             android:layout_width="match_parent"
             android:layout_height="match_parent"
             app:layout_behavior="@string/appbar_scrolling_view_behavior">

         <!-- Your scrolling content -->

     </android.support.v4.widget.NestedScrollView>

     <android.support.design.widget.AppBarLayout
             android:layout_height="wrap_content"
             android:layout_width="match_parent">

         <android.support.v7.widget.Toolbar
                 ...
                 app:layout_scrollFlags="scroll|enterAlways"/>

         <android.support.design.widget.TabLayout
                 ...
                 app:layout_scrollFlags="scroll|enterAlways"/>

     </android.support.design.widget.AppBarLayout>

 </android.support.design.widget.CoordinatorLayout>
```




## CoordinatorLayout

---

CoordinatorLayout是一个超级强大的FrameLayout。 

CoordinatorLayout有两个主要用途： 

- 作为顶级应用程序的装饰或着色布局 
- 作为特定交互的容器，与一个或多个孩子视图交互

CoordinatorLayout作为一个父视图，它的孩子可以通过特定的行为与CoordinatorLayout别的孩子交互。 
CoordinatorLayout使得子view之间知道了彼此的存在，一个子view的变化可以通知到另一个子view，CoordinatorLayout 所做的事情就是当成一个通信的桥梁，连接不同的view，使用 Behavior 对象进行通信。

当视图类作为CoordinatorLayout的孩子时，可以定义特定的行为:

- 在xml文件中使用app:layout_behavior。 
- 在view类使用@DefaultBehavior修饰符来添加注解。


要使用CoordinatorLayout作为父布局, 相应滑动事件的控件(会显示/隐藏的控件)需要添加app:layout_scrollFlags=”xxx"属性。包裹的活动控件需要添加app:layout_behavior=”@string/appbar_scrolling_view_behavior”属性.

CoordinatorLayout的使用核心是Behavior，Behavior就是执行你定制的动作。在讲Behavior之前必须先理解两个概念：Child和Dependency.

Child当然是子View的意思了，是CoordinatorLayout的子View,其实Child是指要执行动作的CoordinatorLayout的子View(就是要被显示或者隐藏的控件),而Dependency是指Child依赖的View,及在它上面动作后触发child显示或者隐藏.


## app:layout_scrollFlags四种属性
app:layout_scrollFlags这个属性其实一般就只有下面4种使用情况:

### app:layout_scrollFlags="scroll"
- 该view显示时，只有在滚动视图顶部向上滚动时，该view才会慢慢消失。
- 该view消失后，只有在滚动视图顶部向下滚动时，该view才会慢慢出现。

### app:layout_scrollFlags="scroll|enterAlways"
- 该view显示时，只要滚动视图向上滚动，该view就会慢慢消失。
- 该view消失后，只要滚动视图向下滚动，该view就会慢慢出现。

### app:layout_scrollFlags="scroll|enterAlways|enterAlwaysCollapsed",同时该view设置了minHeight
- 该view显示时，只要滚动视图向上滚动，该view就会慢慢消失。
- 该view消失后，只要滚动视图向下滚动，该view就会慢慢出现: 
- 当滚动视图不在顶部时，该view只会慢慢出现minHeight高度的内容
- 当滚动视图在顶部时，该view会慢慢出现全部的内容

### app:layout_scrollFlags="scroll|exitUntilCollapsed",同时该view设置了minHeight
- 该view显示时，只有在滚动视图顶部向上滚动时，该view才会慢慢消失，但不会完全消失，会保留着 minHeight高度的内容依然可见。
- 该view消失后，只有在滚动视图顶部向下滚动时，该view才会慢慢出现。

注意：只有AppBarLayout第一个设置app:layout_scrollFlags属性的直接子视图可以折叠



NestedScrollView

## Reference

---

1. [CoordinatorLayout的使用如此简单](http://blog.csdn.net/huachao1001/article/details/51554608)
1. [玩转AppBarLayout，更酷炫的顶部栏](http://www.jianshu.com/p/d159f0176576)
1. [Android应用Design Support Library完全使用实例，androiddesign](http://www.bkjia.com/Androidjc/1010915.html)
1. [CoordinatorLayout Behaviors使用说明-翻译](http://www.w2bc.com/article/101849?from=extend)
1. [Material Design系列，自定义Behavior支持所有View](http://blog.csdn.net/yanzhenjie1003/article/details/52205665)
1. [Android 详细分析AppBarLayout的五种ScrollFlags](http://www.jianshu.com/p/7caa5f4f49bd)