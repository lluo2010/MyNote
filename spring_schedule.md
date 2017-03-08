# Spring 任务调度 note


## 几种方案



## Quartz


### 三个核心

Quartz提出了三个核心概念－－任务、触发器和调度器。

1. 任务－顾名思义就是到了某个时间段，我们要执行某个任务。比如生成报表的任务。
1. 触发器－既然任务有了，那么什么时间段触发呢？这个时候就需要有一个触发器。例如每个月月初执行生成报表任务，这就是触发器，触发时间是我们定义的触发规则。
1. 调度器－我们既然有了任务，也有了触发器，那么我们要怎么将这两个绑定在一起呢？这个时候就需要调度器。我们可以简单的理解成，调度器就是将一个触发器绑定到一个任务上，让触发器被触发后执行对应的任务。注意：调度器是由工厂创建的。



### 一些概念

1. Job
1. JobDetail
1. JobBuilder 
1. JobDataMap: 一个可Serializable的map. Holds state information for Job instances.通常我们通过JobExecutionContext的getJobDetail().getJobDataMap()获取它.

1. Scheduler
1. StdSchedulerFactor 
1. SimpleScheduleBuilder
1. Trigger
1. TriggerBuilder




## Quartz在spring中的使用








## Reference 

---

1. [Quartz Doc](http://www.quartz-scheduler.org/api/2.1.7/index.html)
1. [基于 Quartz 开发企业级任务调度应用](https://www.ibm.com/developerworks/cn/opensource/os-cn-quartz/)
1. [几种任务调度的 Java 实现方法与比较**](https://www.ibm.com/developerworks/cn/java/j-lo-taskschedule/)
1. [Quartz tutorial](http://www.quartz-scheduler.org/documentation/quartz-2.1.x/tutorials/)
1. [Quartz Cookbook](http://www.quartz-scheduler.org/documentation/quartz-2.1.x/cookbook/)



## Tutorial

---

1. [任务调度框架Quartz](http://www.jianshu.com/p/33be5d612aa1)
1. [Quartz-任务调度](http://www.jianshu.com/p/164fbbdd0449)








