# Spring 注解


## bean标记

用@Component标记一个组件，Spring 2.5 中提供@Component 注释外，还定义了几个拥有特殊语义的注释， 分别是：@Repository、@Service、@Controller

@Component @Controller @Service @Repository的作用
1、@controller 控制器（注入服务）
2、@service 服务（注入dao）
3、@repository dao（实现dao访问）
4、@component （把普通pojo实例化到spring容器中，相当于配置文件中的<bean id=”” class=””/>）
@Component,@Service,@Controller,@Repository注解的类，并把这些类纳入进spring容器中管理。

下面写这个是引入component的扫描组件
<context:component-scan base-package="com.iitshare"/>
其中base-package为需要扫描的包，可以包括下面的子包，比如：com.iitshare.hapishop、com.iitshare.hapicms

1、@Service用于标注业务层组件
2、@Controller用于标注控制层组件(如struts中的action)
3、@Repository用于标注数据访问组件，即DAO组件.
4、@Component泛指组件，当组件不好归类的时候，我们可以使用这个注解进行标注。


参考[@Component @Controller @Service @Repository的作用](http://www.iitshare.com/role-of-component-repository.html)



## bean注入

@Autowired:
Spring 2.5 引入了 @Autowired 注释，它可以对类成员变量、方法及构造函数进行标注，完成自动装配的工作。 通过 @Autowired的使用来消除 set ，get方法。

例子:

```
package com.baobaotao;     
import org.springframework.beans.factory.annotation.Autowired;     
    
public class Boss {     
    
    @Autowired    
    private Car car;     
    
    @Autowired    
    private Office office;     
    
    …     
}  
```



## @Configuration & @Bean

@Configuration标注在类上，相当于把该类作为spring的xml配置文件中的<beans>，作用为：配置spring容器(应用上下文).

@Bean标注在方法上(返回某个实例的方法)，等价于spring的xml配置文件中的<bean>，作用为：注册bean对象.

@Bean注解默认作用域为单例singleton作用域，可通过@Scope(“prototype”)设置为原型作用域； 


@Configuration  
public class MyConfig{  
    @Bean  
    public UserDao userDao(){  
        return new UserDaoImpl();  
    }  

    @Bean(name = "counter")   
    public Counter counter(){  
        return  new Counter(12,"Shake it Off",piano());  
    } 
}  

...
public class Counter {  
    public  Counter() {  
    }  
  
    public  Counter(double multiplier, String song,Instrument instrument) {  
        this.multiplier = multiplier;  
        this.song = song;  
        this.instrument=instrument;  
    }  
  ...
}



## @scope

@scope属性，有如下5种类型：

1. singleton 表示在spring容器中的单例，通过spring容器获得该bean时总是返回唯一的实例
1. prototype表示每次获得bean都会生成一个新的对象
1. request表示在一次http请求内有效（只适用于web应用）
1. session表示在一个用户会话内有效（只适用于web应用）
1. globalSession表示在全局会话内有效（只适用于web应用）

比如:

```
@Service
// 默认为singleto 相当于@Scope("singleton")
public class DemoSingletonService {
}

@Service
@Scope("prototype")
public class DemoPrototypeService {
}

```


## bean初始化和销毁时方法调用

使用通过@PostConstruct 和 @PreDestroy 方法 实现初始化和销毁bean之前进行的操作.

例子:

```
public class PersonService {  
    
    private String  message;  
  
    public String getMessage() {  
        return message;  
    }  
  
    public void setMessage(String message) {  
        this.message = message;  
    }  
      
    @PostConstruct  
    public void  init(){  
        System.out.println("I'm  init  method  using  @PostConstrut...."+message);  
    }  
      
    @PreDestroy  
    public void  dostory(){  
        System.out.println("I'm  destory method  using  @PreDestroy....."+message);  
    }  
      
}  
```




## @ResponseBody


该注解用于将Controller的方法返回的对象，通过适当的HttpMessageConverter转换为指定格式后，写入到Response对象的body数据区。

使用时机：返回的数据不是html标签的页面，而是其他某种格式的数据时（如json、xml等）使用.

如下:


@RequestMapping("applyRefund")
@ResponseBody
public ResultInfo applyRefund(@RequestBody RefundRequest request) {
    。。。。。。
    ResultInfo info = new ResultInfo();
    ....
    return  info;
｝

有了ResponseBody,表示返回值不再是逻辑视图的名称, 而是将返回值写入到Response对象的body数据区。


注意: 如果不使用ResponseBody, 而直接使用HttpServletResponse返回, 在方法的返回值需要为void, 如下:




## 绑定请求参数:@RequestParam,@CookieValue, @RequestHeader


todo...XXX



## @ModelAttribute

方法入参标记该注解后,入参的对象就会放到数据模型中.

todo...XXX




## @ExceptionHandler

todo...XXX





## 几个重要的类

1. HttpServletRequest
1. HttpServletResponse
1. HttpMessageConverter
1. ModelMap
1. SimpleMappingExceptionResolver.

    




每个程序员需掌握的20个代码命名小贴士
http://www.jb51.net/article/62771.htm

