# Spring cache note



Spring 3.1 引入了激动人心的基于注释（annotation）的缓存（cache）技术，它本质上不是一个具体的缓存实现方案（例如 EHCache 或者 OSCache），而是一个对缓存使用的抽象，通过在既有代码中添加少量它定义的各种 annotation，即能够达到缓存方法的返回对象的效果。

Spring 的缓存技术还具备相当的灵活性，不仅能够使用 SpEL（Spring Expression Language）来定义缓存的 key 和各种 condition，还提供开箱即用的缓存临时存储方案，也支持和主流的专业缓存例如 EHCache 集成。

其特点总结如下：

1. 通过少量的配置 annotation 注释即可使得既有代码支持缓存
1. 支持开箱即用 Out-Of-The-Box，即不用安装和部署额外第三方组件即可使用缓存
1. 支持 Spring Express Language，能使用对象的任何属性或者方法来定义缓存的 key 和 condition
1. 支持 AspectJ，并通过其实现任何方法的缓存支持
1. 支持自定义 key 和自定义缓存管理者，具有相当的灵活性和扩展性



## 几个主要的注释概述

---

### @EnableCaching

使用@EnableCaching启用Cache注解支持.

The caching feature can be declaratively enabled by simply adding the @EnableCaching annotation to any of the configuration classes.

如下:


```
@Configuration
// 标注启动了缓存
@ComponentScan(basePackages = "com.luo.test.cache")
@EnableCaching
public class MyCacheConfig{

    /*
     * 据shared与否的设置,Spring分别通过CacheManager.create()或new CacheManager()方式来创建一个ehcache基地.
     */
    @Bean
    public EhCacheManagerFactoryBean ehCacheManagerFactoryBean(){
        EhCacheManagerFactoryBean cacheManagerFactoryBean = new EhCacheManagerFactoryBean ();
        cacheManagerFactoryBean.setConfigLocation (new ClassPathResource("ehcache.xml"));
        cacheManagerFactoryBean.setShared (true);
        return cacheManagerFactoryBean;
    }


    /*
     * ehcache 主要的管理器
     */
    @Bean(name = "appEhCacheCacheManager")
    public EhCacheCacheManager ehCacheCacheManager(EhCacheManagerFactoryBean bean){
        return new EhCacheCacheManager (bean.getObject ());
    }
}
```
or

```
@Configuration
@EnableCaching
public class CachingConfig {
 
    @Bean
    public CacheManager cacheManager() {
        return new ConcurrentMapCacheManager("addresses");
    }
}
```


当然, 我们也可以使用配置文件激活:

```
<beans>
    <cache:annotation-driven />
    <bean id="cacheManager" class="org.springframework.cache.support.SimpleCacheManager">
        <property name="caches">
            <set>
                <bean
                  class="org.springframework.cache.concurrent.ConcurrentMapCacheFactoryBean"
                  name="addresses"/>
            </set>
        </property>
    </bean>
</beans>
```


### @Cacheable

应用到读取数据的方法上，即可缓存的方法上.表示调用该方法时, 先从缓存中读取，如果缓存中没有再调用方法获取数据，然后把数据添加到缓存中. 如果缓存中有就不会执行该函数后面的获取动作.

如:

```
@Cacheable(value="accountCache")// 使用了一个缓存名叫 accountCache 
   public Account getAccountByName(String userName) { 
     // 方法内部实现不考虑缓存逻辑，直接实现业务
     System.out.println("real query account."+userName); 
     return getFromDB(userName); 
   } 
  
   private Account getFromDB(String acctName) { 
     System.out.println("real querying db..."+acctName); 
     return new Account(acctName); 
   } 
```
上面注释的意思是，当调用这个方法的时候，会从一个名叫 accountCache 的缓存中查询，如果没有，则执行实际的方法（即查询数据库），并将执行的结果存入缓存中，否则返回缓存中的对象。这里的缓存中的 key 就是参数 userName，value 就是 Account 对象。** “accountCache”缓存是在 spring*.xml 中定义的名称, 就是cache的名称**. 对于ehCache,它就是ehCache的配置文件ehcache.xml中cache的name.

While in most cases, one cache is enough, the Spring framework also supports multiple caches to be passed as parameters:

```
@Cacheable({"addresses", "directory"})
public String getAddress(Customer customer) {...}
```


按照条件操作缓存:

Spring cache 提供了一个很好的方法，那就是基于 SpEL 表达式的 condition 定义，这个 condition 是 @Cacheable 注释的一个属性，下面我来演示一下:

