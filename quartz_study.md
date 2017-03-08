
# Quartz study note


Quartz是一个开源的作业调度包，能够运行在几乎任何java项目中，小到单机应用，大到电商系统。Quartz能够创建很容易的调度，也可以创建十个、百个、千个、甚至万个任务的复杂调度。Quartz将任务定义成java组件，能够执行几乎任何你定义的事情。Quartz也支持许多企业级特性，比如JTA和集群。

Quartz的核心是Scheduler、Job、Trigger。Job负责定义需要执行的任务，Trigger负责调度策略，Scheduler负责将二者组合。

todo...XXX



## 三个核心

---

Quartz提出了三个核心概念－－任务、触发器和调度器。

1. 任务(job)－顾名思义就是到了某个时间段，我们要执行某个任务。比如生成报表的任务。
1. 触发器(trigger)－既然任务有了，那么什么时间段触发呢？这个时候就需要有一个触发器。例如每个月月初执行生成报表任务，这就是触发器，触发时间是我们定义的触发规则。
1. 调度器(scheduler)－我们既然有了任务，也有了触发器，那么我们要怎么将这两个绑定在一起呢？这个时候就需要调度器。我们可以简单的理解成，调度器就是将一个触发器绑定到一个任务上，让触发器被触发后执行对应的任务。注意：调度器是由工厂创建的。


## 大致的使用过程

1. 从Job接口派生定义要做的任务.
1. 获取Scheduler.
1. 使用JobBuilder创建JobDetail, 和Job关联,并传入相应的参数.
1. 在ScheduleBuilder里指定任务的运行规则.
1. 使用TriggerBuild创建Trigger, 和ScheduleBuilder关联.
1. 调用scheduler.scheduleJob将trigger和job关联.
1. 调用scheduler.start()启动Scheduler.

例子如下:

```
public class HelloJob implements Job {
    public void execute(JobExecutionContext context) throws JobExecutionException {
        Date date = new Date();
        String dateStr = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(date);

        JobDataMap jobDataMap = context.getJobDetail().getJobDataMap();
        String para = jobDataMap.getString("para");
        System.out.println(para + " - " + dateStr);
}

public static void main(String[] args) {
    try {
        Scheduler scheduler = StdSchedulerFactory.getDefaultScheduler();
        String jobName = "HelloJob";
        String jobGroup = Scheduler.DEFAULT_GROUP;

        JobDetail jobDetail = JobBuilder.newJob(HelloJob.class).withIdentity(jobName, jobGroup).build();
        JobDataMap jobDataMap = jobDetail.getJobDataMap();
        jobDataMap.put("para", "hello");//job执行的参数

        //创建触发器：每5秒执行一次，共执行3+1次
        SimpleScheduleBuilder builder = SimpleScheduleBuilder.simpleSchedule().withIntervalInSeconds(5).withRepeatCount(3);
        Trigger trigger = TriggerBuilder.newTrigger().withIdentity(jobName, jobGroup).startNow().withSchedule(builder).build();

        scheduler.scheduleJob(jobDetail, trigger);
        scheduler.start();//启动scheduler
    } catch (SchedulerException e) {
        e.printStackTrace();
    }       
 }
}

```



## 一些概念简介

---

常用的一些类概况如下:

类|功能作用
-|-
Job|是一个接口,有一个execute()方法,相应的任务都实现改接口
JobDetail|包含了Job对应的信息,Job只是表示了它要做什么,但是相关的信息都是存储在JobDetails里的. 它是由JobBuilder创建的. 
JobBuilder|用来创建JobDetail
Scheduler|用来执行job任务
Trigger|表示任务的触发规则, 分为SimpleTrigger与CronTrigger.
SimpleTrigger|当仅需触发一次或者以固定时间间隔周期执行,使用它.
CronTrigger|它使用Cron表达式. 用来处理类似如每早晨9:00执行，周一、周三、周五下午5:00执行等场景.
TriggerBuilder|用来创建Trigger.



具体如下:

### Job

Job is an interface to be implemented by components that you wish to have executed by the scheduler. It has only one simple method:

```
  public interface Job {

    public void execute(JobExecutionContext context)
      throws JobExecutionException;
  }
```

开发者实现该接口定义运行任务，JobExecutionContext类提供了调度上下文的各种信息。Job运行时的信息保存在JobDataMap实例中.


### JobDetail

used to define instances of Jobs. Conveys the detail properties of a given Job instance. JobDetails are to be created/defined with JobBuilder. Quartz does not store an actual instance of a Job class, but instead allows you to define an instance of one, through the use of a JobDetail.

