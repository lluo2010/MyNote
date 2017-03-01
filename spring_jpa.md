# Spring data JPA note


todo....xxx


## 方法的生成和使用

首先需要继承JpaRepository, 如下:

```
public interface UserRepository extends JpaRepository<User, Long> {
}
```
spring data jpa 默认预先生成了一些基本的CURD的方法，例如：增、删、改等等


### 使用默认方法
比如下面:

```
@Test
public void testBaseQuery() throws Exception {
	User user=new User();
	userRepository.findAll();
	userRepository.findOne(1l);
	userRepository.save(user);
	userRepository.delete(user);
	userRepository.count();
	userRepository.exists(1l);
	// ...
}
```

### 自定义简单查询

自定义的简单查询就是根据方法名来自动生成SQL，主要的语法是findXXBy,readAXXBy,queryXXBy,countXXBy, getXXBy后面跟属性名称：

User findByUserName(String userName);

也使用一些加一些关键字And、 Or

User findByUserNameOrEmail(String username, String email);

修改、删除、统计也是类似语法

```
Long deleteById(Long id);
Long countByUserName(String userName)
```


基本上SQL体系中的关键词都可以使用，例如：LIKE、 IgnoreCase、 OrderBy。

```
List<User> findByEmailLike(String email);
User findByUserNameIgnoreCase(String userName);
List<User> findByUserNameOrderByEmailDesc(String email);
```

这些都不需要实现,只要名称符合规范就可以了.


