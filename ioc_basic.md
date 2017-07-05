# Sprint IOC 实用 note


## IOC做什么？

通过IOC, 达到了对象的使用和对象的创建解耦:

1. 创建对象。在Spring里， 被创建的对方叫Bean.Bean的创建由spring负责。
2. 注入到某一个地方。所以需要对象的地方需要指明自己需要什么，尽管不需要它自己创建。在Spring中，除了使用XXContext。getBean("xx")获取bean外，其他需要bean的对象本身也是bean.


## bean生命周期回调和范围

Spring Bean 是受 Spring IoC 容器管理，由容器进行初始化和销毁的（prototype 类型由容器初始化之后便不受容器管理），通常我们不需要关注容器对 Bean 的初始化和销毁操作，由 Spring 经过构造函数或者工厂方法创建的 Bean 就是已经初始化完成并立即可用的。然而在某些情况下，可能需要我们手工做一些额外的初始化或者销毁操作，这通常是针对一些资源的获取和释放操作。

@Bean 支持两种属性，即 initMethod 和 destroyMethod，这些属性可用于定义生命周期方法。在实例化 bean 或即将销毁它时，容器便可调用生命周期方法。生命周期方法也称为回调方法，因为它将由容器调用。使用 @Bean 注释注册的 bean 也支持 JSR-250 规定的标准 @PostConstruct 和 @PreDestroy 注释。

    ```
    <bean id=”userService” 
        class=”bookstore.service.UserService” 
        init-method=”init” destroy-method=”destroy”> 
        …
    </bean>
        ```
    public class UserService {  
        
        private String  message;  
    
        public String getMessage() {  
            return message;  
        }  
    
        public void setMessage(String message) {  
            this.message = message;  
        }  
        
        public void  init(){  
            System.out.println("I'm  init  method  using  init-method"+message);  
        }  

        public void  dostory(){  
            System.out.println("I'm  destory method  using  destroy-method....."+message);  
        }  

        @PostConstruct  
        public void  postConstruct(){  
            System.out.println("I'm  init  method  using  @PostConstrut...."+message);  
        }  
        
        @PreDestroy  
        public void  preDestroy(){  
            System.out.println("I'm  destory method  using  @PreDestroy....."+message);  
        }  
        
    }  

    ```


## 自动装配策略

自动装配是指，Spring 在装配 Bean 的时候，根据指定的自动装配规则，将某个 Bean 所需要引用类型的 Bean 注入进来。 元素提供了一个指定自动装配类型的 autowire 属性，该属性有如下选项： 

- no -- 显式指定不使用自动装配。
- byName -- 如果存在一个和当前属性名字一致的 Bean，则使用该 Bean 进行注入。如果名称匹配但是类型不匹配，则抛出异常。如果没有匹配的类型，则什么也不做。  也就是说，即使按名字匹配，类型还是要一样的，一个需要A类型的地方，注入个B肯定有问题的。
- byType -- 如果存在一个和当前属性类型一致的 Bean ( 相同类型或者子类型 )，则使用该 Bean 进行注入。byType 能够识别工厂方法，即能够识别 factory-method 的返回类型。如果存在多个类型一致的 Bean，则抛出异常。如果没有匹配的类型，则什么也不做。 
- constructor -- 与 byType 类似，只不过它是针对构造函数注入而言的。如果当前没有与构造函数的参数类型匹配的 Bean，则抛出异常。使用该种装配模式时，优先匹配参数最多的构造函数。 
- autodetect -- 根据 Bean 的自省机制决定采用 byType 还是 constructor 进行自动装配。如果 Bean 提供了默认的构造函数，则采用 byType；否则采用 constructor 进行自动装配。

注意：如果是在xml里使用autowire属性进行自动装配，对应的代码里需要有相应的setXX,或者构造函数。

上面这个autowie属性是在xml中bean中的一个属性，我们也可以选择使用注解的方法实现上述的几种自动装配, 这些方式不需要代码里有对应的setXXX:


### @Autowired

@Autowired注解进行装配，默认是根据类型进行匹配。

@Autowired 注解可以用于 Setter 方法、构造函数、字段，甚至普通方法，前提是方法必须有至少一个参数。@Autowired 可以用于数组和使用泛型的集合类型。然后 Spring 会将容器中所有类型符合的 Bean 注入进来。

@Autowired 标注作用于 Map 类型时，如果 Map 的 key 为 String 类型，则 Spring 会将容器中所有类型符合 Map 的 value 对应的类型的 Bean 增加进来，用 Bean 的 id 或 name 作为 Map 的 key。

**关于@Qualifier**

和@Autowired一起使用时， 用于指明按指定的名称装配。

当容器中存在多个 Bean 的类型与需要注入的相同时，注入将不能执行，我们可以给 @Autowired 增加一个候选值，做法是在 @Autowired 后面增加一个 @Qualifier 标注，提供一个 String 类型的值作为候选的 Bean 的名字。举例如下：

    ```
    @Autowired(required=false) 
    @Qualifier("ppp") 
    public void setPerson(person p){}
    ```

@Qualifier 甚至可以作用于方法的参数 ( 对于方法只有一个参数的情况，我们可以将 @Qualifer 标注放置在方法声明上面，但是推荐放置在参数前面 )，举例如下：

    ```
    @Autowired(required=false) 
    public void sayHello(@Qualifier("ppp")Person p,String name){}
    ``