The JobDetail object is created by the Quartz client (your program) at the time the Job is added to the scheduler. It contains various property settings for the Job, as well as a JobDataMap, which can be used to store state information for a given instance of your job class. It is essentially the definition of the job instance, an

Quartz在每次执行Job时，都重新创建一个Job实例，所以它不直接接受一个Job的实例，相反它接收一个Job实现类，以便运行时通过newInstance()的反射机制实例化Job。因此需要通过一个类来描述Job的实现类及其它相关的静态信息，如Job名字、描述、关联监听器等信息，JobDetail承担了这一角色。

也就是说, Job只是表示了它要做什么,但是相关的信息都是存储在JobDetails里的. 它是由JobBuilder创建的.

下面的代码演示了通过JobBuilder创建JobDetail, 通过newJob将Job和JobDetail相关联. 相关的参数通过JobDataMap保存, Job接口可以通过execute(JobExecutionContext context)的参数取出来:

```
public class HelloJob implements Job {
    public void execute(JobExecutionContext context) throws JobExecutionException {
        ...
        JobDataMap jobDataMap = context.getJobDetail().getJobDataMap();
        String para = jobDataMap.getString("para");
        ...
}

JobDetail jobDetail = JobBuilder.newJob(HelloJob.class).withIdentity(jobName, jobGroup).build();
JobDataMap jobDataMap = jobDetail.getJobDataMap();
jobDataMap.put("para", "hello");//job执行的参数
```

关于JobDataMap:

The JobDataMap can be used to hold any amount of (serializable) data objects which you wish to have made available to the job instance when it executes. JobDataMap is an implementation of the Java Map interface, and has some added convenience methods for storing and retrieving data of primitive types.

它是一个可Serializable的map. 通过它保存Job的一些信息. 它被放在了JobDetail里, 在Job中通常我们通过JobExecutionContext的getJobDetail().getJobDataMap()获取它.

创建JobDetail时有两种办法可以往里存放东西:

1. 使用usingJobData:

    ```
    // define the job and tie it to our DumbJob class
    JobDetail job = newJob(DumbJob.class)
        .withIdentity("myJob", "group1") // name "myJob", group "group1"
        .usingJobData("jobSays", "Hello World!")
        .usingJobData("myFloatValue", 3.141f)
        .build();
    ```

1. 获取JobDataMap后调用put加入:

    ```
    JobDetail jobDetail = JobBuilder.newJob(HelloJob.class).withIdentity(jobName, jobGroup).build();
    JobDataMap jobDataMap = jobDetail.getJobDataMap();
    jobDataMap.put("para", "hello");//job执行的参数
    ```


### JobBuilder

used to define/build JobDetail instances, which define instances of Jobs. 它使用来创建JobDetail的. 

例子如下:

```
JobDetail jobDetail = JobBuilder.newJob(HelloJob.class).withIdentity(jobName, jobGroup).build();
```


### Scheduler

the main API for interacting with the scheduler.

A Scheduler maintains a registry of JobDetails and Triggers. Once registered, the Scheduler is responsible for executing Job s when their associated Trigger s fire (when their scheduled time arrives).

代表一个Quartz的独立运行容器，Trigger和JobDetail可以注册到Scheduler中，两者在Scheduler中拥有各自的组及名称，组及名称是Scheduler查找定位容器中某一对象的依据，Trigger的组及名称必须唯一，JobDetail的组和名称也必须唯一（但可以和Trigger的组和名称相同，因为它们是不同类型的）。Scheduler定义了多个接口方法，允许外部通过组及名称访问和控制容器中Trigger和JobDetail。

### StdSchedulerFactor 

An implementation of SchedulerFactory that does all of its work of creating a QuartzScheduler instance based on the contents of a Properties file.
用来创建Scheduler.

例子如下:

```
Scheduler scheduler = StdSchedulerFactory.getDefaultScheduler();
```


### SimpleScheduleBuilder & CronScheduleBuilder

SimpleScheduleBuilder is a ScheduleBuilder that defines strict/literal interval-based schedules for Triggers.

CronScheduleBuilder is a ScheduleBuilder that defines CronExpression-based schedules for Triggers.

创建trigger时候指定它们.

比如:

```
SimpleScheduleBuilder builder = SimpleScheduleBuilder.simpleSchedule().withIntervalInSeconds(5).withRepeatCount(3);
Trigger trigger = TriggerBuilder.newTrigger().withIdentity(jobName, jobGroup).startNow().withSchedule(builder).build();
```


### Trigger

a component that defines the schedule upon which a given Job will be executed. Trigger objects are used to trigger the execution (or ‘firing’) of jobs.

