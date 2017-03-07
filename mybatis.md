# MyBatis study note


## 简介

---

MyBatis 是支持普通 SQL 查询，存储过程和高级映射的优秀持久层框架，其几乎消除了所有的 JDBC 代码和参数的手工设置以及结果集的检索。MyBatis 使用简单的 XML 或注解用于配置和原始映射，将接口和 Java 的 POJOs（Plain Old Java Objects，普通的 Java对象）映射成数据库中的记录。MyBatis 应用程序大都使用 SqlSessionFactory 实例，SqlSessionFactory 实例可以通过 SqlSessionFactoryBuilder 获得，而　SqlSessionFactoryBuilder 则可以从一个 XML 配置文件或者一个预定义的配置类的实例获得。

Hibernate不用你编写一行SQL语句，而MyBatis需要手写SQL语句，优点就是能够灵活调试SQL语句。

我的理解是, 定义好POJO, 定义好要操作的接口(接口里包含操作数据库的方法, 当然,接口里只有声明,没有实现), 然后在相应的Xml Map文件中, 做好POJO对象和数据库的映射, 使用sql语句实现接口相应方法的真正的操作, 然后将Xml map文件和POJO,操作接口关联, 最后就可以直接使用操作接口操作了.

在Spring里通过配置后, 通过配置, SqlSessionFactory, SqlSession都隐藏了, 可以直接通过注入获取操作接口进行使用, 具体见后面的例子.

对于操作接口, 从有些例子看可以没有?? 可以通过SqlSession的有些方法直接引用Map 配置文件中的相应语句操作直接执行, 比如下面:

```
List<Person> personList = sqlSession.selectList("yeepay.payplus/mapper.UserMapper.findAll");
```
上面的"yeepay.payplus/mapper.UserMapper.findAll"对应的配置如下, 它不是定需要和相应的实际接口关联:

```
<mapper namespace="yeepay.payplus/mapper.UserMapper">   <!-- 命名空间，名字可以随意起，只要不冲突即可 -->
    <!-- 对象映射，可以不写 -->
    <resultMap type="yeepay.payplus.Person" id="personRM">
        <!-- property="id"，表示实体对象的属性；column="ID"，表示结果集字段 -->
        <id property="id" column="ID"/>
        <result property="Name" column="NAME"/>
        <result property="age" column="AGE"/>
    </resultMap>

    <!-- 查询功能，resultType 设置返回值类型 -->
    <select id="findAll" resultType="yeepay.payplus.Person">  <!-- 书写 SQL 语句 -->
        SELECT * FROM person
    </select>
```

上面的第一个部分是关联POJO类Person和数据库.




## MyBatis底层原理

---

MyBatis底层还是依赖JDBC来操作数据库，不过对JDBC的语法进行了封装，使用JDBC来操作数据库的流程一般是加载JDBC驱动->创建JDBC连接->创建Statement->执行查询->解析ResultSet->异常处理->关闭Statement和Connection，如果每次都这样做就比较繁琐。

MyBatis所有的数据库操作都是在SqlSession中操作，这样的话就可以缓存查询结果、不用重复创建JDBC连接。



## MyBatis核心概念

---

1. SqlSessionFactoryBuilder:顾名思义就是采用了Builder模式，用于创建SqlSessionFactory对象。
1. SqlSessionFactory: SqlSessionFactory工厂方法，用于创建SqlSession。
1. SqlSession: SqlSession 的实例不是线程安全的,每个线程都应该有它自己的SqlSession 实例，应该随着HttpServletRequest的创建而创建，请求结束则销毁。
1. Mapper: 承载了实际的业务逻辑，其生命周期比较短，由SqlSession创建,用于将Java对象和实际的SQL语句对应起来。



## Spring环境下Mybatis初始化过程

---

在开发过程中通常结合Spring框架一起使用提高开发效率，由于Spring进行了控制反转，所以其中MyBatis的初始化过程和正常过程稍稍有些不同：

Spring框架会在classpath下找到MyBatis的核心配置文件，使用它来初始化一个SqlSessionFactory实例。mapper类也会作为bean注入到代码中去的，Spring会使用上一步创建的SqlSessionFactory实例来创建SqlSession的实例，然后再使用SqlSession尝试创建各个mapper对象。

