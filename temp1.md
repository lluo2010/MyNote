SQLAlchemy


- [SqlAlchemy个人学习笔记完整汇总](http://www.cnblogs.com/harrychinese/archive/2012/09/12/My_Own_Tutorial_For_SqlAlchemy.html)
- [SQLAlchemy不负责指南](http://aljun.me/post/13)
- [SQLAlchemy ORM教程之一：Create](http://www.jianshu.com/p/0d234e14b5d3)








账号投资银行卡投资，都在due_detail里查询。

回款，奖励，充值，提现都在wallet查询。
其中回款，奖励，充值都都是进， type=1;

回款： type=1, pay_type=0
奖励： type=1, pay_type=3.
充值：type=1,pay_status =2;

提现： type=2, status:转出状态(1:正常/2:交易中/3:等待处理)，
type=2还有一种情况是账户购买， 
提现为type=2并且recharge_no以TX开头


钱包查询

select add_time as time, value as amount, 
		CASE
			WHEN type=1 and pay_type=0 THEN '回款'
			WHEN type=1 and pay_status=2 THEN '充值'
			WHEN type=1 and recharge_no like 'JL%' THEN '奖励'
			WHEN type=2 and recharge_no like 'TX%' THEN '提现'
		    ELSE '其他'
		END typeName,
		CASE
            WHEN type=1 THEN '交易成功'
			WHEN recharge_no like 'TX%' AND status=1  THEN '已到账'
			WHEN recharge_no like 'TX%' AND status!=1 THEN '等待处理'
            ELSE '交易成功'
		END statusName
 from s_user_wallet_records where user_id = 8  and not (type=2 and recharge_no like 'QB%') order by id desc

 select add_time as time, due_capital as amount, 
		CASE
			WHEN from_wallet=1 THEN '余额投资'
		    ELSE '银行卡投资'
		END typeName,
		'交易成功' as statusName
from s_user_due_detail where user_id = 8 order by id desc limit 10;



/*

回款： type=1, pay_type=0
奖励： type=1, pay_type=3.
充值：type=1,pay_status =2;

*/


0:全部, 1:充值,2:提现,3:余额投资,4:银行卡投资,5:回款回款 6:奖励


充值,提现,余额投资,银行卡投资,回款回款,奖励


CASE (Transact-SQL)
https://docs.microsoft.com/en-us/sql/t-sql/language-elements/case-transact-sql


奖励：
SUserWalletRecords
pay_type = 3;
pay_status = 2;


SUserWalletRecords




CASE

　　WHEN condition1 THEN result1
　　WHEN condistion2 THEN result2
　　...
　　WHEN condistionN THEN resultN
　　ELSE default_result
END


select product_id,product_type_id,
　　case
    　　when product_type_id=1 then 'Book'
    　　when product_type_id=2 then 'Video'
    　　when product_type_id=3 then 'DVD'
    　　when product_type_id=4 then 'CD'
    　　else 'Magazine'
　　end
from products 




user/fundRecordDetail.json

pay_type = 0; 表示回款
pay_status=2表示已支付。




0:全部
1:充值
2:提现
3:余额投资
4:银行卡投资
5:回款回款
6:奖励

	@POST
	@Path("/walletDetail")
	public JsonResult walletDetail(@FormParam("token") String token,@FormParam("pageNo") String pageNo,@FormParam("type") String type);


充值,提现,余额投资,银行卡投资,回款,奖励


select add_time as time, value as amount,
            CASE
                WHEN type=1 and pay_type=0 THEN '回款'
                WHEN type=1 and pay_status=2 THEN '充值'
                WHEN type=1 and recharge_no like 'JL%' THEN '奖励'
                WHEN type=2 and recharge_no like 'TX%' THEN '提现'
                ELSE '其他'
            END typeName,
            CASE
                WHEN type=1 THEN '交易成功'
                WHEN recharge_no like 'TX%' AND status=1  THEN '已到账'
                WHEN recharge_no like 'TX%' AND status!=1 THEN '等待处理'
                ELSE '交易成功'
            END statusName
        from s_user_wallet_records
        where 1 = 1
        <if test="userId != null">
            and user_id = #{userId}
        </if>
        and not (type=2 and recharge_no like 'QB%')

        UNION

        select add_time as time, due_capital as amount,
            CASE
                WHEN from_wallet=1 THEN '余额投资'
                ELSE '银行卡投资'
            END typeName, '交易成功' as statusName
        from s_user_due_detail
        where 1 = 1
        <if test="userId != null">
            and user_id = #{userId}
        </if>

充值：


/*

回款： type=1, pay_type=0
奖励： type=1, pay_type=3.
充值：type=1,pay_status =2;

*/


0:全部, 1:充值,2:提现,3:余额投资,4:银行卡投资,5:回款回款 6:奖励


充值,提现,余额投资,银行卡投资,回款回款,奖励


CASE (Transact-SQL)
https://docs.microsoft.com/en-us/sql/t-sql/language-elements/case-transact-sql


奖励：
SUserWalletRecords
pay_type = 3;
pay_status = 2;


SUserWalletRecords



VEtfMjAxNzA0MjQxMjE1MTFfOF8yMDIyNjdfREk5VWZsT1Z6MmRDSmR3T2loMUdhYjZIK0V5T2Yr
Z3M1UFM5aUFPaDhSUT0K

VEtfMjAxNzA0MjQxMjE1MTFfOF8yMDIyNjdfREk5VWZsT1Z6MmRDSmR3T2loMUdhYjZIK0V5T2Yr Z3M1UFM5aUFPaDhSUT0K






8c47df5, 9460816



-- //类型名称：1:充值,2:提现,3:余额投资,4:银行卡投资,5:回款 6:奖励
select add_time as time, value as amount,
            CASE
				WHEN type=1 and recharge_no like 'JL%' THEN '6' 
                WHEN type=1 and pay_type=0 THEN '5'   
                WHEN type=1 and pay_status=2 THEN '1' 
                
                WHEN type=2 and recharge_no like 'TX%' THEN '2' 
                ELSE '-1'
            END recordType,
            CASE
                WHEN type=1 THEN '交易成功'
                WHEN recharge_no like 'TX%' AND status=1  THEN '已到账'
                WHEN recharge_no like 'TX%' AND status!=1 THEN '等待处理'
                ELSE '交易成功'
            END statusName
        from s_user_wallet_records
        where 1 = 1
            and user_id = 8
        and not (type=2 and recharge_no like 'QB%')

        UNION

        select add_time as time, due_capital as amount,
            CASE
                WHEN from_wallet=1 THEN '3'   
                ELSE '4'                       
            END recordType, '交易成功' as statusName
        from s_user_due_detail
        where 1 = 1
        
            and user_id = 8
       

        order by time desc
       

       
转入：
充值，回款，奖励


转出：
提现，投资，




walletDetail


sInvestmentDetail
									.setDayIncrement(tRate.subtract(yRate).divide(yRate, 10, BigDecimal.ROUND_HALF_UP)
											.multiply(BigDecimal.valueOf(100)).setScale(2, BigDecimal.ROUND_HALF_UP));


提现：等待处理和已到账两种状态
其他都是交易成功。


CASE
                WHEN type=1 and recharge_no like 'JL%' THEN '6' <!--奖励-->
                WHEN type=1 and pay_type=0 THEN '5'   <!--回款-->
                WHEN type=1 and pay_status=2 THEN '1' <!--充值-->
                WHEN type=2 and recharge_no like 'TX%' THEN '2' <!--提现-->
                ELSE '-1'
            END recordType,

R252:资金记录和余额记录页面优化



g/mapper/SUserWalletRecordsExtendMapper.xml
### The error may involve com.yiding.mapper.SUserWalletRecordsExtendMapper.selectByMap-Inline
### The error occurred while setting parameters
### SQL: select                 id, user_id, recharge_no, trade_no, pay_type, value, type, pay_status, pay_code,     enable_interest,      status, user_bank_id, device_type,add_time, modify_time,               from s_user_wallet_records     where 1 = 1                  and recharge_no = ?                                               order by add_time desc
### Cause: com.mysql.jdbc.exceptions.jdbc4.MySQLSyntaxErrorException: You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'from s_user_wallet_records
    where 1 = 1



# The error may exist in com/yiding/mapper/SUserWalletRecordsExtendMapper.xml
### The error may involve com.yiding.mapper.SUserWalletRecordsExtendMapper.selectByMap-Inline
### The error occurred while setting parameters
### SQL: select                 id, user_id, recharge_no, trade_no, pay_type, value, type, pay_status, pay_code,     enable_interest,      status, user_bank_id, device_type,add_time, modify_time,               from s_user_wallet_records     where 1 = 1                  and recharge_no = ?                                               order by add_time desc
### Cause: com.mysql.jdbc.exceptions.jdbc4.MySQLSyntaxErrorException: You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'from s_user_wallet_records
    where 1 = 1
     
     
    	and recharge_no = 'R' at line 9
; bad SQL grammar []; nested exception is com.mysql.jdbc.exceptions.jdbc4.MySQLSyntaxErrorException: You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'from s_user_wallet_records
    where 1 = 1
     
     
    	and recharge_no = 'R' at line 9


    <if test="notProjectNewPreferentials != null">
      and sudd.project_id in (
        select DISTINCT id from s_project where new_preferential not in
        <foreach item="item" index="index" collection="notProjectNewPreferentials"
                 open="(" separator="," close=")">#{item}
        </foreach
        >
      )



资金记录和余额记录页面优化



## CASE..when..end

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

## foreach

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















select 
		sid.user_id,
		su.username,
		su.real_name,
		su.card_no,
		sid.inv_succ,
		sid.add_time,
		CASE 
			WHEN sid.inv_succ >= 5000 THEN 50
			WHEN sid.inv_succ >= 1000 THEN 30
		END as type
from s_investment_detail sid
inner JOIN s_user su on su.id = sid.user_id 
where sid.id in (
	select min(sid.id)
	from s_investment_detail sid 
	INNER JOIN s_user su on su.id = sid.user_id 
	-- 渠道号
		and su.channel = '' and su.real_name_auth = 1 
	-- 日期筛选
		and su.add_time BETWEEN '2017-05-23 00:00:00' and '2017-05-28 00:00:00'
	INNER JOIN s_project sp on sp.id = sid.project_id 
		and sp.duration >= 30
	where sid.user_id > 0
	GROUP BY sid.user_id
)




select 
		sid.user_id,
		su.username,
		su.real_name,
		su.card_no,
		sid.inv_succ,
		sid.add_time,
		CASE 
			WHEN sid.inv_succ >= 5000 THEN 50
			WHEN sid.inv_succ >= 1000 THEN 30
		END as type
from s_investment_detail sid
inner JOIN s_user su on su.id = sid.user_id 
INNER JOIN s_project sp on sp.id = sid.project_id 
	and sp.duration >= 30
where sid.id in (
	select min(sid.id)
	from s_investment_detail sid 
	INNER JOIN s_user su on su.id = sid.user_id 
	-- 渠道号
		and su.channel = '' and su.real_name_auth = 1 
	-- 日期筛选
		and su.add_time BETWEEN '2017-05-23 00:00:00' and '2017-05-28 00:00:00'
	where sid.user_id > 0
	GROUP BY sid.user_id
)



select 
		sid.user_id,
		su.username,
		su.real_name,
		su.card_no,
		sid.inv_succ,
		sid.add_time,
		CASE 
			WHEN sid.inv_succ >= 5000 THEN 50
			WHEN sid.inv_succ >= 1000 THEN 30
		END as type
from s_investment_detail sid
inner JOIN s_user su on su.id = sid.user_id 
INNER JOIN s_project sp on sp.id = sid.project_id 
	and sp.duration >= 30
where sid.id in (
	select min(sid.id)
	from s_investment_detail sid 
	INNER JOIN s_user su on su.id = sid.user_id 
	-- 渠道号
		and su.channel = 'quanmama' and su.real_name_auth = 1 
	-- 日期筛选
		and su.add_time BETWEEN '2017-05-23 00:00:00' and '2017-05-28 00:00:00'
	where sid.user_id > 0
	GROUP BY sid.user_id
)




from sqlalchemy import text

sql = text('select name from penguins')
result = db.engine.execute(sql)
names = []
for row in result:
    names.append(row[0])

print names

or

from sqlalchemy import create_engine

eng = create_engine('sqlite:///:memory:')
with eng.connect() as con:
    rs = con.execute('SELECT 5')
    data = rs.fetchone()[0]
    print "Data: %s" % data  


>>> from sqlalchemy.orm import sessionmaker
>>> Session = sessionmaker(bind=engine)


from sqlalchemy import *
from sqlclchemy.orm import *
# 2. 建立数据库引擎
mysql_engine = create_engine("$address", echo, module)
    #address 数据库://用户名:密码（没有密码则为空）@主机名：端口/数据库名
    #echo标识用于设置通过python标准日志模块完成的SQLAlchemy日志系统，当开启日志功能，我们将能看到所有的SQL生成代码
# 3. 建立连接
connection = mysql_engine.connect()
# 4. 查询表信息
result = connection.execute("select name from t_name)
for row in result:
    print "name: ", row['name']
# 5. 关闭连接
connection.close()


1. 今天投资用户
1. 新增用户
1. 今天首投用户




rm-uf670z2v1k0zu1u6ho.mysql.rds.aliyuncs.com



yiding_read

Yiding2017Read!

Yiding2017Read!



rds_release

15251462230

STHeiti Light.ttc



simhei




zCard


zRevRangeByScore


RedisZSetCommands


zIncrBy


incrByValue


zScore,zRevrank,RedisZSetCommands

zRevRangeWithScores


zScore



various ['vεəriəs] 

adj. 各种各样的；多方面的




variety 
 英  [və'raɪətɪ]   美  [və'raɪəti]
n. 多样；种类；杂耍；变化，多样化



select count(*) from s_user where s_user.username in (select mobile from s_user_history) and s_user.real_name_auth = 1;




Logstash 最佳实践

1. [访谈与书评：《LogStash，使日志管理更简单》](http://www.infoq.com/cn/articles/review-the-logstash-book/)




## Reference
1. (Logstash 最佳实践)[http://udn.yyuap.com/doc/logstash-best-practice-cn/index.html]
1. [访谈与书评：《LogStash，使日志管理更简单》](http://www.infoq.com/cn/articles/review-the-logstash-book/)





rpm包和deb包是两种Linux系统下最常见的安装包格式.rpm包主要应用在RedHat系列包括Fedora,centos等发行版的Linux系统上，deb包主要应用于Debian系列包括现在比较流行的Ubuntu等发行版上。 
安装rpm包的命令是“rpm -参数”，安装deb包的命令是“dpkg -参数”。

yum可以用于运作rpm包，例如在Fedora系统上对某个软件的管理：

- 安装：yum install <package_name> 
- 卸载：yum remove <package_name> 
- 更新：yum update <package_name> 

apt-get可以用于运作deb包，例如在Ubuntu系统上对某个软件的管理：

- 安装：apt-get install <package_name> 
- 卸载：apt-get remove <package_name> 
- 更新：apt-get update <package_name>


职位描述:
1.开发互联网理财APP的后端建设；
2.分析梳理业务场景，通过架构将之落地；

要求:
1. JAVA基础扎实，理解io、多线程、集合等基础框架，对JVM原理有一定的了解； 
2. 3年及以上使用JAVA开发的经验，对于你用过的开源框架，能了解到它的原理和机制；对Spring,ibatis,struts等开源框架熟悉； 
3. 熟悉分布式系统的设计和应用，熟悉分布式、缓存、消息等机制；能对分布式常用技术进行合理应用，解决问题； 
4. 掌握多线程及高性能的设计与编码及性能调优；有高并发应用开发经验优先； 
5. 掌握Linux 操作系统、大型数据库（MySql）； 
6. 喜欢去看及尝试最新的技术，追求编写优雅的代码，从技术趋势和思路上能影响技术团队；
7. 有互联网金融业务背景者优先 ；



岗位需求：
职位描述:
1.开发互联网理财APP的后端建设；
2.具备基础的需求分析和设计能力；
3.负责项目软件的功能实现并进行单元测试、模块测试；

要求:
1. JAVA基础扎实，理解io、多线程、集合等基础框架； 
2. 2年及以上使用JAVA开发的经验，熟悉Spring/SpringMVC/CXF/Mybatis等开源框架；
3. 熟练使用Mysql，了解如何优化sql；
4. 了解缓存机制；能熟练的使用Redis的特性； 
5. 责任心强具备良好的团队合作精神和承受压力的能力； 
6. 有互联网金融业务背景者优先 ；



userInvestedEvent



ScheduledExecutorService service = Executors.newScheduledThreadPool(10);



1. reportlab layout:
【Reportlab pdf layout guide - Stack Overflow】https://stackoverflow.com/questions/26351922/reportlab-pdf-layout-guide
1. reportlab layout:
【Python reportlab.platypus.Paragraph Examples】https://mr.baidu.com/imb9qsu


1. [SimpleDocTemplate Examples](http://www.programcreek.com/python/example/51068/reportlab.platypus.SimpleDocTemplate)




1. [Product Catalogue Tutorial](https://www.reportlab.com/documentation/tutorial/product-catalogue/)
1. [Fund Reporting Tutorial](https://www.reportlab.com/documentation/tutorial/fund-reports/)



# Reference

1. [A Simple Step-by-Step Reportlab Tutorial](https://www.blog.pythonlibrary.org/2010/03/08/a-simple-step-by-step-reportlab-tutorial/)
1. [Product Catalogue Tutorial](https://www.reportlab.com/documentation/tutorial/product-catalogue/)
1. [Fund Reporting Tutorial](https://www.reportlab.com/documentation/tutorial/fund-reports/)




#mysql jdbc driver information
jdbc.driverClassName=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://rm-bp185s5th4i6ntlel.mysql.rds.aliyuncs.com:3306/yiding_test?useUnicode=true&autoReconnect=true&characterEncoding=UTF-8
jdbc.username=yiding
jdbc.password=Yiding2017



add_time


 params.put("status", 2); add_time<xx


 private RedisCacheService redisCacheService;


public boolean setIfAbsent(String key, String value,long expireTime) {
        boolean result=keyValueStore.setIfAbsent(key,value);
        if(result)
            latestMessageExpiration.expire(key,expireTime,TimeUnit.SECONDS);
        return result;
    }



add_time


针对第二点：xx标的在售时长为xx小时，已达48小时及以上。特此预警，请相关人员做好应对。
params.put("status", 2); add_time<xx

xx标的剩余金额为xxxx元，已不足5000元，并持续一小时没有卖完。特此预警，请相关人员做好应对。
params.put("status", 2); able<5000 and modify_time<xxx



BaseService



getWalletLeagueInvest


SUserWalletRecordsMapper

SUserWalletRecordsMapper



DATE(DATE_ADD(add_time, INTERVAL +2 day)) &lt; now()
DATE_SUB(OrderDate,INTERVAL 2 DAY)


DATE_ADD(OrderDate,INTERVAL 2 DAY) &lt; now();



/projects/sellout/check



select id, title, start_time, end_time, modify_time from s_project where is_delete = 0  and end_time > now() and status=2 and DATE_ADD(start_time,INTERVAL 2 DAY) <= now();

select id, title, start_time, end_time, modify_time from s_project where is_delete = 0 and status=2 and end_time > now();


        if (!redisService.setIfNotExists(getDayLoginRecordKey(nUserID,new Date()),"1",24*60*60*2)){
            return;
        }



        o        if (!redisService.setIfNotExists("ranking20170605:lock:finalAwards", "1", 0)) return false;




public boolean isRealActivityTime(Date date){
        String testNowTime = redisService.get(nowDateKey);
        Date now = StringUtils.isEmpty(testNowTime) ? date : DateUtil.parseDateTime(testNowTime);

        if (now.getTime() >= DateUtil.parseDate(startDate).getTime() &&
                now.getTime() < DateUtil.parseDate(endDate).getTime()){
            return true;
        }

        return false;
    }


 public Map<String, Object> getProjects(String type){
        BigDecimal userPlatformSubsidy = BigDecimal.ZERO;
        switch (type){
            case "first" :
                userPlatformSubsidy = new BigDecimal("5.18");
                break;
            case "second" :
                userPlatformSubsidy = new BigDecimal("3.88");
                break;
            case "third" :
                userPlatformSubsidy = new BigDecimal("2.88");
 public boolean updateBeanChance(String userId, String chanceType, String type){
        if (!isAllActivityTime(new Date(), true)) return false;

        if (StringUtils.isEmpty(userId) ||
                !(UpdateTypeAdd.equals(type) || UpdateTypeSub.equals(type))) return false;
        if  (UpdateTypeAdd.equals(type) &&
                !ChanceTypeUserRegister.equals(chanceType) &&
                !ChanceTypeInvested6MonthProject.equals(chanceType) &&
                !ChanceTypeShared.equals(chanceType)) return false;

        String cacheValue = redisService.get(currentKey + userId);
        InternetFinancialFestival518Bean internetFinancialFestival518Bean = null;
        if (StringUtils.isEmpty(cacheValue)) {
            internetFinancialFestival518Bean = getDefaultPanicBuying528Bean();
        } else {
            internetFinancialFestival518Bean = JSONObject.parseObject(cacheValue, InternetFinancialFestival518Bean.class);
        }



返回1是星期日、2是星期一、3是星期二、4是星期三、5是星期四、6是星期五、7是星期六 


爆款


1:新人特惠,2:爆款,3:HOT,4:奖励,5:预售,6:活动


select id, title,amount, start_time, end_time, modify_time, duration from s_project where title = '首尾%' and is_delete = 0 and duration>20 and duration<40 
		and start_time>='2017-06-08' and status in (1,2,3);


  String sql = "select DISTINCT user_id from s_user_interest_coupon where source = 'ranking20170605' and interest_rate = 1  and `status` = 0 " +
                "and DATE_FORMAT(expire_time, '%Y-%m-%d') = DATE_ADD(CURDATE(),INTERVAL 1 DAY)";
        List<Long> userIds = sqlMapper.selectList(sql, Long.class);

e setting parameters
### SQL: select id, title,status, start_time as startTime, is_countdown as isCountdown from s_project where title like '首尾%' and is_delete = 0 and start_time>= '2017-06-09' and duration BETWEEN(25,35) and status in (1,2,3);
### Cause: java.sql.SQLException: Operand should contain 1 column(s)
; bad SQL grammar []; nested exception is java.sql.SQLException: Operand should contain 1 column(s)


----
2017-06-09 17:49:55,442  INFO http-nio-8080-exec-4 (org.apache.cxf.jaxrs.utils.FormUtils:173) - token=VEtfMjAxNzA1MTUyMDA3MjRfMTM0OF8wNjc4MTdfREk5VWZsT1Z6MmRuaFM2WHdlNzJOSFI1aFhW%0D%0AT1BJNENPa2drWncxSTlHRT0K&label=weekend&type=info
2017-06-09 17:49:55,810 ERROR http-nio-8080-exec-4 (com.yiding.rest.service.activity.impl.RestActivityServiceImpl:189) - get userId : 1348 from token :VEtfMjAxNzA1MTUyMDA3MjRfMTM0OF8wNjc4MTdfREk5VWZsT1Z6MmRuaFM2WHdlNzJOSFI1aFhW
T1BJNENPa2drWncxSTlHRT0K
2017-06-09 17:49:58,898  INFO http-nio-8080-exec-4 (com.yiding.activity.Weekend.WeekendHelper:71) - getAmount, key:weekend20170624:amount:2017-06-24:1348

select id, title,status, start_time as startTime, is_countdown as isCountdown from s_project where title like '首尾%' and is_delete = 0 and start_time>= '2017-06-09' and duration BETWEEN 25 and 35 and status in (1,2,3);
select id, title,status, start_time as startTime, is_countdown as isCountdown from s_project where title like '首尾%' and is_delete = 0 and start_time>= '2017-06-09' and duration BETWEEN 55 and 65 and status in (1,2,3);
select id, title,status, start_time as startTime, is_countdown as isCountdown from s_project where title like '首尾%' and is_delete = 0 and start_time>= '2017-06-09' and duration BETWEEN 85 and 95 and status in (1,2,3);



select id, title,status, start_time as startTime, is_countdown as isCountdown from s_project where title like '周末专场%' and is_delete = 0 and start_time>= '2017-06-10' and duration BETWEEN 25 and 35 and status in (1,2,3);
select id, title,status, start_time as startTime, is_countdown as isCountdown from s_project where title like '周末专场%' and is_delete = 0 and start_time>= '2017-06-10' and duration BETWEEN 55 and 65 and status in (1,2,3);
select id, title,status, start_time as startTime, is_countdown as isCountdown from s_project where title like '周末专场%' and is_delete = 0 and start_time>= '2017-06-10' and duration BETWEEN 85 and 95 and status in (1,2,3);

-----

select id, title,s_project.status, start_time as startTime, is_countdown as isCountdown, able,duration from s_project 
where  start_time>= '2017-06-08' and status in (1,2,3) ;

select id, title,status, start_time as startTime, is_countdown as isCountdown,able , duration from s_project where title like '周末专场%' and is_delete = 0 and start_time>= '2017-06-09'  and status in (1,2,3);
/* and duration BETWEEN(25,35) and status in (1,2,3);*/


select id, title,status, start_time as startTime,end_time, is_countdown as isCountdown from s_project where title like '周末专场%' and is_delete = 0 and start_time>= '2017-06-10' and duration BETWEEN 25 and 35 and status in (1,2,3);
select id, title,status, start_time as startTime,end_time, is_countdown as isCountdown from s_project where title like '周末专场%' and is_delete = 0 and start_time>= '2017-06-10' and duration BETWEEN 55 and 65 and status in (1,2,3);
select id, title,status, start_time as startTime,end_time, is_countdown as isCountdown from s_project where title like '周末专场%' and is_delete = 0 and start_time>= '2017-06-10' and duration BETWEEN 85 and 95 and status in (1,2,3);
select id, title,status, start_time as startTime,end_time, is_countdown as isCountdown, able  from s_project where title like '周末专场%' and is_delete = 0 and start_time>= '2017-06-09';



weekend20170624:now:time


139.196.41.47

yiding2017


redis-cli -h 139.196.41.47 -p 6379




2017-06-12 13:41:55,258  INFO http-nio-8080-exec-8 (com.yiding.activity.Weekend.WeekendHelper:83) - getAmount, key:weekend20170624:amount:2017-06-08:1348
2017-06-12 13:41:59,422  INFO http-nio-8080-exec-8 (com.yiding.activity.Weekend.WeekendHelper:214) - queryWeekendProject sql:select id, title,status, start_time as startTime, is_countdown as isCountdown from s_project where title like '周末专场%' and is_delete = 0 and start_time>= '2017-06-17' and duration BETWEEN 25 and 35 and status in (1,2,3);
2017-06-12 13:41:59,681  INFO http-nio-8080-exec-8 (com.yiding.activity.Weekend.WeekendHelper:214) - queryWeekendProject sql:select id, title,status, start_time as startTime, is_countdown as isCountdown from s_project where title like '周末专场%' and is_delete = 0 and start_time>= '2017-06-17' and duration BETWEEN 55 and 65 and status in (1,2,3);
2017-06-12 13:41:59,770  INFO http-nio-8080-exec-8 (com.yiding.activity.Weekend.WeekendHelper:214) - queryWeekendProject sql:select id, title,status, start_time as startTime, is_countdown as isCountdown from s_project where title like '周末专场%' and is_delete = 0 and start_time>= '2017-06-17' and duration BETWEEN 85 and 95 and status in (1,2,3);
2017-06-12 13:42:00,309 ERROR http-nio-8080-exec-8 (com.yiding.interceptor.MD5OutMessageInterceptor:37) - out : {"code":"0","result":{"amount":0,"3month":"未开启","2month":"未开启","1month":"未开启"},"errorType":null,"errorMsg":"正常","success":true}
2017-06-12 13:42:00,515  INFO http-nio-8080-exec-8 (com.yiding.interceptor.AuthFilter:138) - execute[/yiding-rest/rest/activity/weekend/info.json]spend time : 6713
2017-06-12 13:52:48,685  INFO http-nio-8080-exec-10 (com.yiding.interceptor.AuthFilter:85) - request url : /yiding-rest/rest/activity/weekend/info.json, params : {label=weekend, type=info, token=VEtfMjAxNzA1MTUyMDA3MjRfMTM0OF8wNjc4MTdfREk5VWZsT1Z6MmRuaFM2WHdlNzJOSFI1aFhW
T1BJNENPa2drWncxSTlHRT0K}
2017-06-12 13:52:48,719  INFO http-nio-8080-exec-10 (org.apache.cxf.interceptor.LoggingInInterceptor:250) - Inbound Message


weekend20170624:amount:2017-06-08:8

set weekend20170624:now:time "2017-06-20 22:01:01"
get weekend20170624:now:time

get weekend20170624:amount:2017-06-08:8
get weekend20170624:amount:2017-06-08:8



岗位职责：
1.负责公司项目的软件测试工作，跟踪协调问题的解决；
2.根据需求及设计等制定测试计划，设计、执行并维护测试用例，执行测试；
3.推动问题及时合理地解决，确保质量；
4.提交测试报告，完整地记录测试结果，编写完整的测试报告等相关的技术文档； 

任职要求
1到2年互联网软件测试工作经验
1. 熟悉测试用例设计，
2. 会使用postman或其他工具进行接口测试
3. 熟练掌握数据库知识(MYSQL),准确地定位并跟踪问题，推动问题及时合理地解决;
4. 有较强的分析设计能力和方案整合能力；
5. 良好的沟通技能，团队合作能力、以及辅导能力.
6. 有使用BUG管理工具经验，如 JRIA、BUGFree，Bugzilla、redmine等

优先考虑条件
1.熟悉压力测试脚本设计（Loadrunner）、掌握系统性能测试方法；
2.具有自动化测试开发经验






paySuccess
walletPaySuccess


## 保存添加定时Job

    /**
        * 保存添加定时任务
        * @param info
    */
    public void add(TaskInfo info) {
        ...
        try {
            TriggerKey triggerKey = TriggerKey.triggerKey(jobName, jobGroup);
            boolean bExits = scheduler.checkExists(triggerKey);             //check if trigger exist
            if (bExits) {
                logger.info("===> AddJob fail, job already exist, jobGroup:{}, jobName:{}", jobGroup, jobName);
                throw new RuntimeException(String.format("Job已经存在, jobName:{%s},jobGroup:{%s}", jobName, jobGroup));
            }
            TriggerKey triggerKey = TriggerKey.triggerKey(jobName, jobGroup);
            JobKey jobKey = JobKey.jobKey(jobName, jobGroup);

            //create Scheduler Builder
            CronScheduleBuilder cronScheduleBuilder = CronScheduleBuilder.cronSchedule(cronExpression).withMisfireHandlingInstructionDoNothing();       
            CronTrigger cronTrigger = TriggerBuilder.newTrigger().withIdentity(triggerKey).withDescription(createTime).withSchedule(cronScheduleBuilder).build(

            Class<? extends Job> clazz = (Class<? extends Job>)Class.forName(jobClassName);
            JobBuilder jobBuilder = JobBuilder.newJob(clazz).withIdentity(jobKey).withDescription(jobDescription);
            if (!StringUtils.isEmpty(ipAddress)) jobBuilder.usingJobData("ipAddress", ipAddress);
            if (!StringUtils.isEmpty(method)) jobBuilder.usingJobData("method", method);
            if (!StringUtils.isEmpty(param)) jobBuilder.usingJobData("param", param);

            JobDetail jobDetail = jobBuilder.build();
            scheduler.scheduleJob(jobDetail, cronTrigger);
        } catch (SchedulerException | ClassNotFoundException e) {
            ...
        }
    }

1. 通过JobBuilder创建JobDetail.
1. 通过TriggerBuilder创建Trigger, 使用withSchedule关联chedulerBuilder.


## 删除定时Job

    /**
        * 删除定时任务
    */
    public void delete(TaskInfo taskInfo){
        TriggerKey triggerKey = TriggerKey.triggerKey(taskInfo.getJobName(), taskInfo.getJobGroup());
        try {
            //check if trigger exist
            if (checkExists(taskInfo.getJobName(), taskInfo.getJobGroup())) {
                scheduler.pauseTrigger(triggerKey);
                //移除trigger, unscheduleJobs(List<TriggerKey> triggerKeys) 可以移除指定的trigger列表
                scheduler.unscheduleJob(triggerKey);      
                logger.info("===> delete, triggerKey:{}", triggerKey);
            }
        } catch (SchedulerException e) {
            throw new RuntimeException(e.getMessage());
        }
    }

1. 通过scheduler.pauseTrigger(triggerKey)暂停Trigger.
1. scheduler.unscheduleJob(triggerKey)移除Trigger关联的Job.


## 暂停定时Job

    /**
        * 暂停定时任务
    */
    public void pause(TaskInfo taskInfo){
        TriggerKey triggerKey = TriggerKey.triggerKey(taskInfo.getJobName(), taskInfo.getJobGroup());
        try {
            if (checkExists(taskInfo.getJobName(), taskInfo.getJobGroup())) {
                scheduler.pauseTrigger(triggerKey);
            }
        } catch (SchedulerException e) {
            throw new RuntimeException(e.getMessage());
        }
    }

1. 通过scheduler.pauseTrigger(triggerKey)暂停trigger.


## 重新开始Job

    /**
     * 重新开始任务
     * @param taskInfo
     */
    @QuartzRecordType(type = QuartzRecordType.job, action = "resume")
    public void resume(TaskInfo taskInfo){
        TriggerKey triggerKey = TriggerKey.triggerKey(taskInfo.getJobName(), taskInfo.getJobGroup());

        try {
            if (checkExists(taskInfo.getJobName(), taskInfo.getJobGroup())) {
                scheduler.resumeTrigger(triggerKey);
                logger.info("===> Resume success, triggerKey:{}", triggerKey);
            }
        } catch (SchedulerException e) {
            e.printStackTrace();
        }
    }

1. 通过scheduler.resumeTrigger(triggerKey)重新开始.


## 修改定时Job

    /**
        * 修改定时任务
    */
        @QuartzRecordType(type = QuartzRecordType.job, action = "edit")
        public void edit(TaskInfo info) {
            ...
            try {
                if (!checkExists(jobName, jobGroup)) {
                    throw new RuntimeException(String.format("Job不存在, jobName:{%s},jobGroup:{%s}", jobName, jobGroup));
                }
                TriggerKey triggerKey = TriggerKey.triggerKey(jobName, jobGroup);
                JobKey jobKey = new JobKey(jobName, jobGroup);

                CronScheduleBuilder cronScheduleBuilder = CronScheduleBuilder.cronSchedule(cronExpression).withMisfireHandlingInstructionDoNothing();
                CronTrigger cronTrigger = TriggerBuilder.newTrigger().withIdentity(triggerKey).withDescription(createTime).withSchedule(cronScheduleBuilder).build();

                JobDetail jobDetail = scheduler.getJobDetail(jobKey);

                if (!jobDetail.getJobClass().getName().equals(jobClassName)){
                    throw new RuntimeException(String.format("Job不存在, jobName:{%s},jobGroup:{%s}", jobName, jobGroup));
                }

                JobBuilder jobBuilder = jobDetail.getJobBuilder();
                if (!StringUtils.isEmpty(jobDescription)) jobBuilder.withDescription(jobDescription);
                if (!StringUtils.isEmpty(ipAddress)) jobBuilder.usingJobData("ipAddress", ipAddress);
                if (!StringUtils.isEmpty(method)) jobBuilder.usingJobData("method", method);
                if (!StringUtils.isEmpty(param)) jobBuilder.usingJobData("param", param);
                jobDetail = jobBuilder.build();
                HashSet<Trigger> triggerSet = new HashSet<>();
                triggerSet.add(cronTrigger);

                scheduler.scheduleJob(jobDetail, triggerSet, true);
            } catch (SchedulerException e) {
                throw new RuntimeException("类名不存在或执行表达式错误");
            }
        }

**先创建Trigger,获取原来的JobDetail, 更新JobMapData, 然后使用scheduleJob(JobDetail jobDetail, Set<? extends Trigger> triggersForJob, boolean replace) ,来替换原有job**



## 获取指定Job

    /**
     * 指定任务
     */
    public TaskInfo detail(String jobName, String jobGroup){
        try {
            if (!checkExists(jobName, jobGroup)) {
                throw new RuntimeException(String.format("Job不存在, jobName:{%s},jobGroup:{%s}", jobName, jobGroup));
            }

            TriggerKey triggerKey = TriggerKey.triggerKey(jobName, jobGroup);
            JobKey jobKey = new JobKey(jobName, jobGroup);

            List<? extends Trigger> triggers = scheduler.getTriggersOfJob(jobKey);  //通过JobKey找trigger.
            if (triggers.isEmpty()) return null;

            JobDetail jobDetail = scheduler.getJobDetail(jobKey);
            Trigger.TriggerState triggerState = scheduler.getTriggerState(triggerKey);

            TaskInfo info = new TaskInfo();
            info.setJobName(jobKey.getName());
            info.setJobGroup(jobKey.getGroup());
            info.setJobClassName(jobDetail.getJobClass().getName());
            info.setJobDescription(jobDetail.getDescription());
            info.setJobStatus(triggerState.name());
            if (triggers.get(0) instanceof CronTrigger){
                CronTrigger cronTrigger = (CronTrigger) triggers.get(0);
                info.setCronExpression(cronTrigger.getCronExpression());
                info.setCreateTime(cronTrigger.getDescription());
            }
            JobDataMap jobDataMap = jobDetail.getJobDataMap();
            info.setIpAddress(jobDataMap.getString("ipAddress"));
            info.setMethod(jobDataMap.getString("method"));
            info.setParam(jobDataMap.getString("param"));
            return info;
        } catch (SchedulerException e) {
            e.printStackTrace();
        }

        return null;
    }

**通过JobKey获取对应的Trigger列表：public List<? extends Trigger> getTriggersOfJob(JobKey jobKey) Get all Trigger s that are associated with the identified JobDetail.**



## 验证Job是否存在

    /**
     * 验证是否存在
     * @param jobName
     * @param jobGroup
     * @throws SchedulerException
     */
    private boolean checkExists(String jobName, String jobGroup) throws SchedulerException{
        if (StringUtils.isEmpty(jobName) || StringUtils.isEmpty(jobGroup)){
            return false;
        }

        TriggerKey triggerKey = TriggerKey.triggerKey(jobName, jobGroup);
        return scheduler.checkExists(triggerKey);
    }

**使用Scheduler.checkExists(TriggerKey var1)判断Trigger是否存在,如果是JobKey,则是判断Job是否存在**




## 获取所有任务列表

    /**
     * 所有任务列表
     */
    public List<TaskInfo> list(){
        List<TaskInfo> list = new ArrayList<>();

        try {
            for(String groupJob: scheduler.getJobGroupNames()){
                for(JobKey jobKey: scheduler.getJobKeys(GroupMatcher.<JobKey>groupEquals(groupJob))){
                    List<? extends Trigger> triggers = scheduler.getTriggersOfJob(jobKey);
                    for (Trigger trigger: triggers) {
                        Trigger.TriggerState triggerState = scheduler.getTriggerState(trigger.getKey());
                        JobDetail jobDetail = scheduler.getJobDetail(jobKey);

                        String cronExpression = "", createTime = "";

                        if (trigger instanceof CronTrigger) {
                            CronTrigger cronTrigger = (CronTrigger) trigger;
                            cronExpression = cronTrigger.getCronExpression();
                            createTime = cronTrigger.getDescription();
                        }
                        TaskInfo info = new TaskInfo();
                        info.setJobName(jobKey.getName());
                        info.setJobGroup(jobKey.getGroup());
                        info.setJobClassName(jobDetail.getJobClass().getName());
                        info.setJobDescription(jobDetail.getDescription());
                        info.setJobStatus(triggerState.name());
                        info.setCronExpression(cronExpression);
                        info.setCreateTime(createTime);
                        JobDataMap jobDataMap = jobDetail.getJobDataMap();
                        info.setIpAddress(jobDataMap.getString("ipAddress"));
                        info.setMethod(jobDataMap.getString("method"));
                        info.setParam(jobDataMap.getString("param"));
                        info.setJobDateMap(jobDataMap);
                        list.add(info);
                    }
                }
            }
        } catch (SchedulerException e) {
            e.printStackTrace();
        }

        return list;
    }

1. Trigger.TriggerState包括normal, none,pause,error等。
1. scheduler.getJobGroupNames()获取该Scheduler所有JobDetail的名称。
1. cheduler.getJobKeys(match)获取所有符合条件的JobKey的列表。
1. scheduler.getTriggersOfJob(xx)根据JobKey获取关联的所有的Trigger的list.
1. scheduler.getTriggerState(trigger.getKey())根据Trigger获取对应的状态。



## 总结

1. 添加
    - 通过JobBuilder创建JobDetail.
    - 通过TriggerBuilder创建Trigger, 使用withSchedule关联chedulerBuilder.
1. 删除
    - 通过scheduler.pauseTrigger(triggerKey)暂停Trigger.
    - scheduler.unscheduleJob(triggerKey)移除Trigger关联的Job.
1. 修改
    - 先创建Trigger,获取原来的JobDetail, 更新JobMapData, 然后使用scheduleJob(JobDetail jobDetail, Set<? extends Trigger> triggersForJob, boolean replace) ,来替换原有job
1. 暂停
    - 通过scheduler.pauseTrigger(triggerKey)暂停trigger.
1. 恢复
    - 通过scheduler.resumeTrigger(triggerKey)重新开始.
1. 检查trigger/job是否存在
    - 通过scheduler.checkExists(key)检查trigger或者job是否存在.



孙巍

获取配置

配置如下：
    #周三夺宝时间配置(活动开始)
    day-of-week-beign=3
    hour-of-day-begin=0
    minute-begin=0
    second-begin=0


    //加载周三夺宝活动时间
    redisService.set(DAY_OF_WEEK_BEGIN, ResourceBundle.getBundle("env").getString(DAY_OF_WEEK_BEGIN));
    redisService.set(HOUR_OF_DAY_BEGIN, ResourceBundle.getBundle("env").getString(HOUR_OF_DAY_BEGIN));
    redisService.set(MINUTE_BEGIN, ResourceBundle.getBundle("env").getString(MINUTE_BEGIN));
    redisService.set(SECOND_BEGIN, ResourceBundle.getBundle("env").getString(SECOND_BEGIN));



    select id, title,status, start_time as startTime, is_countdown as isCountdown from s_project where title like '周末专场%' and is_delete = 0 and start_time>= '2017-06-24' and duration BETWEEN 55 and 65 and status in (1,2,3);











sUserRewardLogService.create(
                    SUserRewardLog.Builder.aSUserRewardLog()
                            .userId(sUser.getId().intValue())
                            .content("一马当先1.8%加息券")
                            .point(0)
                            .type(1)
                            .typeSupply(new BigDecimal("1.8"))
                            .label(LABEL)
                            .couponId(couponId)
                            .rechargeNoOrigin(sRechargeLog.getRechargeNo())
                            .build()
            );


    @Autowired
    private SUserRewardLogService sUserRewardLogService;
    status



        private static final String LABEL = "beginEndProjects";


@LabelHandler("zhouSanDuoBao")

@TypeMethodHandler("getCurAwardCode")




activity/lottery


201707daydayAward


listAllUnGetCoupon


if (weekendHelper.isRealActivityTime(new Date()) && WeekendHelper.isWeekendProject(sProjectWithBLOBs)) {
			long weekendProjectAmount = weekendHelper.getAmount(userId);
			Double dAmount = Double.valueOf(amount);
			if (dAmount > weekendProjectAmount) {
				return "当前购买额度不足，请查看活动详情";
			} else {
				weekendHelper.decrAmount(userId, dAmount);
			}
		}

2196


(ArrayList<Integer> acc, ArrayList<Integer> item) {
                        System.out.println("BinaryOperator");
                        acc.addAll(item);


jsonResult= JsonResult.create(ResultCode.ERROR_LOGIN_AUTH);
                jsonResult.setErrorMsg("请求路径错误!");


 jsonResult = JsonResult.create(ResultCode.GENERAL);
                    jsonResult.setCode("1111");
                    jsonResult.setErrorMsg("token 已失效");

JsonResult jsonResult = JsonResult.create();
jsonResult.setResult("新增成功");


	public List<SUserDueDetail> queryDueDetail(Map<String,Object> params);

	public List<InvestDetailBean> queryInvestDetail(Map<String,Object> params);


            SUserDueDetail sUserDueDetail = sUserDueDetailService.getByRechargeNo(sRechargeLog.getRechargeNo());
        if (sUserDueDetail == null) return;

                        coupon.setTag(SUserCouponCredit.getProjectTagDescs(coupon.getTag()));

daydayAward/getAwardCoupon


 case 1:
                SUserInterestCoupon interestCoupon = new SUserInterestCoupon();

                interestCoupon.setCouponId(getCouponId(gift.getType()));
                interestCoupon.setTitle(gift.getName());
                interestCoupon.setSubtitle(gift.getName());
                interestCoupon.setUserId(userId.intValue());
                interestCoupon.setModifyUserId(userId.intValue());
                interestCoupon.setStatus(0);
                interestCoupon.setAddTime(now);
                interestCoupon.setModifyTime(now);
                interestCoupon.setExpireTime(expire);
                interestCoupon.setInterestRate(BigDecimal.valueOf(gift.getValue()));
                interestCoupon.setMinDue(minDue);
                interestCoupon.setMinInvest(minInvest);
                interestCoupon.setIsRead(0);
                interestCoupon.setSource(gift.getLabel());
                sUserInterestCouponMapper.insertSelective(interestCoupon);
                return interestCoupon.getId();
            case 2:
                SUserRedenvelope redenvelope = new SUserRedenvelope();
                redenvelope.setTitle(gift.getName());
                redenvelope.setContent(gift.getName());
                redenvelope.setUserId(userId.intValue());
                redenvelope.setAddUserId(userId.intValue());
                redenvelope.setModifyUserId(userId.intValue());
                redenvelope.setStatus(0);
                redenvelope.setCreateTime(now);
                redenvelope.setModifyTime(now);
                redenvelope.setExpireTime(expire);
                redenvelope.setAmount(BigDecimal.valueOf(gift.getValue()));
                redenvelope.setMinDue(minDue);
                redenvelope.setMinInvest(minInvest);
                redenvelope.setIsRead(0);
                redenvelope.setSource(gift.getLabel());
                sUserRedenvelopeMapper.insertSelective(redenvelope);
                return redenvelope.getId();



anniversaryDaydayAward



Gift gift3 = Gift.Builder.aGift().value(100).type(3).name(COUPON_NAME).alias("100").label(LABEL).build();
Integer couponId3 = couponService.sendAward(gift3, sUser.getId(), expireDate, 0, BigDecimal.ZERO);
couponService.CashCouponToWallet(sUserCashCouponService.selectByPrimaryKey(couponId3));
sUserRewardLogService.create(
        SUserRewardLog.Builder.aSUserRewardLog()
                .userId(sUser.getId().intValue())
                .content("100元现金")
                .point(0)
                .type(4)
                .typeSupply(new BigDecimal("100"))
                .label(LABEL)
                .couponId(couponId3)
                .build()
);break;


Gift gift3 = Gift.Builder.aGift().value(100).type(2).name(COUPON_NAME).alias("1").label(LABEL).build();
Integer couponId3 = couponService.sendAward(gift3, sUser.getId(), expireDate, 0, BigDecimal.ZERO);


		String userId = getUserIdByToken(token);
o
public String getUserIdByToken(String token) {
		if (token == null || "".equals(token)) {
			return null;
		}
		TokenModel tokenModel;
		String userId = null;
		try {
			BASE64Decoder bASE64Decoder = new BASE64Decoder();
			String tusrc = new String(bASE64Decoder.decodeBuffer(token));
			if (tusrc.startsWith("TK_")) {
				tokenModel = TokenUtil.tokenDecode(tusrc);
				userId = tokenModel.getUserId().toString();
				logger.error("get userId : " + userId + " from token :" + token);
				return userId;
			} else {
                userId = redisService.get(token);
                logger.error("get userId :" + userId + " from redis token :" + token);
            }
		} catch (Exception e) {
			logger.error("getUserIdByToken error : " + token);
			e.printStackTrace();
		}

		return userId;
	}

	public String getUserIdByToken(String token) {
		TokenModel tokenModel;
		if (token == null || "".equals(token)) {
			return null;
		}
		String userId = null;
		try {
			BASE64Decoder bASE64Decoder = new BASE64Decoder();
			String tusrc = new String(bASE64Decoder.decodeBuffer(token));
			if (tusrc.startsWith("TK_")) {
				tokenModel = TokenUtil.tokenDecode(tusrc);
				userId = tokenModel.getUserId().toString();
				logger.error("get userId : " + userId + " from token :" + token);
				return userId;
			} else {
				userId = redisService.get(token);
				logger.error("get userId : " + userId + " from redis token :" + token);
			}
		} catch (Exception e) {
			logger.error("getUserIdByToken error : " + token);
			e.printStackTrace();
		}

		return userId;
	}

RedisService redisService;



set anniversaryDaydayAward:now:time

get anniversaryDaydayAward:INVEST:INTEREST_COUPON:u:8


get anniversaryDaydayAward:RECHARGE:INTEREST_COUPON:u:8


redis-cli -h 139.196.41.47