描述触发Job执行的时间触发规则。主要有SimpleTrigger和CronTrigger这两个子类。当仅需触发一次或者以固定时间间隔周期执行，SimpleTrigger是最适合的选择；而CronTrigger则可以通过Cron表达式定义出各种复杂时间规则的调度方案：如每早晨9:00执行，周一、周三、周五下午5:00执行等；

Trigger属性:

1. The “jobKey” property indicates the identity of the job that should be executed when the trigger fires.
1. The “startTime” property indicates when the trigger’s schedule first comes into affect. 
1. The “endTime” property indicates when the trigger’s schedule should no longer be in effect.

Trigger可以设置优先级.

又分为SimpleTrigger与CronTrigger.


#### SimpleTrigger

SimpleTrigger is handy if you need ‘one-shot’ execution (just single execution of a job at a given moment in time), or if you need to fire a job at a given time, and have it repeat N times, with a delay of T between executions. 

当仅需触发一次或者以固定时间间隔周期执行，SimpleTrigger是最适合的选择.


如下, 使用TriggerBuilder创建trigger, 使用SimpleScheduleBuilder指定为SimepleTrigger:

```
SimpleScheduleBuilder builder = SimpleScheduleBuilder.simpleSchedule().withIntervalInSeconds(5).withRepeatCount(3);
Trigger trigger = TriggerBuilder.newTrigger().withIdentity(jobName, jobGroup).startNow().withSchedule(builder).build();
```


#### CronTrigger

CronTrigger is useful if you wish to have triggering based on calendar-like schedules - such as “every Friday, at noon” or “at 10:15 on the 10th day of every month.”

它使用Cron表达式. 用来处理类似如每早晨9:00执行，周一、周三、周五下午5:00执行等场景.

如下:

```
String cron = "0 55 14 * * ?";
CronScheduleBuilder scheduleBuilder = CronScheduleBuilder.cronSchedule(cron);
// 按照cron表达式构建一个新的trigger
CronTrigger trigger = TriggerBuilder.newTrigger().withIdentity(jobName, jobGroup).withSchedule(scheduleBuilder).build();
```


### TriggerBuilder

used to define/build Trigger instances.

用来创建Trigger的.


### Calendar
org.quartz.Calendar和java.util.Calendar不同，它是一些日历特定时间点的集合（可以简单地将org.quartz.Calendar看作java.util.Calendar的集合).

接口如下:

```
public interface Calendar {
  public boolean isTimeIncluded(long timeStamp);
  public long getNextIncludedTime(long timeStamp);
}
```

一个Trigger可以和多个Calendar关联，以便排除或包含某些时间点。假设，我们安排每周星期一早上10:00执行任务，但是如果碰到法定的节日，任务则不执行，这时就需要在Trigger触发机制的基础上使用Calendar进行定点排除。

比如下面, Job在每天上午9:30执行,但是排除节假日, 所以使用了Calendar:

```
HolidayCalendar cal = new HolidayCalendar();
cal.addExcludedDate( someDate );
cal.addExcludedDate( someOtherDate );
sched.addCalendar("myHolidays", cal, false);

Trigger t = newTrigger()
    .withIdentity("myTrigger")
    .forJob("myJob")
    .withSchedule(dailyAtHourAndMinute(9, 30)) // execute job daily at 9:30
    .modifiedByCalendar("myHolidays") // but not on holidays
    .build();

```

![quartz图](http://upload-images.jianshu.io/upload_images/1945306-396fecf2b8b1547c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



## quartz.properties文件

---

todo..XXX



## TriggerListeners and JobListeners

---

todo...XXX



## SchedulerListeners

---




## Reference 

---

1. [quartz详解2：quartz由浅入深](http://blog.csdn.net/guolong1983811/article/details/51501346)
1. [Quartz Doc](http://www.quartz-scheduler.org/api/2.1.7/index.html)
1. [基于 Quartz 开发企业级任务调度应用](https://www.ibm.com/developerworks/cn/opensource/os-cn-quartz/)
1. [几种任务调度的 Java 实现方法与比较**](https://www.ibm.com/developerworks/cn/java/j-lo-taskschedule/)
1. [Quartz tutorial](http://www.quartz-scheduler.org/documentation/quartz-2.1.x/tutorials/)
1. [Quartz Cookbook](http://www.quartz-scheduler.org/documentation/quartz-2.1.x/cookbook/)



## Tutorial

---

1. [任务调度框架Quartz](http://www.jianshu.com/p/33be5d612aa1)
1. [Quartz-任务调度](http://www.jianshu.com/p/164fbbdd0449)
1. [Quartz调度框架](http://www.jianshu.com/p/b15467c20a5c)