我们可以在配置文件中指定某个 Bean 的 qualifier 名字，方法如下：

    ```
    <bean id="person" class="footmark.spring.Person"> 
        <qualifier value="ppp"/> 
    </bean>
    ```

如果没有明确指定 Bean 的 qualifier 名字，那么默认名字就是 Bean 的名字。



### @Resource

@Resource使用名称进行自动装配， 所以如果希望用名称进行自动装配，优先使用@Resource,而不是@Autowire+@Qualifier.

@Resource 使用 byName 的方式执行自动封装。@Resource 标注可以作用于带一个参数的 Setter 方法、字段，以及带一个参数的普通方法上。@Resource 注解有一个 name 属性，用于指定 Bean 在配置文件中对应的名字。如果没有指定 name 属性，那么默认值就是字段或者属性的名字。

@Resource 和 @Qualifier 的配合虽然仍然成立，但是 @Qualifier 对于 @Resource 而言，几乎与 name 属性等效。


@Resource装配顺序:

1. 如果同时指定了name和type，则从Spring上下文中找到唯一匹配的bean进行装配，找不到则抛出异常;
1. 如果指定了name，则从上下文中查找名称（id）匹配的bean进行装配，找不到1. 
1. 如果指定了type，则从上下文中找到类型匹配的唯一bean进行装配，找不到或者找到多个，都1. 
1. 如果既没有指定name，又没有指定type，则自动按照byName方式进行装配；如果没有匹配，则回退为一个原始类型进行匹配，如果匹配则自动装配；

对于按名称查找， 如果Resource后有指定名称，根据指定的名称装配， 如果没有指定名称， 根据对象的名称装配，如下：

    ```
    @Resource
    private Cat cat2; //按对象名"cat2"装配。

    @Resource(name = "cat1")
    private Cat catByName;   //按Resource中指定的name "cat1"装配。
    ```

例子：

    ···
    @Resource(name=“userDao”)
    private UserDao  userDao;//用于字段上
    ···



## 使用 @Configuration 和 @Bean 进行 Bean 的声明

spring里的配置文件里有如下配置对bean进行声明定义：

    ```
    <beans xmlns="http://www.springframework.org/schema/beans"  ...>
        ...
        <bean id=”userDao” class=”bookstore.dao.UserDaoImpl”/> 
        <bean id=”bookDao” class=”bookstore.dao.BookDaoImpl”/>
        ...
    </beans> 
    ```
**而采用@Configuration 和 @Bean也可以做同样的事情,及对bean进行声明定义。**



