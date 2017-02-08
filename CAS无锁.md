# CAS实现无锁


## 概述

---

实现无锁（lock-free）的非阻塞算法有多种实现方法，其中 CAS（比较与交换，Compare and swap） 是一种有名的无锁算法。

CAS的语义是**“我认为V的值应该为A，如果是，那么将V的值更新为B，否则不修改并告诉V的值实际为多少”**，CAS是一种 乐观锁技术，**当多个线程尝试使用CAS同时更新同一个变量时，只有其中一个线程能更新变量的值，而其它线程都失败，失败的线程并不会被挂起，而是被告知这次竞争中失败，并可以再次尝试**。

CAS有3个操作数，内存值V，旧的预期值A，要修改的新值B。当且仅当预期值A和内存值V相同时，将内存值V修改为B，否则什么都不做。

CAS是以原子操作为基础，采用事务->提交->提交失败->重试这样特定编程手法的机制，它使得正在访问共享资源的线程不依赖于任何其它线程的调度和执行，并且能够在有限的步骤内完成。



## 什么是CAS原子操作

---

在研究无锁之前，我们需要首先了解一下CAS原子操作——Compare & Set，或是 Compare & Swap，** 现在几乎所image有的CPU指令都支持CAS的原子操作，X86下对应的是 CMPXCHG 汇编指令**。

大家应该还记得操作系统里面关于“原子操作”的概念，一个操作是原子的(atomic)，如果这个操作所处的层(layer)的更高层不能发现其内部实现与结构。原子操作可以是一个步骤，也可以是多个操作步骤，但是其顺序是不可以被打乱，或者切割掉只执行部分。有了这个原子操作这个保证我们就可以实现无锁了。

一个线程的失败或者挂起不应该影响其他线程的失败或挂起的算法。现代的CPU提供了特殊的指令，可以自动更新共享数据，而且能够检测到其他线程的干扰，而 compareAndSet() 就用这些代替了锁定。

CAS原子操作在维基百科中的代码描述如下:

```
int compare_and_swap(int* reg, int oldval, int newval)
{
  ATOMIC();
  int old_reg_val = *reg;
  if (old_reg_val == oldval)
     *reg = newval;
  END_ATOMIC();
  return old_reg_val;
}
```

上面的代码里有ATOMIC(), END_ATOMIC(), 但我感觉CPU指令支持的CAS院子操作应该不是采取锁的方式的, 否则就不叫无锁了, 上面的两个函数表示的之间的操作是原子的, 实现方式应该还是无锁的,至于用什么方式, 那是各平台自己的事了.

也就是检查内存*reg里的值是不是oldval，如果是的话，则对其赋值newval。上面的代码总是返回old_reg_value，调用者如果需要知道是否更新成功还需要做进一步判断，为了方便，它可以变种为直接返回是否更新成功，如下：

```
bool compare_and_swap (int *accum, int *dest, int newval)
{
  if ( *accum == *dest ) {
      *dest = newval;
      return true;
  }
  return false;
}
```




## JVM对CAS的支持

---

提供了AtomicInt, AtomicLong.incrementAndGet().

在JDK1.5之前，如果不编写明确的代码就无法执行CAS操作，在JDK1.5中引入了底层的支持，在int、long和对象的引用等类型上都公开了CAS的操作，并且JVM把它们编译为底层硬件提供的最有效的方法，在运行CAS的平台上，运行时把它们编译为相应的机器指令，**如果处理器/CPU不支持CAS指令，那么JVM将使用自旋锁**。

**也就是说CAS一般需要平台处理器/CPU的支持, 如果不支持, Java使用自旋转锁实现.**

在原子类变量中，如java.util.concurrent.atomic中的AtomicXXX，都使用了这些底层的JVM支持为数字类型的引用类型提供一种高效的CAS操作，而在java.util.concurrent中的大多数类在实现时都直接或间接的使用了这些原子变量类。


看下AtomicInteger实现:


```
public final int incrementAndGet() {  
    for (;;) {  
        //这里可以拿到value的最新值  
        int current = get();  
        int next = current + 1;  
        if (compareAndSet(current, next))  
            return next;  
       }  
}  

public final boolean compareAndSet(int expect, int update) {  
    //使用unsafe的native方法，实现高效的硬件级别CAS  
    return unsafe.compareAndSwapInt(this, valueOffset, expect, update);  
}  

```

从上面可以看出incrementAndGet里面使用了自旋锁(for循环),for循环里调用了compareAndSet(), compareAndSet函数调用了native实现的硬件级别的CAS方法.

**从上面我们可以知道CAS只是进行原子操作, 也有可能其他线程在这个过程中正在操作而导致这次原子操作请求失败(获取的当前值和期望值不同), 如果失败可以使用自旋不停去尝试, 即一次CAS操作请求有可能成功也有可能失败.**


## 高并发环境下优化锁或无锁（lock-free）的设计思路

高并发环境下要实现高吞吐量和线程安全，两个思路：

1. 一个是用优化的锁实现.
1. 一个是lock-free的无锁结构。

但非阻塞算法要比基于锁的算法复杂得多。开发非阻塞算法是相当专业的训练，而且要证明算法的正确也极为困难，不仅和具体的目标机器平台和编译器相关，而且需要复杂的技巧和严格的测试。虽然Lock-Free编程非常困难，但是它通常可以带来比基于锁编程更高的吞吐量。所以Lock-Free编程是大有前途的技术。它在线程中止、优先级反转以及信号安全等方面都有着良好的表现。

- 优化锁实现的例子 ：Java中的ConcurrentHashMap，设计巧妙，用桶粒度的锁和锁分离机制，避免了put和get中对整个map的锁定，尤其在get中，只对一个HashEntry做锁定操作，性能提升是显而易见的。
- vLock-free无锁的例子 ：CAS（Compare-And-Swap）的利用和LMAX的disruptor 无锁消息队列数据结构等。例如ConcurrentLinkedQueue。


## Reference

---

1. [锁与CAS介绍](http://blog.csdn.net/zero__007/article/details/43572777)
1. [CAS原子操作实现无锁及性能分析**](http://blog.csdn.net/chen19870707/article/details/41083183)
1. [非阻塞同步算法与CAS(Compare and Swap)无锁算法](http://www.cnblogs.com/Mainz/p/3546347.html?utm_source=tuicool&utm_medium=referral)