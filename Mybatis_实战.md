MyBatis 实用 note



resultMap
resultType
parameterType

<mapper namespace="com.yiding.mapper.SUserHistoryMapper">
    <resultMap type="com.yiding.model.SUserHistory" id="sUserHistoryMap">
		<result property="id" column="id"/>
		<result property="oldUserId" column="old_user_id"/>
		<result property="newUserId" column="new_user_id"/>
		<result property="mobile" column="mobile"/>
		<result property="share_coupon" column="share_coupon"/>
		<result property="type" column="type"/>
		<result property="insert_time" column="insert_time"/>
    </resultMap>


<select id="count" parameterType="map" resultType="Integer">
      SELECT count(id) FROM s_user_history WHERE 1 = 1
		<if test="id != null">
			AND id = #{id}
		</if>

    </select>

<select id="get" parameterType="map" resultMap="sUserHistoryMap">
        SELECT * FROM s_user_history WHERE 1 = 1
		<if test="id != null">
			AND id = #{id}
		</if>
		<if test="mobile != null">
			AND mobile = #{mobile}
		</if>
		limit 0, 1
    </select>


mapper namespace

<mapper namespace="com.yiding.mapper.SUserHistoryMapper">
<mapper namespace="clark">
namespace只是一个标识， 可以随便写什么，但是一般都是写成和它对应的mapper类。


selectKey

<insert id="create" parameterType="com.yiding.model.SUserHistory">
        <selectKey resultType="Integer" order="AFTER" keyProperty="id" >    


<trim prefix="(" suffix=")" suffixOverrides=",">        


1. <set>
<update id="update" parameterType="com.yiding.model.SUserHistory">
		UPDATE s_user_history
		<set>
			<if test="oldUserId != null">old_user_id = #{oldUserId},</if>
			<if test="newUserId != null">new_user_id = #{newUserId},</if>
			<if test="mobile != null">mobile = #{mobile},</if>
			<if test="share_coupon != null">share_coupon = #{share_coupon},</if>
		</set>
		WHERE 1=1 <if test="id != null and ''!= id">and id = #{id} </if> <if test="mobile != null and ''!= mobile"> and mobile = #{mobile} </if>

	</update>


typeAlias



映射XML文件

       上文示例已经展示了映射XML文件的用法，下面我们逐个细节理解。

       1. select、update、delete和insert标签用于填写对应动作的SQL语句。

       2. parameterType属性就是入参类型具体法则如下(parameterMap已被deprecated，所以不理也罢)：

          a. parameterType为POJO类型时，可通过 #{属性名} 填入属性值（该属性值经过防SQL注入处理），也可通过 ${name} 填入属性raw值（未经过防SQL注入处理的属性值），更爽的是 #{} 支持短路径操作如上文中的 #{myClass.id} 。

          b. parameterType为int、long等值类型时，当仅有一个入参时，可以使用 #{任意字符} 填入属性值，但无法通过 ${任意字符串} 填入属性raw值（报找不到改实例属性），还可以通过 #{0} 和 #{param0} 来填入属性值；而入参为多个时，则只能使用 #{0}到#{n} 和 #{param0}到#{paramn} 来填入属性值了；但由于动态SQL下的标签仅识别 #{0} 等格式的占位符，因此建议通过使用 #{0} 格式的占位符，保持代码一致性。

       3. resultType属性就是返回值类型。

       4. sql标签则用于重用SQL片段，示例如下：

复制代码
<sql id="studentCols">
  Name, ClassID
</sql>
<select id="qryStudent" resultType="EStudent">
  select <include id="studentCols"/> from TStudentML
</select>




## Reference

- [MyBatis魔法堂：即学即用篇](http://www.cnblogs.com/fsjohnhuang/p/4014819.html)
- [Mybatis 高级结果映射 ResultMap Association Collection](http://blog.csdn.net/wxwzy738/article/details/24742495)