于此同时，MyBatis会扫描classpath下的mapper映射XML文件（此路径可以自定义），对于每一个mapper接口，它的「类全名」会作为命名空间，来和映射文件中的mapper标签进行匹配。
对于每一个映射文件中的一个执行语句标签（如select、delete），MyBatis会把他们映射到SqlSession的方法上，创建mapper接口的一个实现类。
如果mapper接口和其映射文件一一匹配，则bean创建成功。



## Spring中使用例子

---

分为下面几个步骤:

1. 创建Mapper文件: 将POJO类与数据库表格对应, 操作接口类对应的sql具体操作.
1. 创建POJO类以及操作接口类.
1. Spring配置, 包括:

    - jdbc数据库配置.
    - sqlSessionFactory配置.
    - sqlSessionTemplate配置. 
    - mapper批量扫描配置, 即配置MapperScannerConfigurer.

具体见下面.


### 创建Mapper文件

主要的工作是将POJO类与数据库表格对应, 操作接口类对应的sql具体操作. 具体如下:

```
<mapper namespace="com.ezlippi.mapper.UserMapper" >
	
	<!--定义了Select语句返回结果和实际POJO对象的属性映射关系-->
    <resultMap id="BaseResultMap" type="com.ezlippi.model.User" >
        <id column="id" property="id" jdbcType="BIGINT" />
        <result column="userName" property="userName" jdbcType="VARCHAR" />
        <result column="passWord" property="passWord" jdbcType="VARCHAR" />
        <result column="user_sex" property="userSex" javaType="com.ezlippi.enums.UserSexEnum"/>
        <result column="nick_name" property="nickName" jdbcType="VARCHAR" />
    </resultMap>

	<!-- 定义了一条SQL语句模板，后面可以通过<include refid="Base_Column_List"/>引用这条SQL -->
    <sql id="Base_Column_List" >
        id, userName, passWord, user_sex, nick_name
    </sql>

    <select id="getAll" resultMap="BaseResultMap"  >
       SELECT 
       <include refid="Base_Column_List" />
       FROM users
    </select>

    <select id="getOne" parameterType="java.lang.Long" resultMap="BaseResultMap" >
        SELECT 
       <include refid="Base_Column_List" />
       FROM users
       WHERE id = #{id}
    </select>

    <insert id="insert" parameterType="com.ezlippi.model.User" >
       INSERT INTO 
               users
               (userName,passWord,user_sex) 
           VALUES
               (#{userName}, #{passWord}, #{userSex})
    </insert>

    <update id="update" parameterType="com.ezlippi.model.User" >
       UPDATE 
               users 
       SET 
           <if test="userName != null">userName = #{userName},</if>
           <if test="passWord != null">passWord = #{passWord},</if>
           nick_name = #{nickName}
       WHERE 
               id = #{id}
    </update>

    <delete id="delete" parameterType="java.lang.Long" >
       DELETE FROM
                users 
       WHERE 
                id =#{id}
    </delete>
</mapper>
```

"<mapper namespace="com.ezlippi.mapper.UserMapper" >"的路径要和真正的操作接口UserMapper路径一致.


### 创建POJO类和操作接口类

操作接口类如下, 每个接口方法都和Map配置文件里的sql操作关联:

```
@Repository
public interface UserMapper {
List<User> getAll();
User getOne(Long id);
void insert(User user);
void update(User user);
void delete(Long id);
}
```

前面加@Repository是为了可直接在Service层直接自动装配注入.


### Spring配置