Spring 3.0 新增了另外两个实现类：AnnotationConfigApplicationContext 和 AnnotationConfigWebApplicationContext。从名字便可以看出，它们是为注解而生，直接依赖于注解作为容器配置信息来源的 IoC 容器初始化类。

AnnotationConfigApplicationContext 搭配上 @Configuration 和 @Bean 注解，自此，XML 配置方式不再是 Spring IoC 容器的唯一配置方式。两者在一定范围内存在着竞争的关系，但是它们在大多数情况下还是相互协作的关系，两者的结合使得 Spring IoC 容器的配置更简单，更强大。

我们需要在用于指定配置信息的类上加上 @Configuration 注解，以明确指出该类是 Bean 配置的信息源。并且 Spring 对标注 Configuration 的类有如下要求：

1. 配置类不能是 final 的；
1. 配置类不能是本地化的，亦即不能将配置类定义在其他类的方法内部；
1. 配置类必须有一个无参构造函数。

AnnotationConfigApplicationContext 将配置类中标注了 @Bean 的方法的返回值识别为 Spring Bean，并注册到容器中，受 IoC 容器管理。

**@Bean 的作用等价于 XML 配置中的 标签。**

示例如下：

    ```
    @Configuration 
    public class BookStoreDaoConfig{ 
        @Bean 
        public UserDao userDao(){ return new UserDaoImpl();} 
        @Bean 
        public BookDao bookDao(){return new BookDaoImpl();} 
    }
    ```
与以上配置等价的 XML 配置如下：

    ```
    <bean id=”userDao” class=”bookstore.dao.UserDaoImpl”/> 
    <bean id=”bookDao” class=”bookstore.dao.BookDaoImpl”/>
    ```


在一般的项目中，为了结构清晰，通常会根据软件的模块或者结构定义多个 XML 配置文件，然后再定义一个入口的配置文件，该文件使用 将其他的配置文件组织起来。最后只需将该文件传给 ClassPathXmlApplicationContext 的构造函数即可。针对基于注解的配置，Spring 也提供了类似的功能，只需定义一个入口配置类，并在该类上使用 @Import 注解引入其他的配置类即可，最后只需要将该入口类传递给 AnnotationConfigApplicationContext。具体示例如下：  

    ```
    @Configuration  
    @Import({BookStoreServiceConfig.class,BookStoreDaoConfig.class})  
    public class BookStoreConfig{ … }  
    ```

**上面的例子中BookStoreConfig和BookStoreServiceConfig都是配置类， 采用@Import方式将其他的配置类组织到BookStoreConfig类，以后就可以将BookStoreConfig作为一个入口配置类。**

对于以注解为中心的配置方式,可以将使用 @ImportResource 注解引入存在的 XML 即可，如下所示：  

    ```
    @Configuration  
    @ImportResource(“classpath:/bookstore/config/spring-beans.xml”)  
    public class MyConfig{  
        ……  
    }  
    // 容器的初始化过程和纯粹的以配置为中心的方式一致：  
    AnnotationConfigApplicationContext ctx =  new AnnotationConfigApplicationContext(MyConfig.class);  
    ……  
    ```

@Bean 具有以下四个属性：

- name -- 指定一个或者多个 Bean 的名字。这等价于 XML 配置中 的 name 属性。
- initMethod -- 容器在初始化完 Bean 之后，会调用该属性指定的方法。这等价于 XML 配置中 的 init-method 属性。
- destroyMethod -- 该属性与 initMethod 功能相似，在容器销毁 Bean 之前，会调用该属性指定的方法。这等价于 XML 配置中 的 destroy-method 属性。
- autowire -- 指定 Bean 属性的自动装配策略，取值是 Autowire 类型的三个静态属性。Autowire.BY_NAME，Autowire.BY_TYPE，Autowire.NO。与 XML 配置中的 autowire 属性的取值相比，这里少了 constructor，这是因为 - constructor 在这里已经没有意义了。



## Bean范围scope

bean 的方法是使用 @Scope 注释定义的。XML 中实现这一目标的方法是指定 bean 元素中的 scope 属性。

