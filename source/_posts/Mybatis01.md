---
title: Mybatis01
date: 2018-02-05 11:14:21
tags: [Mybatis]
---

1.Mybatis中 select
---

```java
<select id="selectByChannelCode" resultType="com.zhongfl.tiejun.api.channel.model.ChannelVisitDetail">        
    SELECT
    <include refid="Base_Column_List"/>
    FROM  channel_visit_detail
    WHERE   channel_code = #{channelCode}
</select>
```

2. Mybatis中 insert
---

```java
<insert id="insert" parameterType="com.zhongfl.tiejun.api.channel.model.ChannelOperationLog">
    insert into channel_operationlog_log
    <trim prefix="(" suffix=")" suffixOverrides=",">
      <if test="channelId != null">
        channel_id,
      </if>
      <if test="operType != null">
        oper_type,
      </if>
      <if test="sourceLevel != null">
        source_level,
      </if>
      <if test="targetLevel != null">
        target_level,
      </if>
      <if test="remark != null">
        remark,
      </if>
      <if test="modifiedBy != null">
        modified_by,
      </if>
        oper_time,
    </trim>
    <trim prefix="values (" suffix=")" suffixOverrides=",">
      <if test="channelId != null">
        #{channelId},
      </if>
      <if test="operType != null">
        #{operType},
      </if>
      <if test="sourceLevel != null">
        #{sourceLevel},
      </if>
      <if test="targetLevel != null">
        #{targetLevel},
      </if>
      <if test="remark != null">
        #{remark},
      </if>
      <if test="modifiedBy != null">
        #{modifiedBy},
      </if>
        now(),
    </trim>
  </insert>
```

3.注意模糊查询 concat('%',#{custName},'%')的使用
---

```java
<!-- 根据分页条件查询 客户信息 -->
    <select id="findByConditions"
            parameterType="com.example.demo.domain.QueryVo"
            resultType="com.example.demo.domain.Customer">
        SELECT
        a.cust_id custId,
        a.cust_createtime custCreatetime,
        a.cust_linkman custLinkman,
        a.cust_address custAddress,
        a.cust_name custName,
        b.dict_item_name custSource,
        c.dict_item_name custIndustry,
        d.dict_item_name custLevel,
        a.cust_phone custPhone,
        a.cust_mobile custMobile
        FROM customer a ,base_dict b ,base_dict c , base_dict d
        <where>
            a.cust_source = b.dict_id
            AND a.cust_industry= c.dict_id
            AND a.cust_level = d.dict_id
            <if test="custName != null and custName != '' ">
                AND a.cust_name LIKE concat('%',#{custName},'%')
            </if>
            <if test="custSource != null and custSource != '' ">
                AND a.cust_source = #{custSource}
            </if>
            <if test="custIndustry != null and custIndustry != '' ">
                AND a.cust_industry = #{custIndustry}
            </if>
            <if test="custLevel != null  and custLevel != '' ">
                AND a.cust_level = #{custLevel}
            </if>
        </where>
        <if test="startNum != null ">
            LIMIT #{page},#{pageSize}
        </if>
    </select>
```





