---
title: Mybatis02
date: 2018-03-05 11:14:21
tags: [Mybatis]
---

Mybatis参数传递
---

---

### 1.传递简单类型

```xml
<!-- -->
<!-- 根据id查询 -->
<select id="findUserWithID" parameterType="int" resultType="com.itheima.pojo.User">
	select * from user where id=#{id}
</select>	
	
<!-- 根据用户名模糊查询  -->
<select id="findUserWithLikeUsername" parameterType="string" resultType="com.itheima.pojo.User">
	select * from user where username like "%"#{username}"%"
</select>


<!-- 根据用户名模糊查询  -->
<select id="findUserWithLikeUsername" parameterType="string" resultType="com.itheima.pojo.User">
	select * from user where username like "%"#{username}"%"
	// 最好不要使用 *  来查询 
	select * from  user where username like concat('%',#{username},'%')
</select>
```

### 2.传递多个参数类型,不同类型时

```xml
@Param("page") Integer page

@Param("pageSize") Integer pageSize

@Param("username") String  username
```

### 3.传递pojo对象

```xml
<insert id="saveUser2" parameterType="com.itheima.pojo.User">
		<!-- 
			selectKey:查询用户主键,保存时候查询
			keyProperty:查询结果映射主键的名称
			order:主键生成策略,主键是sql语句执行后生成的
			resultType:指定返回的类型
		 -->
		<selectKey keyProperty="id" order="AFTER" resultType="int">
			SELECT LAST_INSERT_ID()
		</selectKey>
		insert into user values(#{id},#{username},#{birthday},#{sex},#{address})
</insert>
```

### 4.传递pojo包装对象

开发中通过pojo传递查询条件 ，查询条件是综合的查询条件，不仅包括用户查询条件还包括其它的查询条件（比如将用户购买商品信息也作为查询条件），这时可以使用包装对象传递输入参数。Pojo类中包含pojo。

需求：根据用户名查询用户信息，查询条件放到QueryVo的user属性中

POJO类:

```java
public class QueryVo {
	private User user;
	public User getUser() {
		return user;
	}
	public void setUser(User user) {
		this.user = user;
	}
}
```

Mapper文件

```xml
<!-- 
    使用包装类型查询用户使用ognl从对象中取属性值，
    如果是包装对象可以使用.操作符来取内容部的属性
-->
<select id="findUserByQueryVo" parameterType="queryvo" resultType="user">
	SELECT * FROM user where username like '%${user.username}%'
</select>
```

Mybatis返回参数
---

### 1. resultType

#### 1.1 输出简单类型------resultType

```xml
<select id="findUserWithCount" parameterType="QueryVo" resultType="int">
	 	select count(1) from user
	 	where sex = #{user.sex}
	 	and username  like  "%"#{user.username}"%" 
</select>

<!-- 注意使用 concat('%',#{...},'%')  -->

<select id="findUserWithCount" parameterType="QueryVo" resultType="int">
	 	select count(1) from user
	 	where sex = #{user.sex}
	 	and username  like  concat('%',#{user.username},'%')
</select>
```

#### 1.2 输出POJO类型------resultType

```xml
<!-- 需求:查询订单,关联查询用户,实现一对一关联查询,使用resultType进行关系映射 -->	  
	 <select id="findOrderswithUserOneToOne"     resultType="OrderCustomer">
		SELECT
		orders.id,
		orders.user_id userId,
		orders.number,
		orders.createtime createTime,
		orders.note,
		user.*
		FROM orders,USER 
		WHERE orders.user_id = user.id;
	 </select>
```

#### 1.3 输出POJO类型-------输出pojo列表

需求:查询订单,关联查询用户,实现一对一关联查询,使用resultType进行关系映射