### scope几种属性:

1. singleton作用域  
这是scope的默认方式。 当一个bean的作用域设置为singleton, 那么Spring IOC容器中只会存在一个共享的bean实例，并且所有对bean的请求，只要id与该bean定义相匹配，则只会返回bean的同一实例

1. prototype  
prototype作用域部署的bean，每一次请求（将其注入到另一个bean中，或者以程序的方式调用容器的getBean()方法）都会产生一个新的bean实例，相当与一个new的操作，对于prototype作用域的bean，有一点非常重要，那就是Spring不能对一个prototype bean的整个生命周期负责，容器在初始化、配置、装饰或者是装配完一个prototype实例后，将它交给客户端，随后就对该prototype实例不闻不问了。

1. request   
request表示该针对每一次HTTP请求都会产生一个新的bean，同时该bean仅在当前HTTP request内有效

1. session  
session作用域表示该针对每一次HTTP请求都会产生一个新的bean，同时该bean仅在当前HTTP session内有效

1. global session  
global session作用域类似于标准的HTTP Session作用域，不过它仅仅在基于portlet的web应用中才有意义。

1. 自定义bean装配作用域  
在spring2.0中作用域是可以任意扩展的，你可以自定义作用域，甚至你也可以重新定义已有的作用域（但是你不能覆盖singleton和 prototype），spring的作用域由接口org.springframework.beans.factory.config.Scope来定 义，自定义自己的作用域只要实现该接口即可



### scope指定方式:

1. 使用XML 方法定义 bean 范围

    ```
    <bean id="course" class="demo.Course" scope="prototype" >
        <property name="module" ref="module"/>
    </bean>
    ```

1. 使用 Java 配置的 bean 范围定义

    ```
    @Configuration
    public class AppContext {
        @Bean(initMethod = "setup", destroyMethod = "cleanup")
        @Scope("prototype")
        public Course course() {
            Course course = new Course();
            course.setModule(module());
            return course;
        }
        ...
    }
    ```



## 使用AnnotationConfigApplicationContext 注册配置类

在传统 XML 方法中，您可使用 ClassPathXmlApplicationContext 类来加载外部 XML 上下文文件。但在使用基于 Java 的配置时，有一个 AnnotationConfigApplicationContext 类。AnnotationConfigApplicationContext 类是 ApplicationContext 接口的一个实现，使您能够注册所注释的配置类。

    ```
    public static void main(String[] args) {
    ApplicationContext ctx = new AnnotationConfigApplicationContext(AppContext.class);
    Course course = ctx.getBean(Course.class);
    course.getName();
    }
    ```

AnnotationConfigApplicationContext 提供了三个构造函数用于初始化容器:

1. AnnotationConfigApplicationContext()：该构造函数初始化一个空容器，容器不包含任何 Bean 信息，需要在稍后通过调用其 register() 方法注册配置类，并调用 refresh() 方法刷新容器。
1. AnnotationConfigApplicationContext(Class<?>... annotatedClasses)：这是最常用的构造函数，通过将涉及到的配置类传递给该构造函数，以实现将相应配置类中的 Bean 自动注册到容器中。
1. AnnotationConfigApplicationContext(String... basePackages)：该构造函数会自动扫描以给定的包及其子包下的所有类，并自动识别所有的 Spring Bean，将其注册到容器中。它不但识别标注 @Configuration 的配置类并正确解析，而且同样能识别使用 @Repository、@Service、@Controller、@Component 标注的类。



## 几种Bean的声明方式的使用

**Bean可以声明在xml配置文件里，也可以用一个config或者几个config声明，也可以是这几种方式的组合使用。**


### 一个Config类将其他几个Config合并进来

