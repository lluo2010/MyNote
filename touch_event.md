# Android的Touch事件处理机制介绍


## 简介

---

Android的Touch事件处理机制比较复杂，特别是在考虑了多点触摸以及事件拦截之后。 

Android的Touch事件处理分3个层面：Activity层，ViewGroup层，View层。 
首先说一下Touch事件处理的几条基本规则:

1. 如果在某个层级没有处理ACTION_DOWN事件，那么该层就再也收不到后续的Touch事件了直到下一次ACTION_DOWN事件。   
    说明： 

    - 某个层级没有处理某个事件指的是它以及它的子View都没有处理该事件。 
    - 这条规则不适用于Activity层（它是顶层），它们可以收到每一个Touch事件。 
    - 如果没有处理ACTION_MOVE这类事件，不会有任何影响。 

1. 如果ACTION_DOWN事件发生在某个View的范围之内，则后续的ACTION_MOVE，ACTION_UP和ACTION_CANCEL等事件都将被发往该View，即使事件已经出界了。   
    第一根按下的手指触发ACTION_DOWN事件，之后按下的手指触发ACTION_POINTER_DOWN事件，中间起来的手指触发ACTION_POINTER_UP事件，最后起来的手指触发ACTION_UP事件（即使它不是触发ACTION_DOWN事件的那根手指）。 

    pointer id可以用于跟踪手指，从按下的那个时刻起pointer id生效，直至起来的那一刻失效，这之间维持不变。 
1. 如果一个ACTION_DOWN事件被父View拦截了，则任何子View不会再收到任何Touch事件了（这符合第1点的要求）。 

1. 如果一个非ACTION_DOWN事件被父View拦截了，则那些上次处理了ACTION_DOWN事件的子View会收到一个ACTION_CANCEL事件，之后不会再收到任何Touch事件了，即使父View不再拦截后续的Touch事件。 

1. 如果父View决定处理Touch事件或者子View没有处理Touch事件，则父View按照普通View的处理方式处理Touch事件，否则它根本不处理Touch事件（它只负责分发）。 

1. 如果父View在onInterceptTouchEvent中拦截了事件，则onInterceptTouchEvent中不会再收到Touch事件了，事件被直接交给它自己处理（按照普通View的处理方式）。 

下面分层讲述一些细节:


## Activity层

---

```
public boolean dispatchTouchEvent(MotionEvent ev) { 
if (ev.getAction() == MotionEvent.ACTION_DOWN) { 
onUserInteraction(); 
} 
if (getWindow().superDispatchTouchEvent(ev)) { //在这里交给View层处理 
return true; 
} 
return onTouchEvent(ev); // 如果View层没有处理，则在这里处理 
}
```


## View层

---

```
public boolean dispatchTouchEvent(MotionEvent event) { 
    
// 省略了部分细节 
ListenerInfo li = mListenerInfo; 
if (li != null && li.mOnTouchListener != null && (mViewFlags & ENABLED_MASK) == ENABLED 
&& li.mOnTouchListener.onTouch(this, event)) { 
return true; 
} 
if (onTouchEvent(event)) { 
return true; 
} 
return false; 
}

```

View的onTouch方法代码比较多，主要的逻辑分两步：

- 先是将事件交给TouchDelegate处理（如果有的话），如果TouchDelegate没有处理再自行处理；
- 自行处理主要负责View状态的变换（如按下状态），长按事件，点击事件的检测与触发等。 

## ViewGroup层（比较复杂）

---

ViewGroup层处理Touch事件的总体逻辑是：先检测是否需要拦截，没有拦截的话下发给子View处理，如果子View没有处理再自行处理，自行处理的逻辑与View一样。 

拦截的逻辑是，将从down到up之间的所有事件看作一组事件，如果从down就拦截了，则组内的后续其它事件完全交给自己处理，不需要再进入拦截逻辑了；如果是从中间拦截，则先给子View发送cancel事件，组内的后续其它事件完全交给自己处理，不需要再进入拦截逻辑了。 

分发的逻辑是，在ACTION_DOWN事件的时候，寻找子View进行处理，称为寻找Target；如果没有找到Target，则自行处理；如果找到Target，则交由Target处理。 

从代码上看，dispatchTouchEvent负责分发逻辑，onTouchEvent负责真正的处理逻辑，一般应该重载onTouchEvent，只有特殊情况下才需要重载dispatchTouchEvent。 



## Reference

---

1. [Android的Touch事件处理机制介绍](http://www.jb51.net/article/31797.htm)