```
<?xml version="1.0" encoding="UTF-8"?>  
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:tx="http://www.springframework.org/schema/tx" xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.2.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.2.xsd">  

	<!-- 加载配置文件 -->
	<context:property-placeholder location="classpath:jdbc.properties" />

    <!-- 配置dbcp数据源 引用jdbc.peoperties属性文件中的属性-->  
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource" destroy-method="close">  
        <property name="driverClass" value="${jdbc.driver}" />  
        <property name="jdbcUrl" value="${jdbc.url}" />  
        <property name="user" value="${jdbc.username}" />  
        <property name="password" value="${jdbc.password}" />  
        <property name="initialPoolSize" value="${connection_pools.initial_pool_size}" />
        <property name="minPoolSize" value="${connection_pools.min_pool_size}" />
        <property name="maxPoolSize" value="${connection_pools.max_pool_size}" />
        <property name="maxIdleTime" value="${connection_pools.max_idle_time}" />
        <property name="acquireIncrement" value="${connection_pools.acquire_increment}" />
        <property name="checkoutTimeout" value="${connection_pools.checkout_timeout}" />
    </bean>  

    <!-- 配置mybatisSqlSessionFactoryBean -->  
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">  
        <property name="dataSource" ref="dataSource" />  
        <property name="configLocation" value="classpath:mybatis/mybatis.xml"/>  
        <property name="mapperLocations" value="classpath*:com/ezlippi/**/*Mapper.xml"/>
    </bean>  

    <!-- 配置SqlSessionTemplate -->  
    <bean id="sqlSessionTemplate" class="org.mybatis.spring.SqlSessionTemplate">  
        <constructor-arg name="sqlSessionFactory" ref="sqlSessionFactory" />  
    </bean>  

    <!-- mapper批量扫描，从mapper包中扫描出mapper接口，自动创建代理对象并且在spring容器中注册 
	遵循规范：将mapper.java和mapper.xml映射文件名称保持一致，且在一个目录 中
	自动扫描出来的mapper的bean的id为mapper类名（首字母小写）
	-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.ezlippi.**.dao"/>
        <property name="sqlSessionTemplateBeanName" value="sqlSessionTemplate"/>
    </bean>

    <!-- 事务配置 -->  
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">  
        <property name="dataSource" ref="dataSource" />  
    </bean>  

    <!-- 使用annotation注解方式配置事务 -->  
    <tx:annotation-driven transaction-manager="transactionManager"/>  


	<context:component-scan base-package="com.ezlippi" />
	<context:annotation-config />

</beans>
```

其中如下代码为spring自扫描所有dao包并把其下的所有mybatis接口文件装配入容器。

```
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.ezlippi.**.dao"/>
        <property name="sqlSessionTemplateBeanName" value="sqlSessionTemplate"/>
</bean>
```


### 真正使用如下

通过@AutoWire注入UserMapper:

```
@Service(value = "userService")
public class UserServiceImpl {

	private UserMapper mapper;

	@Autowired
	public UserServiceImpl(UserMapper userMapper) {
    	this.mapper = userMapper;
	}

	public List<User> selectAll() {
		return userMapper.selectAll();
	} 
}

```


## Map文件规范

---

1. map文件中的namespace属性值必须等于接口的全路径名称.
1. map文件中的sql语句的id属性值必须等于mapper接口中的方法的名称.
1. map文件中传入的参数类型必须等于接口方法中传入的参数类型.
1. map文件中操作的返回值类型必须等于接口方法的返回值类型



## #{}和${}的区别

在拼接SQL语句中可以用”#{}”和”${}”来引用方法中的参数,两者的区别如下:

1. “#{}”在底层实现上使用?做占位符来生成PreparedStatement，然后将参数传入，大多数情况都应使用这个，它更快、更安全。
1. “${}”将传入的数据直接显示生成在sql中。如：order by ${user_id}，如果传入的值是111,那么解析???



## 例子

---

一般都是定义了POJO类,定义了操作接口(只是接口)类,然后在配置文件里实现对应的sql, 然后关联, 但是也有没有接口类, 是直接在map配置文件里通过sql指定, 然后使用时直接通过使用配置文件里的namespace再加上配置文件里实现的方法的id来指定的.  比如下面的"yeepay.payplus/mapper.UserMapper.findAll"就是由namespace "yeepay.payplus/mapper.UserMapper"+操作id "find"组成.

```
List<Person> personList = sqlSession.selectList("yeepay.payplus/mapper.UserMapper.findAll");
```

下面就是没有定义操作接口类的方式

