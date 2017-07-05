
    SchedulerFactory sf = new StdSchedulerFactory();
    Scheduler sched = sf.getScheduler();


    JobBuilder
        JobDetail job = newJob(SimpleJob.class).withIdentity("job1", "group1").build();

    TriggerBuilder
        public static TriggerBuilder<Trigger> newTrigger();

    trigger = (SimpleTrigger) newTrigger().withIdentity("trigger2", "group1").startAt(startTime).build();

    ScheduleBuilder sb = new SimpleScheduleBuilder();
    trigger = newTrigger().withIdentity("trigger3", "group1").startAt(startTime)
        .withSchedule(sb).withIntervalInSeconds(10).withRepeatCount(10)).build();

    trigger = newTrigger().withIdentity("trigger6", "group1").startAt(startTime)
        .withSchedule(simpleSchedule().withIntervalInSeconds(40).repeatForever()).build();

    Date ft = sched.scheduleJob(job, trigger);

    // 手工触发job
    sched.triggerJob(JobKey.jobKey("job8", "group1"));




## Other Term


### public interface Scheduler

This is the main interface of a Quartz Scheduler.

**A Scheduler maintains a registry of JobDetails and Triggers. Once registered, the Scheduler is responsible for executing Job s when their associated Trigger s fire (when their scheduled time arrives).**

Scheduler instances are produced by a SchedulerFactory. A scheduler that has already been created/initialized can be found and used through the same factory that produced it. After a Scheduler has been created, it is in "stand-by" mode, and must have its start() method called before it will fire any Jobs.

Job s are to be created by the 'client program', by defining a class that implements the Job interface. JobDetail objects are then created (also by the client) to define a individual instances of the Job. JobDetail instances can then be registered with the Scheduler via the scheduleJob(JobDetail, Trigger) or addJob(JobDetail, boolean) method.

Trigger s can then be defined to fire individual Job instances based on given schedules. SimpleTrigger s are most useful for one-time firings, or firing at an exact moment in time, with N repeats with a given delay between them. CronTrigger s allow scheduling based on time of day, day of week, day of month, and month of year.

Job s and Trigger s have a name and group associated with them, which should uniquely identify them within a single Scheduler. The 'group' feature may be useful for creating logical groupings or categorizations of Jobs s and Triggerss. If you don't have need for assigning a group to a given Jobs of Triggers, then you can use the DEFAULT_GROUP constant defined on this interface.

Stored Job s can also be 'manually' triggered through the use of the triggerJob(String jobName, String jobGroup) function.


#### 方法

1. Date rescheduleJob(TriggerKey triggerKey, Trigger newTrigger) throws SchedulerException  

    Remove (delete) the Trigger with the given key, and store the new given one - which must be associated with the same job (the new trigger must have the job name & group specified) - however, the new trigger need not have the same name as the old trigger.



### ScheduleBuilder


    public abstract class ScheduleBuilder<T extends Trigger>
        extends Object

只有一个方法：

    protected abstract MutableTrigger build();



### SimpleScheduleBuilder

SimpleScheduleBuilder is a ScheduleBuilder that defines strict/literal interval-based schedules for Triggers.

提供了一些方法，指定按每/几秒,分,小时，毫秒触发:

    public class SimpleScheduleBuilder
        extends ScheduleBuilder<SimpleTrigger>


Sample:

    JobDetail job = newJob(MyJob.class)
                .withIdentity("myJob")
                .build();
                
    Trigger trigger = newTrigger() 
        .withIdentity(triggerKey("myTrigger", "myTriggerGroup"))
        .withSchedule(simpleSchedule()
            .withIntervalInHours(1)
            .repeatForever())
        .startAt(futureDate(10, MINUTES))
        .build();
    scheduler.scheduleJob(job, trigger);



### CronScheduleBuilder

CronScheduleBuilder is a ScheduleBuilder that defines CronExpression-based schedules for Triggers.

    public class CronScheduleBuilder
        extends ScheduleBuilder<CronTrigger>


Sample:

    JobDetail job = newJob(MyJob.class).withIdentity("myJob").build();
    
    Trigger trigger = newTrigger()
            .withIdentity(triggerKey("myTrigger", "myTriggerGroup"))
            .withSchedule(dailyAtHourAndMinute(10, 0))
            .startAt(futureDate(10, MINUTES)).build();
    
    scheduler.scheduleJob(job, trigger);

也可以这样：

    JobDetail job = newJob(MyJob.class).withIdentity("myJob").build();

    trigger = newTrigger().withIdentity("trigger3", "group2").startAt(startTime)
            .withSchedule(simpleSchedule().withIntervalInSeconds(10).withRepeatCount(2)).forJob(job).build();

    ft = sched.scheduleJob(trigger);    


CronTriggerr也可以这样创建：

    JobDetail job = newJob(SimpleJob.class).withIdentity("job1", "group1").build();
    CronTrigger trigger = newTrigger().withIdentity("trigger1", "group1").withSchedule(cronSchedule("0/20 * * * * ?"))
        .build();



###  JobKey

    public final class JobKey
        extends Key<JobKey>

Uniquely identifies a JobDetail.

Keys are composed of both a name and group, and the name must be unique within the group. If only a group is specified then the default group name will be used.



### TriggerKey

    public final class TriggerKey
        extends Key<TriggerKey>

Uniquely identifies a Trigger.

Keys are composed of both a name and group, and the name must be unique within the group. If only a name is specified then the default group name will be used.








1. [Quartz任务调度框架之触发器精讲SimpleTrigger和CronTrigger、最详细的Cron表达式范例](http://blog.csdn.net/zixiao217/article/details/53075009)



3551



R0001930465170620125