```xml
<mapper namespace="com.itheima.dao.OrdersMapper">
	<!-- 
		需求:查询订单,关联查询用户,实现一对一关联查询,使用resultType进行关系映射
	 -->	  
	 <select id="findOrderswithUserOneToOne"  resultType="OrderCustomer">
		SELECT
		orders.id,
		orders.user_id userId,
		orders.number,
		orders.createtime createTime,
		orders.note,
		user.*
		FROM orders,USER 
		WHERE orders.user_id = user.id;
	 </select>
	 
	 
	 
	 <!-- 
	 	定义map关系映射
	 	resultMap: 定义查询列名和javabean属性名称一一对应关系映射标签
	 	type:查询结果返回值类型
	 	id:resultMap的唯一
	  -->
	 <resultMap type="orders" id="orderMap">
	 	 <!-- 
	 		id:定义主键映射关系
	 		column:查询列表
	 		property:javabean属性名称
	 	 -->
	 	 <!-- 
	 	 	result :定义普通属性的关系映射
	 	 	column :查询列名
	 	 	property:javabean属性名称
	 	 -->
	 	 <id column="id" property="id"/>
	 	 <result column="user_id"  property="userId"/>
	 	 <result column="number"  property="number"/>
	 	 <result column="createtime"  property="createTime"/>
	 	 <result column="note"  property="note"/>
	 	 
	 	 <!-- 
	 	 	association:用户描述一对一关系映射属性
	 	 	property: 指定映射的属性
	 	 	javaType: 指定映射属性类型
	 	  -->
	 	 <association property="user" javaType="user">
	 	 	<id column="user_id" property="id"/>
	 	 	<result column="username" property="username"/>
	 	 	<result column="birthday" property="username"/>
	 	 	<result column="sex" property="sex"/>
	 	 	<result column="address" property="address"/>
	 	 </association>
	 	 
	 </resultMap>
	 
	 
	 <!-- 
		需求:查询订单,关联查询用户,实现一对一关联查询,使用resultMap进行关系映射
	 -->	  
	 <select id="findOrderswithUserOneToOneMap"  resultMap="orderMap">
		SELECT
		*
		FROM orders,user 
		WHERE orders.user_id = user.id;
	 </select>
</mapper>
```

### 2.ResultMap

#### 2.1 输出POJO类型

需求: 查询用户关联查询订单

```xml
<mapper namespace="com.itheima.dao.UserMapper">
	<!-- 
		需求:查询用户关联查询订单
		只能使用resultMap进行关系映射,不能使用ResultType
	 -->	
	 <!-- 定义关系映射的Map -->
	 <resultMap type="user" id="userMap">
 		<id column="id" property="id"/>
 	 	<result column="username" property="username"/>
 	 	<result column="birthday" property="username"/>
 	 	<result column="sex" property="sex"/>
 	 	<result column="address" property="address"/>
 	 	
 	 	<!-- 一个用户对应多个订单 -->
 	 	<!-- 
 	 		collection:定义一对多的关系映射
 	 		property:表示映射user中的那个属性
 	 		ofType:指定映射对象最终类型
 	 	 -->
 	 	<collection property="oList" ofType="orders">
 	 		<id column="oid" property="id"/>
	 		<result column="user_id"  property="userId"/>
	 	 	<result column="number"  property="number"/>
	 	 	<result column="createtime"  property="createTime"/>
	 	 	<result column="note"  property="note"/>
 	 	</collection>
	 </resultMap>
	 
	   
	 <select id="findUserWithOrders" resultMap="userMap">
	 	 SELECT 
	 	 user.*,
	 	 orders.id oid ,
	 	 orders.user_id,
	 	 orders.number,
	 	 orders.createtime,
	 	 orders.user_id
	 	 FROM USER ,orders
	 	 WHERE user.id = orders.user_id;
	 </select>
</mapper>
```

User类

```java
public class User {
	private Integer id;
	private String username;
	private Date birthday;
	private String sex;
	private String address;
	//一个用户对应多个订单
	private List<Orders> oList;
	.....
}	
```

Orders类

```java
public class Orders {
	private Integer id;
	private Integer userId;
	private String number;
	private Date createTime;
	private String note;
} 
```