1. 核心配置文件 sqlMapConfig.xml

    ```
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE configuration
            PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-config.dtd">

    <configuration>
        <!-- 配置开发环境，可以配置多个，在具体用时再做切换 -->
        <environments default="">
            <environment id="test">
                <transactionManager type="JDBC"></transactionManager>    <!-- 事务管理类型：JDBC、MANAGED -->
                <dataSource type="POOLED">    <!-- 数据源类型：POOLED、UNPOOLED、JNDI -->
                    <property name="driver" value="com.mysql.jdbc.Driver" />
                    <property name="url" value="jdbc:mysql://localhost:3306/test?characterEncoding=utf-8" />
                    <property name="username" value="root" />
                    <property name="password" value="root" />
                </dataSource>
            </environment>
        </environments>

        <!-- 加载映射文件 mapper -->
        <mappers>
            <!-- 路径用 斜线（/） 分割，而不是用 点(.) -->
            <mapper resource="yeepay/payplus/mapper/UserMapper.xml"></mapper>
        </mappers>
    </configuration>
    ```

1. 实体类（Person）

    ```
    package yeepay.payplus;

    public class Person {
        private Integer id;
        private String name;
        private Integer age;

        public Integer getId() {
            return id;
        }

        public void setId(Integer id) {
            this.id = id;
        }
        ...
    }

    ```

1. 映射文件 UserMapper.xml

    ```
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE mapper
            PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

    <mapper namespace="yeepay.payplus/mapper.UserMapper">   <!-- 命名空间，名字可以随意起，只要不冲突即可 -->
        <!-- 对象映射，可以不写 -->
        <resultMap type="yeepay.payplus.Person" id="personRM">
            <!-- property="id"，表示实体对象的属性；column="ID"，表示结果集字段 -->
            <id property="id" column="ID"/>
            <result property="Name" column="NAME"/>
            <result property="age" column="AGE"/>
        </resultMap>

        <!-- 查询功能，resultType 设置返回值类型 -->
        <select id="findAll" resultType="yeepay.payplus.Person">  <!-- 书写 SQL 语句 -->
            SELECT * FROM person
        </select>

        <!-- 通过 ID 查询 -->
        <select id="get" parameterType="Integer" resultMap="personRM">  <!-- 书写 SQL 语句 -->
            SELECT * FROM person
            WHERE id = #{id}
        </select>

        <!-- 新增功能，在SQL语句中有参数，并以实体来封装参数 -->
        <insert id="insert" parameterType="yeepay.payplus.Person">
            INSERT INTO person (id,name,age) VALUES (#{id},#{name},#{age})
        </insert>

        <!-- 修改功能 -->
        <update id="update" parameterType="yeepay.payplus.Person">
            UPDATE person set name=#{name},age=#{age}
            WHERE id = #{id}
        </update>

        <!-- 删除功能 -->
        <delete id="deleteById" parameterType="integer">
            DELETE FROM person
            WHERE id = #{id}
        </delete>
    </mapper>

    ```

1. 部分真正操作方法

    ```
    /**
    *  1、获得 SqlSessionFactory
    *  2、获得 SqlSession
    *  3、调用在 mapper 文件中配置的 SQL 语句
    */
    String resource = "sqlMapConfig.xml";           // 定位核心配置文件
    InputStream inputStream = Resources.getResourceAsStream(resource);
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);    // 创建 SqlSessionFactory

    SqlSession sqlSession = sqlSessionFactory.openSession();    // 获取到 SqlSession

    // 调用 mapper 中的方法：命名空间 + id
    List<Person> personList = sqlSession.selectList("yeepay.payplus/mapper.UserMapper.findAll");

    for (Person p : personList){
        System.out.println(p);
    }
    -------------------------------

    /**
    * insert
    */
    ...
    SqlSession sqlSession = sqlSessionFactory.openSession();            //获取到 SqlSession
        Person p = new Person();
        p.setId(5);
        p.setName("gavin");
        p.setAge(12);
        sqlSession.insert("yeepay.payplus.mapper.UserMapper.insert", p);
        sqlSession.commit();            //默认是不自动提交，必须手工提交
    }
    -------------------------------

    /**
    * update
    */
    ...
    Person p = sqlSession.selectOne("yeepay.payplus.mapper.UserMapper.get", 2);   // 获得 id=2 的记录
    p.setName("jane");
    p.setAge(16);

    sqlSession.insert("yeepay.payplus.mapper.UserMapper.update", p);
    sqlSession.commit(); 
    -------------------------------

    /**
    * delete
    */
    ...
    sqlSession.delete("yeepay.payplus.mapper.UserMapper.deleteById", 2);
    sqlSession.commit(); 

    ```



## 配置文件怎么将接口注入到spring

---

