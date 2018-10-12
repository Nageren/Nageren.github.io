---
title: Mybatis03
date: 2018-03-21 14:27:57
tags: [Mybatis]
---



动态的sql
===

```xml
<mapper namespace="com.itheima.dao.UserMapper">
    <!--
        sql: 定义sql片段,抽取公共的sql
        id: 定义sql片段的唯一标识
    -->
    
    <sql id="userSqlWhere">
		<where>
	 		<if test="user.sex !=null and user.sex !='' ">
	 			sex = #{user.sex}
	 		</if>
	 		<if test="user.username !=null and user.username !=''  ">
				and username like "%"#{user.username}"%"
			</if>
	 	</where>
	</sql>
	
	
	<!-- 查询姓张的用户,性别为男 -->
	<!-- 
		不使用where标签
	 -->
	<!-- <select id="findUserWithSexAndUsername" parameterType="QueryVo" resultType="user">
		select * from user 
		where 1=1
		<if test="user.sex !=null and user.sex !='' ">
			sex=#{user.sex} 
		</if>
		<if test="user.username !=null and user.username !=''  ">
			and username like "%"#{user.username}"%"
		</if> 
	</select> -->
	
	
	
	<!-- 
		使用where标签
	 -->
	<select id="findUserWithSexAndUsername" parameterType="QueryVo" resultType="user">
		select * from user 
		<where>
	 		<if test="user.sex !=null and user.sex !='' ">
	 			sex = #{user.sex}
	 		</if>
	 		<if test="user.username !=null and user.username !=''  ">
				and username like "%"#{user.username}"%"
			</if>
	 	</where>
	</select>
	
	
	
	<!-- 
	 	查询性别为男,姓张的用户的总条数
	 	where标签 :自动生成where条件,不需要的时候自动去掉and
	  -->
	
	 <select id="findUserWithCount" parameterType="QueryVo" resultType="int">
	 	select count(1) from user
		<!-- 
			include: 引入sql片段
			id: 通过id引入sql片段
		 -->
		<include refid="userSqlWhere"/>
	 </select>
	 
	 
	 <!-- 
	 	需求: 
	 	 	向sql传递数组或List，mybatis使用foreach解析，
	 	 如下：
	 	 	SELECT * FROM USER  WHERE id= 24 OR id =25 OR id =39;
			SELECT * FROM USER  WHERE id IN (24,25,39);
			
			SELECT * FROM USERS WHERE username LIKE '%张%' AND (id =10 OR id =89 OR id=16)
			SELECT * FROM USERS WHERE username LIKE '%张%'  id IN (10,89,16)
	  -->
	   <select id="findUserWithListOr" parameterType="queryVo" resultType="user">
	  	  SELECT * FROM user
	  	  <where>
	  	  	 <if test="ids!= null and ids.size > 0">
	  	  	 	<!-- 
	  	  	 		foreach:循环遍历标签,此时foreach用来循环遍历queryVo里面的ids集合
	  	  	 		item:临时遍量,把每次获取的值存储在item中
	  	  	 		open:以什么开始,循环以什么开始
	  	  	 		separator:循环的间隔符号
	  	  	 		close:循环以什么结束
	  	  	 	 -->
	  	  	 	<foreach collection="ids"  item="id" open="(" separator="OR" close=")">
	  	  	 		id=#{id}
	  	  	 	</foreach>
	  	  	 </if>
	  	  </where>
	  </select>
	  
	  
	   <!--  SELECT * FROM USER  WHERE id IN (24,25,39); -->
	  <select id="findUserWithListIn" parameterType="queryVo" resultType="user">
	  	  SELECT * FROM user
	  	  <where>
	  	  	 <if test="ids!= null and ids.size > 0">
	  	  	 	<foreach collection="ids"  item="id" open="and id in(" separator="," close=")">
	  	  	 		#{id}
	  	  	 	</foreach>
	  	  	 </if>
	  	  </where>
	  </select>
	
</mapper>
```