具体规则如下:
![jpa规则](https://pic2.zhimg.com/v2-93887943badd67cb67a63e96cbc81601_b.jpg)


### 自定义SQL查询
其实Spring data 觉大部分的SQL都可以根据方法名定义的方式来实现，但是由于某些原因我们想使用自定义的SQL来查询，spring data也是完美支持的；在SQL的查询方法上面使用@Query注解，如涉及到删除和修改在需要加上@Modifying.也可以根据需要添加 @Transactional 对事物的支持，查询超时的设置等

如下:

```
@Modifying
@Query("update User u set u.userName = ?1 where c.id = ?2")
int modifyByIdAndUserId(String  userName, Long id);
	
@Transactional
@Modifying
@Query("delete from User where id = ?1")
void deleteByUserId(Long id);
  
@Transactional(timeout = 10)
@Query("select u from User u where u.emailAddress = ?1")
    User findByEmailAddress(String emailAddress);
```





### 多表查询

todo...XXX



## @Transactional

Spring中的@Transactional基于动态代理的机制，提供了一种透明的事务管理机制，方便快捷解决在开发中碰到的问题. 在方法前面加上@Transactional, 表示该方法需要事务管理。

@Transactional属性如下:

 
属性|类型|描述
-|-|-
value|String|可选的限定描述符，指定使用的事务管理器
propagation|enum: Propagation|可选的事务传播行为设置
isolation|enum: Isolation|可选的事务隔离级别设置
readOnly|boolean|读写或只读事务，默认读写
timeout|int (in seconds granularity)|事务超时时间设置
rollbackFor|Class对象数组，必须继承自Throwable|导致事务回滚的异常类数组
rollbackForClassName|类名数组，必须继承自Throwable|导致事务回滚的异常类名字数组
noRollbackFor|Class对象数组，必须继承自Throwable|不会导致事务回滚的异常类数组
noRollbackForClassName|类名数组，必须继承自Throwable|不会导致事务回滚的异常类名字数组

比如下面:

```
@Transactional(readOnly = false, propagation = Propagation.REQUIRES_NEW)
   public void updateFoo(Foo foo) {
     // do something
   }
```


### @Transactional之propagation:

Propagation支持7种不同的传播机制：REQUIRED,SUPPORTS,MANDATORY,REQUIRES_NEW,NOT_SUPPORTED,NEVER, NESTED.

1. REQUIRED: 业务方法需要在一个事务中运行,如果方法运行时,已处在一个事务中,那么就加入该事务,否则自己创建一个新的事务.这是spring默认的传播行为.。 
1. SUPPORTS: 如果业务方法在某个事务范围内被调用,则方法成为该事务的一部分,如果业务方法在事务范围外被调用,则方法在没有事务的环境下执行。
1. MANDATORY：只能在一个已存在事务中执行,业务方法不能发起自己的事务,如果业务方法在没有事务的环境下调用,就抛异常.
1. REQUIRES_NEW: 业务方法总是会为自己发起一个新的事务,如果方法已运行在一个事务中,则原有事务被挂起,新的事务被创建,直到方法结束,新事务才结束,原先的事务才会恢复执行.
1. NOT_SUPPORTED: 声明方法需要事务,如果方法没有关联到一个事务,容器不会为它开启事务.如果方法在一个事务中被调用,该事务会被挂起,在方法调用结束后,原先的事务便会恢复执行.
1. NEVER：声明方法绝对不能在事务范围内执行,如果方法在某个事务范围内执行,容器就抛异常.只有没关联到事务,才正常执行.
1. NESTED：如果一个活动的事务存在,则运行在一个嵌套的事务中.如果没有活动的事务,则按REQUIRED属性执行.它使用了一个单独的事务, 这个事务拥有多个可以回滚的保证点.内部事务回滚不会对外部事务造成影响, 它只对DataSourceTransactionManager 事务管理器起效.



## @Query

@Query注解被用于通过使用JPA查询语言创建查询，并且直接绑定这些查询到你的repository接口的方法，当查询方法别调用，Spring Data JPA将执行通过@Query注解指定的查询（如果@Query 注解与命名查询有冲突，将执行通过使用@Query 注解指定的查询）。


比如下面:

```
public interface PersonRepository extends JpaRepository<Person, Long> {

    /**
     * Finds a person by using the last name as a search criteria.
     * @param lastName
     * @return  A list of persons whose last name is an exact match with the given last name.
     *          If no persons is found, this method returns an empty list.
     */
    @Query("SELECT p FROM Person p WHERE LOWER(p.lastName) = LOWER(:lastName)")
    public List<Person> find(@Param("lastName") String lastName);
}
```


对于@Query, 如果有update/delete, 记得要使用@Modifying, clearAutomatically=true表示更新后马上就有变化, 否则该次不是马上有变化的.
```
@Modifying(clearAutomatically = true)
@Query("update UserInfo u set u.name =:name where u.age =:age")
@Transactional
void updateNameByAge(@Param("name") String name, @Param("age") int age);
```

通过 @Modifying 注解完成修改操作（注意：不支持新增）

```
@Repository
public interface CompanyRepository extends JpaRepository<Company, Integer> {
    @Modifying
    @Query("UPDATE Company c SET c.address = :address WHERE c.id = :companyId")
    int updateAddress(@Param("companyId") int companyId, @Param("address") String address);

    @Query(value="delete from spj_student_2",nativeQuery=true)  
    @Modifying  
    public void deleteAllBySql();  
}

```

** 注意: **

- 如果@Query里的nativeQuery为false(默认为false), 则@Query里的Select XX或者Update XX的XX不是指表名,而是指标明对应的@Entity类的类名. 即表对应的对象类名.
- 如果@Query里的nativeQuery为true, 则是表名.






@Param


nativeQuery










## @EntityManager

---

todo...XXX




## Demo

---

### 配置文件application.properties

在resources下面, 配置如下:

```
spring.datasource.url=jdbc:mysql://localhost:3306/NewSchema
spring.datasource.username=root
spring.datasource.password=123456
spring.datasource.driver-class-name=com.mysql.jdbc.Driver

spring.datasource.max-idle=10
spring.datasource.max-wait=10000
spring.datasource.min-idle=5
spring.datasource.initial-size=5
spring.datasource.validation-query=SELECT 1
spring.datasource.test-on-borrow=false
spring.datasource.test-while-idle=true
spring.datasource.time-between-eviction-runs-millis=18800
spring.datasource.jdbc-interceptors=ConnectionState;SlowQueryReport(threshold=0)
```

### 实体

指定对应的表, 代码如下:

```
@Entity
@Table(name = "User1")
public class UserInfo {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private int UserID;
    private String name;

    @Column(name = "age")
    private int age;

    public UserInfo(){

    }

    public UserInfo(String strName, int nAge){
        name = strName;
        age = nAge;
    }

    public int getUserID() {
        return UserID;
    }

    public void setUserID(int userID) {
        UserID = userID;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "UserInfo{" +
                "UserID=" + UserID +
                ", name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```


### UserInfoRepository

从JpaRepository派生, 代码如下:

```
public interface UserInfoRepository extends JpaRepository<UserInfo, Integer> {
    List<UserInfo> findByName(String name);

    List<UserInfo> findByNameAndAge(String name, int age);
    List<UserInfo> findByNameOrAge(String name, int age);
    List<UserInfo> findByAge(int age);
    List<UserInfo> findByNameLike(String name);

    List<UserInfo> findByAgeLessThan(int age);


    //List<UserInfo> findByName(String name);
    //List<UserInfo> findByName(String name);

    @Query("select u from UserInfo u")
    List<UserInfo> getAll();

    @Query("select u from UserInfo u where u.name = ?1")
    List<UserInfo> getByName(String strName);

    @Query("select u from UserInfo u where u.name = ?1 and u.age= ?2")
    List<UserInfo> getByNameAndAge(String strName, int age);
    @Query("select u from UserInfo u where u.name = :name and u.age= :age")
    List<UserInfo> getByAgeAndName(@Param("age")int nAge, @Param("name")String strName);


    @Modifying(clearAutomatically = true)
    @Query("update UserInfo u set u.name =:name where u.age =:age")
    @Transactional
    void updateNameByAge(@Param("name") String name, @Param("age") int age);

    @Modifying(clearAutomatically = true)
    @Query("delete UserInfo u  where u.age <:age")
    @Transactional
    void deleteWhenAgeLess(@Param("age") int age);

}
```


### RepositoryUserService


代码如下:

```
@Service
public class RepositoryUserService {

    @Resource
    private UserInfoRepository userInfoRepository;


    public void setUserInfoRepository(UserInfoRepository uiRepository){
        System.out.println("setUserInfoRepository");
        userInfoRepository = uiRepository;
    }


    @Transactional
    public List<UserInfo> findUserInfo(String strName){
        System.out.println("find User Info");
        List<UserInfo> persons = userInfoRepository.findByName(strName);
        print(persons);
        //List<UserInfo> persons = userInfoRepository.getAll();
        return persons;
    }
```


## Reference

---

1. [Spring Boot系列(五)：spring data jpa的使用 ***](https://zhuanlan.zhihu.com/p/25000309?refer=dreawer)
1. [Spring Data JPA - Reference Documentation](http://docs.spring.io/spring-data/jpa/docs/current/reference/html/)
1. [Spring中@Transactional用法深度分析之一](http://blog.csdn.net/blueheart20/article/details/44654007/)
1. [Spring Data JPA教程, 第三部分: Custom Queries with Query Methods（翻译）**](http://www.cnblogs.com/chenying99/p/3143539.html)
1. [Updating Entities with Update Query in Spring Data JPA*](http://codingexplained.com/coding/java/spring-framework/updating-entities-with-update-query-spring-data-jpa)
1. [Spring Data JPA - Reference Documentation *](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/)
1. [Spring Boot中使用Spring-data-jpa](http://blog.csdn.net/pdw2009/article/details/51115044)
1. [Spring Boot:在Spring Boot中使用Mysql和JPA](http://www.tuicool.com/articles/zEz2QrY)
1. [SpringBoot入门系列：第五篇 JPA mysql](http://blog.csdn.net/sosfnima/article/details/51993689)
1. [Spring Boot中使用Spring-data-jpa让数据访问更简单、更优雅](http://blog.didispace.com/springbootdata2/)
1. [Spring Data JPA Tutorial: Introduction to Query Methods**](https://www.petrikainulainen.net/programming/spring-framework/spring-data-jpa-tutorial-introduction-to-query-methods/)