MapperScannerConfigurer可以自动扫描将Mapper接口生成代理注入到Spring.

Mybatis在与Spring集成的时候可以配置 MapperFactoryBean来生成Mapper接口的代理. 例如

```
<bean id="userMapper" class="org.mybatis.spring.mapper.MapperFactoryBean">
  <property name="mapperInterface" value="org.mybatis.spring.sample.mapper.UserMapper" />
  <property name="sqlSessionFactory" ref="sqlSessionFactory" />
</bean>
```
MapperFactoryBean 创建的代理类实现了 UserMapper 接口,并且注入到应用程序中。 因为代理创建在运行时环境中(Runtime,译者注) ,那么指定的映射器必须是一个接口,而 不是一个具体的实现类。

上面的配置有一个很大的缺点，就是系统有很多的配置文件时 全部需要手动编写，所以上述的方式已经很用了。

没有必要在 Spring 的 XML 配置文件中注册所有的映射器。相反,你可以使用一个 MapperScannerConfigurer , 它 将 会 查 找 类 路 径 下 的 映 射 器 并 自 动 将 它 们 创 建 成 MapperFactoryBean。

要创建 MapperScannerConfigurer,可以在 Spring 的配置中添加如下代码:

```
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
  <property name="basePackage" value="org.mybatis.spring.sample.mapper" />
</bean>
```
basePackage 属性是让你为映射器接口文件设置基本的包路径。 你可以使用分号或逗号 作为分隔符设置多于一个的包路径。每个映射器将会在指定的包路径中递归地被搜索到。

注 意 , 没 有 必 要 去 指 定 SqlSessionFactory 或 SqlSessionTemplate , 因 为 MapperScannerConfigurer 将会创建 MapperFactoryBean,之后自动装配。但是,如果你使 用了一个 以上的 DataSource ,那 么自动 装配可 能会失效 。这种 情况下 ,你可 以使用 sqlSessionFactoryBeanName 或 sqlSessionTemplateBeanName 属性来设置正确的 bean 名 称来使用。这就是它如何来配置的,注意 bean 的名称是必须的,而不是 bean 的引用,因 此,value 属性在这里替代通常的 ref:

```
<property name="sqlSessionFactoryBeanName" value="sqlSessionFactory" />
```
MapperScannerConfigurer 支 持 过 滤 由 指 定 的 创 建 接 口 或 注 解 创 建 映 射 器 。 annotationClass 属性指定了要寻找的注解名称。 markerInterface 属性指定了要寻找的父 接口。如果两者都被指定了,加入到接口中的映射器会匹配两种标准。默认情况下,这两个 属性都是 null,所以在基包中给定的所有接口可以作为映射器加载。

具体可以看下: http://www.tuicool.com/articles/BbAz6v/



## Reference

---

1. [MyBatis简明教程**](http://www.ezlippi.com/blog/2017/02/mybatis-intorduction.html?utm_source=tuicool&utm_medium=referral)
1. [持久层框架之MyBatis](http://www.cnblogs.com/1315925303zxz/p/6374572.html)



## Tutorial

---

1. [史上最简单的 MyBatis 教程（一）**](http://blog.csdn.net/qq_35246620/article/details/54811589)
1. [史上最简单的 MyBatis 教程（二）](http://blog.csdn.net/qq_35246620/article/details/54834989)
1. [史上最简单的 MyBatis 教程（三）](http://blog.csdn.net/qq_35246620/article/details/54837618)
1. [史上最简单的 MyBatis 教程（四）](http://blog.csdn.net/qq_35246620/article/details/54849904)
1. [持久化框架-Mybatis简介与原理](http://blog.csdn.net/jiuqiyuliang/article/details/45286191)
1. [MyBatis入门](http://www.jianshu.com/p/e4199b734cab)
1. [浅尝：myBatis](http://www.jianshu.com/p/e9a8dea6cea7)
1. [MyBatis完全使用指南](http://www.jianshu.com/p/1c7c7d1bba33)
1. [mybatis实战教程(mybatis in action),mybatis入门到精通](http://blog.csdn.net/techbirds_bao/article/details/9233599/)
1. [MyBatis学习 之 一、MyBatis简介与配置MyBatis+Spring+MySql](http://limingnihao.iteye.com/blog/781671)