在一般的项目中，为了结构清晰，通常会根据软件的模块或者结构定义多个 XML 配置文件，然后再定义一个入口的配置文件，该文件使用 将其他的配置文件组织起来。最后只需将该文件传给 ClassPathXmlApplicationContext 的构造函数即可。针对基于注解的配置，Spring 也提供了类似的功能，只需定义一个入口配置类，**并在该类上使用 @Import 注解引入其他的配置类即可**，最后只需要将该入口类传递给 AnnotationConfigApplicationContext。具体示例如下：

    ```
    @Configuration 
    @Import({BookStoreServiceConfig.class,BookStoreDaoConfig.class}) 
    public class BookStoreConfig{ … }
    ```


### 混合使用 XML 与注解进行 Bean 的配置

如果采用以XML配置为主的, 我们只需在 XML 配置文件中将被 @Configuration 标注的类定义为普通的 ，同时注册处理注解的 Bean 后处理器即可。示例如下：

    ```
    // 假设存在如下的 @Configuration 类：
    package bookstore.config; 
    import bookstore.dao.*; 
    @Configuration 
    public class MyConfig{ 
    @Bean 
        public UserDao userDao(){ 
            return new UserDaoImpl(); 
        } 
    } 
    ```

此时，只需在 XML 中作如下声明即可：

    ```  
    <beans … > 
        ……
        <context:annotation-config /> 
        <bean class=”demo.config.MyConfig”/> 
    </beans>
    ```  

**当然, 也可以不用将MyConfig放入中, 而是在xml中补上<context:component-scan base-package=”bookstore.config” />, 这样指定包下所有@Component、@Controller、@Service、@Repository 注解的类，由于 @Configuration 注解本身也用 @Component 标注了，Spring 将能够识别出 @Configuration 标注类并正确解析之。**

对于以注解为中心的配置方式，只需使用 @ImportResource 注解引入存在的 XML 即可，如下所示：

    ```
    @Configuration 
    @ImportResource(“classpath:/bookstore/config/spring-beans.xml”) 
    public class MyConfig{ 
    ……
    } 
    // 容器的初始化过程和纯粹的以配置为中心的方式一致：
    AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(MyConfig.class); 
    ……

    ```
总结

1. 使用@Autowired注解进行装配，默认是根据类型进行匹配。当容器中存在多个 Bean 的类型与需要注入的相同时，注入将不能执行，我们可以给 @Autowired 增加一个候选值，做法是在 @Autowired 后面增加一个 @Qualifier 标注，提供一个 String 类型的值作为候选的 Bean 的名字。如果没有明确指定 Bean 的 qualifier 名字，那么默认名字就是 Bean 的名字。使用了@Qualifier后，变成按名称装配。
1. @Resource 使用 byName 的方式执行自动封装。@Resource 注解有一个 name 属性，用于指定 Bean 在配置文件中对应的名字。如果没有指定 name 属性，那么默认值就是字段或者属性的名字。
1. spring通过配置文件中的<beans><bean>检测到需要创建及注入的bean, 对于那些不是在配置文件中，而是通过注解@Component、@Controller、@Service、@Repository，@Configuration,@Bean声明的bean,需要在配置文件中指定<context:component-scan base-package=”xxx” />告之spring.
1. 可以使用@import将其他configuration类合并入一个configuration, 如下:

    ```
    @Configuration 
    @Import({BookStoreServiceConfig.class,BookStoreDaoConfig.class}) 
    public class BookStoreConfig{ … }
    ```
1. 一个Bean如果是采用sigleton的, 但是如果@AutoWired采用名字匹配(采用qualifier指定), 则如果存在两个名字的, 对象会被构建两个的. 另外对于同一个Bean, 采用@Autowired, 不允许一个是按类型匹配,一个按名称匹配.
1. xx



## 一般spring应用中相应逻辑的测试

todo ...




## Reference

1. [关于Spring IOC (DI-依赖注入)你需要知道的一切****](http://blog.csdn.net/javazejian/article/details/54561302)
1. [使用 Java 配置进行 Spring bean 管理**](https://www.ibm.com/developerworks/cn/webservices/ws-springjava/index.html)
1. [详解 Spring 3.0 基于 Annotation 的依赖注入实现***](https://www.ibm.com/developerworks/cn/opensource/os-cn-spring-iocannt/)
