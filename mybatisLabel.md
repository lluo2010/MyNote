# Mybatis一些标签 note

## 一. CASE..when..end

具有两种格式： 

### 简单 CASE

函数将某个表达式与一组简单表达式进行比较以确定结果。

语法 :

```
CASE input_expression
    WHEN when_expression THEN result_expression
        [ ...n ]
    [
        ELSE else_result_expression
    END 
```
返回结果中， value=compare-value 

例子：

```
SELECT CASE 1 
            WHEN 1 THEN 'one' 
            WHEN 2 THEN 'two' 
            ELSE 'more' 
       END
```
输出：’one’

```
SELECT CASE 
          WHEN 1>0 THEN 'true' 
          ELSE 'false' 
       END;
```
输出： ‘true’


### 使用带有简单 CASE 函数和 CASE 搜索函数的SELECT 语句 

CASE 搜索函数计算一组布尔表达式以确定结果。 

语法：

```
CASE    
    WHEN Boolean_expression THEN result_expression
    [ ...n ]
    ELSE else_result_expression
END
```

例子：

```
WHEN IFNULL(tableA.name, '') != '' THEN
    (
        SELECT
            lang.NAME
        FROM
             commonitem_lang lang
        WHERE
            '123456789' = lang.ID
        AND lang.KEY = 'K6'
    )
WHEN IFNULL(tableA.name, '') = '' THEN
    (
        SELECT
            lang.NAME
        FROM
             commonitem_lang lang
        WHERE
            '987654321' = lang.ID
        AND lang.KEY = 'K7'
    )
END AS PWNAME
```
注意第二种情况CASE后面直接是When，即需要判断的条件。




        select su.channel, due.start_time, due.due_capital, su.add_time 
        from (select user_id, start_time, due_capital from s_user_due_detail where due_capital=500) due 
        left join (select * from s_user) su
        on su.id = due.user_id;
        GROUP BY su.channel;



此时需要生成(**,***,)的形式



## 二. foreach

foreach的主要用在构建in条件中，它可以在SQL语句中进行迭代一个集合。可以理解为传进一批数据， sql语句里构建一个(xx,xx,yy,yy2),然后指定是否in在里面。

foreach元素的属性主要有 item，index，collection，open，separator，close:

1. item表示集合中每一个元素进行迭代时的别名，
1. index指定一个名字，用于表示在迭代过程中，每次迭代到的位置，
1. open表示该语句以什么开始，
1. separator表示在每次进行迭代之间以什么符号作为分隔符，
1. close表示以什么结束.

因为一般需要构建一个(x1,x2,x3), 所以open一般为‘(’,close为‘)’,separator为‘,’.

在使用foreach的时候最关键的也是最容易出错的就是collection属性，该属性是必须指定的，但是在不同情况下，该属性的值是不一样的，

主要有一下3种情况：

1. 如果传入的是单参数且参数类型是一个List的时候，collection属性值为list
2. 如果传入的是单参数且参数类型是一个array数组的时候，collection的属性值为array
3. 如果传入的参数是多个的时候，我们就需要把它们封装成一个Map了，当然单参数也可以封装成map，实际上如果你在传入参数的时候，在breast里面也是会把它封装成一个Map的，map的key就是参数名，所以这个时候collection属性值就是传入的List或array对象在自己封装的map里面的key



### 例子

### 1. 单参数List的类型：    

```
<select id="dynamicForeachTest" resultType="Blog">     
    select * from t_blog where id in      
   <foreach collection="list" index="index" item="item" open="(" separator="," close=")">    
         #{item}   
      </foreach>  
 </select>
```

上述collection的值为list，对应的Mapper是这样的 public List<Blog> dynamicForeachTest(List<Integer> ids);

测试代码：

```
 @Test   
  public void dynamicForeachTest() {    
       SqlSession session = Util.getSqlSessionFactory().openSession();      
       BlogMapper blogMapper = session.getMapper(BlogMapper.class);    //这里使用了注解模式
       List<Integer> ids = new ArrayList<Integer>();      
       ids.add(1);  
       ids.add(3); 
       ids.add(6);      
       List<Blog> blogs = blogMapper.dynamicForeachTest(ids);      
       for (Blog blog : blogs)     
         System.out.println(blog);  
         session.close();  
   }
```

*** 上面其实就是传一个指定的list进去，然后SQL语气查询所有id都在(in)指定的list里的记录。***

### 2. 单参数array数组的类型：

```
<select id="dynamicForeach2Test" resultType="Blog">   
    select * from t_blog where id in        
    <foreach collection="array" index="index" item="item" open="(" separator="," close=")">      
        #{item}       
    </foreach>   
</select>
```

测试代码基本同上，就是传入参数变为数组：

```
...
int[] ids = new int[] {1,3,6,9};     
List<Blog> blogs = blogMapper.dynamicForeach2Test(ids);     
...
```

### 3. map



foreach元素的属性主要有 item，index，collection，open，separator，close。

    item表示集合中每一个元素进行迭代时的别名，
    index指 定一个名字，用于表示在迭代过程中，每次迭代到的位置，
    open表示该语句以什么开始，
    separator表示在每次进行迭代之间以什么符号作为分隔 符，
    close表示以什么结束。


在使用foreach的时候最关键的也是最容易出错的就是collection属性，该属性是必须指定的，但是在不同情况 下，该属性的值是不一样的，主要有一下3种情况：

1. 如果传入的是单参数且参数类型是一个List的时候，collection属性值为list
2. 如果传入的是单参数且参数类型是一个array数组的时候，collection的属性值为array
3. 如果传入的参数是多个的时候，我们就需要把它们封装成一个Map了，当然单参数也可