# GreenDao note


## 简介

---

>greenDAO is an object/relational mapping (ORM) tool for Android. It offers an object oriented interface to the relational database SQLite. ORM tools like greenDAO do many repetitive tasks for you and offer a simple interface to your data.

greenDao是一个将对象映射到关系数据库(SQLite)中的轻量且快速的ORM解决方案。

![greenDao图](http://greenrobot.org/wordpress/wp-content/uploads/greenDAO-orm-640.png)


特性:

* 性能最大化, Maximum performance (probably the fastest ORM for Android); our benchmarks are open sourced too
* 易于使用的 APIs, Easy to use powerful APIs covering relations and joins
* 内存开销最小化, Minimal memory consumption
* 库很小, Small library size (<100KB) to keep your build times low and to avoid the 65k method limit
* 数据库加密, Database encryption: greenDAO supports SQLCipher to keep your user’s data safe


注意:
数据库内的字段名称和属性名称规则是不一样的, todo...XXX.


## 配置

---

### 基本配置

build.gradle(module):

```
apply plugin: 'org.greenrobot.greendao'
...
...
greendao {
    //数据库的schema版本，也可以理解为数据库版本号
    schemaVersion 1
    //设置DaoMaster、DaoSession、Dao包名，也就是要放置这些类的包的全路径。
    daoPackage 'com.lluo.greendaostudy.greendao'
    //设置DaoMaster、DaoSession、Dao目录
    targetGenDir 'src/main/java'
}

dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    ...
    compile 'org.greenrobot:greendao:3.2.0'
    compile'org.greenrobot:greendao-generator:3.2.0'
}
```

build.gradle(project):

```
buildscript { 
    repositories { 
        mavenCentral()    
}    
dependencies {
    classpath 'org.greenrobot:greendao-gradle-plugin:3.2.0'    
    }
}
```

### 代码自动生成的配置

3.0之前,需要另外创建一个library,在library里面通过编程指定要创建的实体的字段,然后运行该library,实体对象随之创建. 3.0之后自己创建实体类,然后再使用注解@Entity修饰, 编译后GreenDao会创建相应的Dao,同时自动修改该实体类.


对于3.0之后, 需要在build.gradle(App)里引入greendao插件,同时指定greenDao自动生成代码的一些配置, 比如生成代码的包名,目标目录,生成的数据库的版本号,配置如下:

```
apply plugin: 'org.greenrobot.greendao'
...
greendao {
    //数据库的schema版本，也可以理解为数据库版本号
    schemaVersion 1
    //设置DaoMaster、DaoSession、Dao包名，也就是要放置这些类的包的全路径。
    daoPackage 'com.lluo.greendaostudy.greendao'
    //设置DaoMaster、DaoSession、Dao目录
    targetGenDir 'src/main/java'
}

````

比如下面, 编译后,greenDao除了自动创建了DaoMaster,DaoSession,还会创建User1对应的User1Dao,同时会对User1进行一些修改:

```
@Entity
public class User1 {
    @Id
    private Long id;
    @Property(nameInDb = "UserName")
    private String name;
    private int age;
    @Generated(hash = 479634891)
    public User1(Long id, String name, int age) {
        this.id = id;
        this.name = name;
        this.age = age;
    }
    @Generated(hash = 1224450628)
    public User1() {
    }
```

## 核心类

---

核心类如下:

### DaoMaster
The entry point for using greenDAO. DaoMaster holds the database object (SQLiteDatabase) and manages DAO classes (not objects) for a specific schema. It has static methods to create the tables or drop them. Its inner classes OpenHelper and DevOpenHelper are SQLiteOpenHelper implementations that create the schema in the SQLite database.

它是使用greenDao的入口, 它持有一个数据库对象(SQLiteDatabase),管理所有特定schema的DAO类.有一个静态的方法创建和drop数据库表。它的内部类OpenHelper和DevOpenHelper是SQLiteOpenHelper的实现类，用于创建SQLite数据库的模式。

### DaoSession
Manages all available DAO objects for a specific schema, which you can acquire using one of the getter methods. DaoSession provides also some generic persistence methods like insert, load, update, refresh and delete for entities. Lastly, a DaoSession objects also keeps track of an identity scope. For more details, have a look at the session documentation.

管理指定schema下所有可用的DAO对象，你可以通过某个get方法获取到。DaoSession提供一些通用的持久化方法，比如对实体进行插入，加载，更新，刷新和删除。最后DaoSession对象会跟踪identity scope.

### DAOs
Data access objects (DAOs) persists and queries for entities. For each entity, greenDAO generates a DAO. It has more persistence methods than DaoSession, for example: count, loadAll, and insertInTx.

数据访问对象，用于实体的持久化和查询。对于每一个实体，greenDao会生成一个DAO，相对于DaoSession它拥有更多持久化的方法.
插入,删除,更新,查询都可以通过它执行.

### Entities
Persistable objects. Usually, entities are objects representing a database row using standard Java properties (like a POJO or a JavaBean).

实体对象,表示的是数据库里的实体对应的类,它的属性对应于数据库表中的某一列. 3.0之前,需要另外创建一个library,在library里面通过编程指定要创建的实体的字段,然后运行该library,实体对象随之创建. 3.0之后自己创建实体类,然后再使用注解@Entity修饰, 编译后GreenDao会创建相应的Dao,同时自动修改该实体类.

关系图如下:

![核心类图](http://greenrobot.org/wordpress/wp-content/uploads/Core-Classes-150.png)


## 核心的初始化

---

Finally, the following code example illustrates the first steps to initialize the database and the core greenDAO classes:

```
// do this once, for example in your Application class
helper = new DaoMaster.DevOpenHelper(this, "notes-db", null);
db = helper.getWritableDatabase();
daoMaster = new DaoMaster(db);
daoSession = daoMaster.newSession();
// do this in your activities/fragments to get hold of a DAO
noteDao = daoSession.getNoteDao();
```

The example assumes a Note entity exists. With its DAO ( noteDao object), we can call the persistence operation for this specific entity.

** DaoMaster和DaoSession可以做成单件, 在需要的时候创建. **

## 常用的几个类
1. QueryBuilder  
Builds custom entity queries using constraints and parameters and without SQL (QueryBuilder creates SQL for you). To acquire an QueryBuilder, use AbstractDao.queryBuilder() or AbstractDaoSession.queryBuilder(Class).

    使用它可以创建自定义的查询, 使用AbstractDao.queryBuilder()或者AbstractDaoSession.queryBuilder(Class)创建.

1. Property  
Meta data describing a property mapped to a database column; used to create WhereCondition object used by the query builder.

    一个Dao中的属性,对应数据库中的一列,实体类的一个属性,可以使用XxxDao.Properties.xxx获取. 提供了一些类似sql条件查询的方法,比如eq,notEq,like,between,in,notIn,gt,lt,ge,le,isNull,notIsNull等方法.这些方法都返回类型WhereCondition.

1. WhereCondition  
Internal interface to model WHERE conditions used in queries. Use the {@link Property} objects in the DAO classes to create new conditions.

    使用Property的条件方法返回WhereCondition,表示某种条件. 比如:

    ```
    WhereCondition condition = StudentDao.Properties.Id.gt(40);
    ```


## Entities创建及注解

---

实体对象Entity,相当于平常的Bean,它的属性对应于数据库表中的某一列. 

3.0之前,需要另外创建一个library,在library里面通过编程指定要创建的实体的字段,然后运行该library,实体对象随之创建. 3.0之后自己创建实体类,然后再使用注解@Entity修饰, 编译后GreenDao会创建相应的Dao,同时自动修改该实体类.
GreenDao3.0之后可以自己创建entity实体,然后使用注解创建greenDao对应的实体XXXDao.

比如下面, 编译后,greenDao除了自动创建了DaoMaster,DaoSession,还会创建User1对应的User1Dao,同时会对User1进行一些修改:

```
@Entity
public class User1 {
    @Id
    private Long id;
    @Property(nameInDb = "UserName")
    private String name;
    private int age;
    @Generated(hash = 479634891)
    public User1(Long id, String name, int age) {
        this.id = id;
        this.name = name;
        this.age = age;
    }
    @Generated(hash = 1224450628)
    public User1() {
    }
```

### 常用注解

注解|说明
-|-
@Entity|用于标识将这个类变成greendao的持久化类
@Id|标明主键，括号里可以指定是否自增.
@Property|用于设置属性在数据库中的列名.如果缺少，greendao会使用这个变量名来命名数据库的列名(全部大写，并且使用下划线来代替驼峰命名法，eg:customName会变成CUSTOM_NAME)。
@NotNull|非空，通常情况下修饰基础类型，比如long,int,short,byte，当然也可以是包装类.
@Transient|表示该属性不是持久化的，仅仅作为临时变量使用.

例子:

```
@Entity
public class User1 {
    @Id
    private Long id;
    @Property(nameInDb = "UserName")
    private String name;
    private int age;
    @Generated(hash = 479634891)
    public User1(Long id, String name, int age) {
        this.id = id;
        this.name = name;
        this.age = age;
    }
    @Generated(hash = 1224450628)
    public User1() {
    }
```

*** 编译后, GreenDao会往标示为@Entity的User1添加一些其他方法,同时产生一个User1Dao类. ***


### 实体类注解

```
@Entity(
        schema = "myschema",
        active = true,
        nameInDb = "AWESOME_USERS",
        indexes = {
            @Index(value = "name DESC", unique = true)
        },
        createInDb = false
)

public class User {
  ...
}
```

其中:

注解|说明
-|-
schema|一个项目中有多个schema时 标明要让这个dao属于哪个schema
active|是标明是否支持实体类之间update，refresh，delete等操作
nameInDb|就是写个存在数据库里的表名（不写默认是一致）
indexes|定义索引，这里可跨越多个列
CreateInDb|如果是有多个实体都关联这个表，可以把多余的实体里面设置为false避免重复创建（默认是true）


### 索引注解

```
@Entity
public class User {
    @Id private Long id;
    @Index(unique = true)
    private String name;
}
```
 
其中:

注解|说明
-|-
@Index|通过这个字段建立索引
@Unique|添加唯一约束，上面的括号里unique=true作用相同


### 关系注解

```
@Entity
public class Order {
    @Id private Long id;
    private long customerId;

    @ToOne(joinProperty = "customerId")
    private Customer customer;
}
  
@Entity
public class Customer {
    @Id private Long id;
}
```

注解|说明
-|-
@ToOne|是将自己的一个属性与另一个表建立关联
@ToMany|的使用场景有些多， @ToMany的属性referencedJoinProperty，类似于外键约束。
@JoinProperty|对于更复杂的关系，可以使用这个注解标明目标属性的源属性。
@JoinEntity|如果你在做多对多的关系，有其他的表或实体参与，可以给目标属性添加这个额外的注解（感觉不常用吧）


### 与代码生成相关的注解
在greenDao3中，实体类是开发者创建和编辑的。然而,在代码生成过程中greenDAO可能会更改开发者创建的包含@Entity修饰的类的代码.

1. @Generated  
这个是build后greendao自动添加的，这个注解理解为防止重复，每一块代码生成后会加个hash作为标记。 官方不建议你去碰这些代码，改动会导致里面代码与hash值不符。如果手动改变引发错误.

    也就是说这个是greenDao在生成代码的时候自动添加的,提示用户不能修改这块代码,修改了会报错.

1. @keep  
告诉编译器, 在下一次运行产生dao代码期间，被该注解标记的，保持不变,编译器不要去覆盖它. 这种情况往往是第一次是编译器自动生成的,然后开发人员自己改了一些,然后期望后面编译器再编译时不会再去盖图该部分.

    *** 注意：不要使用在类的成员变量上(如果你不清楚它的用法)，当model发生改变，不会同步到生成的dao文件中.***


## What's identity scope

---

todo...XXX

部分信息见[Identity scope and session “cache”](http://greenrobot.org/greendao/documentation//sessions/).


## 操作

---

### 添加

Dao里可以使用的方法有:

方法|说明
-|:-
long insert(T entity)|插入指定实体
void insertInTx(T... entities)|使用事务批量插入
void insertInTx(java.lang.Iterable<T> entities)|使用事务批量插入
void insertInTx(java.lang.Iterable<T> entities, boolean setPrimaryKey)|使用事务操作，将给定的实体集合插入数据库，并设置是否设定主键.
long insertWithoutSettingPk(T entity)|插入指定实体，无主键
long insertOrReplace(T entity)|将给定的实体插入数据库，若此实体类存在，则覆盖
void insertOrReplaceInTx(T... entities)|使用事务操作，将给定的实体插入数据库，若此实体类存在，则覆盖.
void insertOrReplaceInTx(java.lang.Iterable<T> entities)|使用事务操作，将给定的实体插入数据库，若此实体类存在，则覆盖.
void insertOrReplaceInTx(java.lang.Iterable<T> entities, boolean setPrimaryKey)|使用事务操作，将给定的实体插入数据库，若此实体类存在，则覆盖并设置是否设定主键.
void save(T entity)|将给定的实体插入数据库，若此实体类存在，则更新  
void saveInTx(T... entities)|使用事务操作，将给定的实体插入数据库，若此实体类存在，则更新.
void saveInTx(java.lang.Iterable<T> entities)|将给定的实体插入数据库，若此实体类存在，则更新.



单个插入:

```
Student student = new Student(null,"louis"+randValue,randValue);
MyApplication.getApp().getDaoSession().getStudentDao().insert(student);
```


批量插入:

```
//使用多个参数批量删除
int tempNum = ++randValue;
Student stu1= new Student(null,"louis"+tempNum,tempNum);
tempNum++;
Student stu2= new Student(null,"louis"+tempNum,tempNum);
stuDao.insertInTx(stu1, stu2);

//使用Iterable参数批量删除
List<Student> list = new ArrayList<>(2);
for (int i=0; i<2; i++){
    tempNum++;
    list.add(new Student(null,"louis"+tempNum,tempNum));
}
stuDao.insertInTx(list);
```

### 删除

可以使用的方法如下:

方法|说明
-|:-
void deleteAll()| 删除所有
void delete(T entity)|删除指定的实体
void deleteInTx(T... entities)|批量删除
void deleteInTx(java.lang.Iterable<T> entities)|批量删除
void deleteByKey(K key)|删除指定主键对应的实体
void deleteByKeyInTx(K... keys)|使用事务操作删除数据库中给定的所有key所对应的实体  
void deleteByKeyInTx(java.lang.Iterable<K> keys)|使用事务操作删除数据库中给定的所有key所对应的实体

使用delete(T entity)例子如下:

```
StudentDao studentDao = MyApplication.getApp().getDaoSession().getStudentDao();
Student stu = new Student(new Long(nID),"louis244",244);
studentDao.delete(stu);
```
StudentDao studentDao = MyApplication.getApp().getDaoSession().getStudentDao();

通过测试发现,上面的方法只要entity的主键id相同就可以,其他可以不同.


也可以使用deleteByKey按主键删除:

```
studentDao.deleteByKey(new Long(39));
```

使用deleteAll()删除全部.

也可以使用DeleteQuery批量删除, 例子如下:

```
QueryBuilder<Student> qb = studentDao.queryBuilder();
DeleteQuery<Student> bd = qb.where(StudentDao.Properties.Age.le(500)).buildDelete();
bd.executeDeleteWithoutDetachingEntities();
```


### 修改
可以使用的方法如下:

方法|说明
-|:-
void update(T entity)|更新给定的实体
void updateInTx(T... entities)|使用事务操作，更新给定的实体.
void updateInTx(java.lang.Iterable<T> entities)|使用事务操作，更新给定的实体.

其实insertOrReplace(xxxx),insertOrReplaceInTx(xxx),save(xx),saveInTx(xxx)当数据已经存在的时候也起到更新的作用.

例子:

```
StudentDao dao = MyApplication.getApp().getDaoSession().getStudentDao();
dao.update(new Student(new Long(stuID),"LouisUpdate", stuID));  //使用update更新
//dao.save(new Student(new Long(stuID), "LouisUpdate2", stuID));  //使用save更新
```


### 查询

#### QueryBuilder查询

>public class QueryBuilder<T>:  
>Builds custom entity queries using constraints and parameters and without SQL (QueryBuilder creates SQL for you). To acquire an QueryBuilder, use AbstractDao.queryBuilder() or AbstractDaoSession.queryBuilder(Class).Entity properties are referenced by Fields in the "Properties" inner class of the generated DAOs. This approach allows compile time checks and prevents typo errors occuring at build time.

使用QueryBuilder自定义查询实体，而不是再写繁琐的SQL语句，避免了SQL语句的出错率。Queries支持lazy-loading的查询结果。

>当处理一个较大的结果集时，lazy-loading（懒加载模式）可以节省内存提高性能。

常用方法:

方法|说明
-|:-
QueryBuilder<T>  queryBuilder()|获取QueryBuilder
QueryBuilder<T>  where(WhereCondition cond, WhereCondition... condMore)|条件，AND 连接
QueryBuilder<T>  whereOr(WhereCondition cond1, WhereCondition cond2, WhereCondition... condMore)| 条件，OR 连接
QueryBuilder<T>  distinct()| 去重，例如使用联合查询时
QueryBuilder<T>  limit(int limit)| 限制返回数
QueryBuilder<T>  offset(int offset)|偏移结果起始位，配合limit(int)使用
QueryBuilder<T>  orderAsc(Property... properties)| 排序，升序
QueryBuilder<T>  orderDesc(Property... properties)| 排序，降序
QueryBuilder<T>  orderCustom(Property property, java.lang.String customOrderForProperty)| 排序，自定义
QueryBuilder<T>  orderRaw(java.lang.String rawOrder)|排序，SQL 语句
QueryBuilder<T>  preferLocalizedStringOrder()|地地化字符串排序，用于加密数据库无效
QueryBuilder<T>  stringOrderCollation(java.lang.String stringOrderCollation)|自定义字符串排序，默认不区分大小写
WhereCondition  and(WhereCondition cond1, WhereCondition cond2, WhereCondition... condMore)|条件，AND 连接
WhereCondition  or(WhereCondition cond1, WhereCondition cond2, WhereCondition... condMore)|条件，OR 连接

单个条件查询:

```
StudentDao stuDao = MyApplication.getApp().getDaoSession().getStudentDao();
Query<Student> query = stuDao.queryBuilder().where(StudentDao.Properties.Id.gt(40)).build();
```

多个条件:

```
StudentDao stuDao = MyApplication.getApp().getDaoSession().getStudentDao();
List<Student> list = stuDao.queryBuilder().where(StudentDao.Properties.Id.le(23), StudentDao.Properties.Age.gt(800)).build().list();

or:
QueryBuilder queryBuild = stuDao.queryBuilder();
//age>900, 并且 name等于louis984或者id=15
queryBuild.where(StudentDao.Properties.Age.gt(900)
            ,queryBuild.or(StudentDao.Properties.Name.eq("louis984"),StudentDao.Properties.Id.eq(15)));
List<Student> list = queryBuild.build().list();

```

XxxDao.where或者XxxDao.whereOr都可以指定一个或者多个条件,前者是and关系,后者是or关系.


#### SQL查询

有下面几种方法:

##### 通过QueryBuilder和WhereCondition.StringCondition

在StringCondition里面指定查询条件.

例子:

```
StudentDao stuDao = MyApplication.getApp().getDaoSession().getStudentDao();

Query<Student> query = stuDao.queryBuilder().where(new WhereCondition.StringCondition(StudentDao.Properties.Age.columnName+"<100 and "+StudentDao.Properties.Id.columnName+">100")).build();
List<Student> list = query.list();
...
query = stuDao.queryBuilder().where(new WhereCondition.StringCondition(StudentDao.Properties.Age.columnName+"<? and "+StudentDao.Properties.Id.columnName+">?",100,70)).build();
list = query.list();
```

StringCondition的构造:

```
StringCondition(java.lang.String string) 
StringCondition(java.lang.String string, java.lang.Object... values) 
StringCondition(java.lang.String string, java.lang.Object value) 
```

##### 使用AbstractDao的queryRaw(),queryRawCreate()或者queryRawCreateListArgs

这几个都返回相应实体的list.

方法名|说明
-|-
java.util.List<T>	queryRaw(java.lang.String where, java.lang.String... selectionArg)|A raw-style query where you can pass any WHERE clause and arguments.
Query<T>	queryRawCreate(java.lang.String where, java.lang.Object... selectionArg)| Creates a repeatable Query object based on the given raw SQL where you can pass any WHERE clause and arguments.
Query<T>	queryRawCreateListArgs(java.lang.String where, java.util.Collection<java.lang.Object>selectionArg)|Creates a repeatable Query object based on the given raw SQL where you can pass any WHERE clause and arguments.


queryRaw例子:

```
StudentDao stuDao = MyApplication.getApp().getDaoSession().getStudentDao();
List<Student> list = stuDao.queryRaw("where "+StudentDao.Properties.Id.columnName+"<20");
```

queryRawCreate例子:

```
StudentDao stuDao = MyApplication.getApp().getDaoSession().getStudentDao();
int nID = 100;
String sql = "WHERE " + StudentDao.Properties.Id.columnName+ "<?";
List<Student> list = stuDao.queryRawCreate(sql, nID).list();
```

queryRawCreate后面的参数是不定数,queryRawCreateListArgs后面的参数是list,queryRawCreate的实现也是调用queryRawCreateListArgs.

** queryRawCreate参数为""则返回全部. **


##### 使用database的方法

使用DaoMaster.getDatabase()获取的Database的方法, Database方法如下:

```
public interface Database {
    Cursor rawQuery(String sql, String[] selectionArgs);
    void execSQL(String sql) throws SQLException;
    void beginTransaction();
    void endTransaction();
    boolean inTransaction();
    void setTransactionSuccessful();
    void execSQL(String sql, Object[] bindArgs) throws SQLException;
```


#### DeleteQuery删除查询

为了执行批量删除，需要创建一个QueryBuilder，调用它的buildDelete方法，它会返回一个DeleteQuery。将来这部分的API可能会有所变化，比如，添加一些更加便利的方法。记住，目前的批量删除不会影响到identity scope中的实体。例如，如果实体在之前已经被缓存了，你可以“复活”这些删除掉的实体，并且可以通过他们的ID访问（load方法）。如果你在使用中遇到问题，你可以考虑清除identity scope。

方法executeDeleteWithoutDetachingEntities执行delete操作,但是在identity scope中还是能找到的.

>public void executeDeleteWithoutDetachingEntities()  
>
>Deletes all matching entities without detaching them from the identity scope (aka session/cache). Note that this method may lead to stale entity objects in the session cache. Stale entities may be returned when loaded by their primary key, but not using queries.

使用方法:

1. 使用XxxDao.queryBuilder()获取QueryBuilder.
1. 使用buildDelete获取DeleteQuery.
1. 调用executeDeleteWithoutDetachingEntities执行删除操作

例子:

```
QueryBuilder<Student> qb = studentDao.queryBuilder();
DeleteQuery<Student> bd = qb.where(StudentDao.Properties.Age.le(500)).buildDelete();
bd.executeDeleteWithoutDetachingEntities();
```


### 查询复用(Executing Queries multiple times)

一旦你使用QueryBuilder创建了一个查询，这个Query对象可以在后面的查询中重用。这样比每次创建一个新的Query对象更高效。

如果查询参数没有改变，你只需要再次调用list/unique方法中的一个。如果参数改变了，你必须调用setParameter方法设置每个改变的参数。目前，每个参数都是从0开始索引。该索引是基于你传递到QueryBuilder中的参数。

下面是参数不变的, 下次再用直接调用mQuery1.list()就可以了:

```
private Query<Student> mQuery1;
...
if (mQuery1==null){
    mQuery1 = stuDao.queryBuilder().where(StudentDao.Properties.Id.gt(50),StudentDao.Properties.Age.le(500)).build();
}

List<Student> list = mQuery1.list();
```

下面是参数会变的,需要调用setParameter方法设置每个改变的参数。目前，每个参数都是从0开始索引。该索引是基于你传递到QueryBuilder中的参数:

```
private Query<Student> mQuery2;
...
if (mQuery2==null){
    mQuery2 = stuDao.queryBuilder().where(StudentDao.Properties.Id.gt(50),StudentDao.Properties.Age.le(500)).build();
}

int age = 100+new Random().nextInt(10);
mQuery2.setParameter(0, 30);
mQuery2.setParameter(1, age);
list = mQuery2.list();
```


#### 联合查询

todo...XXX



## 升级处理

---

todo...XXX

关于DevOpenHelper


## 不同表相互之间有关联 怎么处理????


---

todo...XXX


## RxJava支持

---

GreenDao 3.X已集成RxJava，其中，RxDao<T，K> 和RxQuery<T>便是GreenDao 3.X中RxJava的核心操作类。其最大的特点就是在增删改查等基本操作时返回Observable.

### RxDao

>public class RxDao<T, K> extends RxBase  
>Like AbstractDao but with Rx support. Most methods from AbstractDao are present here, but will return an Observable. Modifying operations return the given entities, so they can be further processed in Rx.

和AbstractDao类似,只是它的方法返回值是Observable<X>.

RxDao<T, K>中, T表示的是实体Entity的类型,K表示的是主键类型,如果没有主键, 使用Void.

### RxQuery

>class RxQuery<T> extends RxBase  
>Gets org.greenrobot.greendao.query.Query results in Rx fashion.

以Rx的方式获取Query, 它的方法的返回值是Observalbe<X>.


### 获取RxXX对象的方法

获取RxDao对象:

```
RxDao<T，K> mRxDao = xxDao.rx(); // 返回一个默认subscribeOn在IO线程的RxDao,其中T是实体类，K主键类型.
RxDao<T，K> mRxDao = xxDao.rxPlain();// 返回一个未设置默认订阅的RxDao.
```

例子:

```
DbManager.getDaoSession(context).getStudentDao().rx().delete(student); 
```

获取RxQuery对象:

```
RxQuery<T> mRxQuery = xxQueryBuilder.rx();  
RxQuery<T> mRxQuery = xxQueryBuilder.rxPlain();  
```

即通过queryBuilder()获取QueryBuilder对象后再调用rx()或者rxPlain(),例子如下:

```
RxQuery<Student> rxQuery = DbManager.getDaoSession(context).getStudentDao().queryBuilder().rx(); 
```

例子:

```
 StudentDao stuDao = MyApplication.getApp().getDaoSession().getStudentDao();
 RxQuery<Student> rxQuery = stuDao.queryBuilder().where(StudentDao.Properties.Age.gt(50), StudentDao.Properties.Age.le(100)).rx();
 rxQuery.list().subscribe(new Action1<List<Student>>() {
     @Override
     public void call(List<Student> students) {
         Log.i(TAG, "RxOperate get result:");
         printList(students);
     }
 });
 
 stuDao.rx().count().subscribe(new Action1<Long>() {
     @Override
     public void call(Long aLong) {
         Log.i(TAG, "total count:"+aLong.intValue());
     }
 });
```


## 惰性加载(lazy load)

---

Limit、Offset、Pagination....

todo...XXX


## 其他

1. 查询故障处理  
如果查询结果没有返回你期待的，有两个静态标识可开启QueryBuilder的SQL和参数的日志输出：

    ```
    QueryBuilder.LOG_SQL = true;        //Set to true to debug the SQL.
    QueryBuilder.LOG_VALUES = true;     //Set to see the given values.
    ```
    这样会输出SQL命令和调用相关build方法时传入的参数。

1.

## Reference

---

1. [GreeDao JavaDoc](http://greenrobot.org/greendao/documentation/javadoc/)
1. [greenDAO Documentation](http://greenrobot.org/greendao/documentation/)
1. [GreenDAO](http://blog.csdn.net/wds1181977/article/details/51584052)
1. [GreenDao 使用](http://www.jianshu.com/p/189377822376)
1. [GreenDao 3.X之基本使用**](http://blog.csdn.net/io_field/article/details/52214099)
1. [GreenDao3 使用说明**](http://www.jianshu.com/p/4e6d72e7f57a)
1. [Greendao Query and QueryBuilder](http://vjson.com/wordpress/greendao-query-and-querybuilder.html)
1. [greenDAO讲义（二）：数据库查询篇](https://my.oschina.net/cheneywangc/blog/196360)
1. [greenDAO官方文档翻译--使用教程](http://blog.sina.com.cn/s/blog_af5cfb030102w20v.html)
1. [GreenDAO 3.x — Android ORM框架(二)](https://kevindgk.github.io/android/greendao/GreenDAO%203.x%E5%AE%98%E6%96%B9%E6%96%87%E6%A1%A3%20%E2%80%94%20Android%20ORM%E6%A1%86%E6%9E%B6(%E4%BA%8C)/)