```
 @Cacheable(value="accountCache",condition="#userName.length() <= 4")// 缓存名叫 accountCache 
 public Account getAccountByName(String userName) { 
 // 方法内部实现不考虑缓存逻辑，直接实现业务
 return getFromDB(userName); 
 }
```
注意其中的 condition=”#userName.length() <=4”，这里使用了 SpEL 表达式访问了参数 userName 对象的 length() 方法，条件表达式返回一个布尔值，true/false，当条件为 true，则进行缓存操作，否则直接调用方法执行的返回结果。


### @CachePut

主要针对方法配置，能够根据方法的请求参数对其结果进行缓存，和 @Cacheable 不同的是，它每次都会触发真实方法的调用.

```
@CachePut(value="accountCache",key="#account.getName()")// 更新 accountCache 缓存
 public Account updateAccount(Account account) { 
   return updateDB(account); 
 } 
```



### @CacheEvict

即应用到移除数据的方法上，如删除方法，调用方法时会从缓存中移除相应的数据.

其中的allEntries表示是否移除所有的数据, 默认为false.

例子:

```
@CacheEvict(value = "user", key = "#user.id") //移除指定key的数据  
public User delete(User user) {  
    users.remove(user);  
    return user;  
}  
@CacheEvict(value = "user", allEntries = true) //移除所有数据  
public void deleteAll() {  
    users.clear();  
}  

@CacheEvict(value="accountCache",key="#account.getName()")// 清空 accountCache 缓存  
public void updateAccount(Account account) {
    updateDB(account); 
} 

@CacheEvict(value="accountCache",allEntries=true)// 清空 accountCache 缓存
public void reload() { 
} 

```
@CacheEvict(value=”accountCache”,key=”#account.getName()”)，其中的 Key 是用来指定缓存的 key 的，这里因为我们保存的时候用的是 account 对象的 name 字段，所以这里还需要从参数 account 对象中获取 name 的值来作为 key，前面的 # 号代表这是一个 SpEL 表达式，此表达式可以遍历方法的参数对象



## @Cacheable、@CachePut、@CacheEvict总结

---

copy... todo...XXX



## 基本原理

---

和 spring 的事务管理类似，spring cache 的关键原理就是 spring AOP，通过 spring AOP，其实现了在方法调用前、调用后获取方法的入参和返回值，进而实现了缓存的逻辑。

Spring cache 利用了 Spring AOP 的动态代理技术，即当客户端尝试调用 pojo 的 foo（）方法的时候，给它的不是 pojo 自身的引用，而是一个动态生成的代理类.

![动态代理调用图](https://www.ibm.com/developerworks/cn/opensource/os-cn-spring-cache/image003.jpg)

如上图所示，这个时候，实际客户端拥有的是一个代理的引用，那么在调用 foo() 方法的时候，会首先调用 proxy 的 foo() 方法，这个时候 proxy 可以整体控制实际的 pojo.foo() 方法的入参和返回值，比如缓存结果，比如直接略过执行实际的 foo() 方法等，都是可以轻松做到的。



## 注意和限制

### 基于 proxy 的 spring aop 带来的内部调用问题
上面介绍过 spring cache 的原理，即它是基于动态生成的 proxy 代理机制来对方法的调用进行切面，这里关键点是对象的引用问题，如果对象的方法是内部调用（即 this 引用）而不是外部引用，则会导致 proxy 失效，那么我们的切面就失效，也就是说上面定义的各种注释包括 @Cacheable、@CachePut 和 @CacheEvict 都会失效. ** 即有这些注解修饰的方法必须是该类的最外层方法, 而不是在该类中通过别的方法被调用**. 

比如:

```
public Account getAccountByName2(String userName) { 
   return this.getAccountByName(userName); 
 } 

 @Cacheable(value="accountCache")// 使用了一个缓存名叫 accountCache 
 public Account getAccountByName(String userName) { 
   // 方法内部实现不考虑缓存逻辑，直接实现业务
   return getFromDB(userName); 
 }
 ```
上面我们定义了一个新的方法 getAccountByName2，其自身调用了 getAccountByName 方法，这个时候，发生的是内部调用（this），所以没有走 proxy，导致 spring cache 失效. 必须调用getAccountByName才有效.


### @CacheEvict 的可靠性问题
我们看到，@CacheEvict 注释有一个属性 beforeInvocation，缺省为 false，即缺省情况下，都是在实际的方法执行完成后，才对缓存进行清空操作。期间如果执行方法出现异常，则会导致缓存清空不被执行。



## Reference

---

1. [Spring Cache抽象详解 *](http://jinnianshilongnian.iteye.com/blog/2001040)
1. [注释驱动的 Spring cache 缓存介绍****](https://www.ibm.com/developerworks/cn/opensource/os-cn-spring-cache/)
1. [A Guide To Caching in Spring](http://www.baeldung.com/spring-cache-tutorial)