---
title: mysql学习
date: 2017-11-27 14:23:50
tags: [mysql,sql]
---

#### 01.SQL高级查询_排序：

	1.使用的关键字：order by 字段名 ASC(升序--默认) / DESC(降序)
	  例如：查询所有商品，要求结果按价格从小到大排序
		SELECT * FROM product ORDER BY proDate ASC;
	2.注意：升序可以不写asc关键字，例如：
		select * from product order by proData;//升序
	3.排序：
		升序(ASC):从小到大；
		降序(DESC):从大到小；
	4.对多列进行排序：
	  例如：对多列进行排序：按金额排序，如果金额相同，按生产日期升序排序
		SELECT * FROM product ORDER BY price ASC,proDate ASC;
		先按第一个字段排序，在第一个字段值相同的情况下，再按第二个字段排。
	5.如果有查询条件，写法：
		select * from 表名 where 条件  order by 字段 ... ;
#### 02.SQL高级查询_聚合函数：

	1.我经常会有需求，对某列进行汇总，这就需要使用"聚合函数"；
	2.今天我们掌握的五个聚合函数：
		a).count(*/字段名)：统计指定列不为NULL的记录行数--任何数据类型
			例如：查询电脑类别的商品，共有多少种
			SELECT COUNT(*) FROM product WHERE categoryName = '电脑';
		b).sum(列名)：计算指定列的数值和，如果指定列类型不是数值类型，那么计算结果为0--数值类型的列
			例如：查询电脑类商品的价格总数是多少？
			select sum(price) from product where categoryName = '电脑';
		c).max(列名)：计算指定列的最大值，如果指定列是字符串类型，那么使用字符串排序运算--数值类型、日期类型
			例如：查询电脑类商品的最高价格？
			select max(price) from product where categoryName = '电脑';
		d).min(列名)：计算指定列的最小值，如果指定列是字符串类型，那么使用字符串排序运算
			例如：查询电脑类商品的最低价格？
			select min(price) from product where categoryName = '电脑';
		e).avg(列名)：计算指定列的平均值，如果指定列类型不是数值类型，那么计算结果为0
			例如：查询电脑类商品的平均价格？
			select avg(price) from product where categoryName = '电脑';
			注意：计算记录的总数量时，不包含NULL的记录。
			      所以如果计算的列中有NULL值，则结果不准确。
	3.注意：聚合查询的结果，只能包含"聚合结果列"，不要包含其他列，要包含，其结果是无意义的。
	        聚合的结果是"计算的结果"，跟某行数据无关，所以不能关联显示其它字段。

#### 03.SQL高级查询_分组：

```sql
1.分组：对某列中"相同的值"作为一组，进行分组。分组只是手段，后续经常需要进行汇总：
2.例如：一条语句查询出每种商品的最高价格是多少？
	SELECT categoryName,MAX(price) FROM product GROUP BY categoryName;
	练习：查询每种商品的价格的总和
	SELECT categoryName,SUM(price) FROM product GROUP BY categoryName;
	练习：查询每种商品的商品数量是多少
	SELECT categoryName,COUNT(*) FROM product GROUP BY categoryname;
3.注意：
   1).分组查询的结果字段中，只能包含"分组字段"，"聚合结果字段"。不能再包含其他字段，如果包含，其结果也是无意义的。
4.having子句：
   1).由于where不能对聚合后的结果进行筛选。所以要对聚合后的结果进行筛选，需要使用having子句。
	例如：查询每种商品的价格总额，结果保留大于1000元的。
		select categoryName,sum(price) from product group by categoryName having sum(price) > 1000;
5.对多列进行分组：
    收支流水表：trans
    id		收支项		账户		金额
    1		工资收入	工商银行	1000
    2		红包收入	工商银行	500
    3		收入		交通银行	3000
    4		支出		工商银行	300
    5		支出		交通银行	770

    需求：查询出每个账户的收支总额，分别是多少？
	账户		收支项		总金额
	工商银行	收入		1500
	工商银行	支出		300
	交通银行	收入		3000
	交通银行	支出		770
   
   select 账户,收支项,sum(金额) from trans group by 账户,收支项;//先按账户分，再按收支项分。
```

#### 04.SQL语句的执行顺序： 

	1).from
	2).where
	3).group by
	4).having
	5).select
	6).distinct
	7).order by

   SQL语句的编写顺序：
	select ... from ...  where ... group by ... having ... order by ...;
#### 05.分页查询：

```sql
1).基本语句：select * from 表名 limit M,N;
             M值：从第几条(第一条记录为0)记录开始取。
	     N值：取几条记录
2).例如：查询所有的商品，每页显示5条：
	第一页：
	select * from product limit 0,5;
	第二页：
	select * from product limit 5,5;
	第三页：
	select * from product limit 10,5

	固定算法：
	select * from product limit (当前的页数 - 1) * 每页显示的条数
3).注意：M值和N值，只要是正数，不会抛异常，可能会返回空结果集。
         但如果是负数，会抛异常。
```
#### 06.备份和恢复数据库：

	1).备份：在要备份的数据库上右键-->备份/导出-->以SQL转储文件备份数据库
	2).恢复：在SQLYog左侧右键-->导入-->从SQL转储文件导入数据库
#### 07.SQL的约束：

```sql
1).主键约束：
	1).主键的作用：唯一标识表中一条记录。用于作为条件，方便的进行增删改查操作。
	2).定义主键：
		create table product(
			pid int primary key,
			..其它字段..
			..
		)
	3).一个表中只能有一个主键；
	4).一个主键，可以由一个或多个字段组成[很少用]；复合主键，联合主键
		客户信息表：将"客户姓名" + "工作单位" 同时作为一个主键
		客户姓名	工作单位	性别	年龄
		张三		人事部		男	20
		李四		人事部		女	22
		张三		业务部		男	23
		张三		人事部		男	18 //错误的数据
	5).任何类型的字段都可以做主键。当前使用int类型。后期varchar
	6).为某个字段添加了"主键约束"，也同时自动添加：唯一约束、非空约束
	7).删除主键约束：
		ALTER TABLE 表名 DROP PRIMARY KEY;
2).自动增长：
	1).自动增长：让某列的值根据某个基数，进行自增。这种约束通常用于"主键".
	2).添加自动增长约束：
		create table product(
			pid int primary key auto_increment,
			....
		)
	3).清空表对自动增长列的基数的变化：
		1).delete from 表名:逐行删除。不改变自动增长的基数。
		2).truncate 表名【效率高】：摧毁表，重建表。将自动增长的基数重新设置为1.
3).非空约束：NOT NULL
	1).作用：强制某列的数据不能包含NULL值；
	2).添加非空约束：
		create table product(
			pid int primary key,
			pname varchar(200) not null,
			....
		)
		如下添加，会抛出异常：
		insert into product values(null,null,...);//第二个null是错误，pname字段不允许null值
	3).删除非空约束 
		ALTER TABLE 表名 MODIFY 列名 数据类型[长度] (后面不出现not null约束即可，就表示删除了not NULL约束) 
4).唯一约束：unique
	1).作用：表示本列的值是唯一的
	2).添加唯一约束：
		create table product(
			pid int primary key,
			pname varchar(200) unique,
			...
		)
	   如果向pname字段添加重复的值，数据库会抛出异常。
	3).如果字段设置了唯一约束，可以写入"空字符串"，但只能有一条。
	   也可以写入NULL值，可以写入多条。
	4).删除唯一约束：
		ALTER TABLE 表名 DROP INDEX 名称;
		如果添加唯一约束时，没有设置约束名称，默认是当前字段的字段名
	5).主键与唯一约束的区别：
		主键：代表：唯一、非空；一个表只能有一个主键；
		唯一：只代表：唯一；可以有多个NULL值；一个表可以有多个字段被设置为唯一约束；
5).默认约束：default 值;
	1).作用：可以设置某列的默认值，在添加数据时，可以不指定这列的数据，而使用默认值。
	2).设置默认约束：
		create table student(
			id	int	primary key auto_increment,
			stuName	varchar(20)	not null,
			sex	char(5)	default '男'
		)
		在添加时，如果要使用默认值：
		INSERT INTO student VALUES(NULL,'bbb',DEFAULT);
	3).删除默认约束：
		ALTER TABLE 表名 MODIFY 列名 数据类型[长度](后面不要出现default关键字即可)
```
#### 08.多表_分表的作用：

	1.在制作表时要注意：一个表只描述一件事情。如果需要描述多件事情，可以创建多表，然后通过某个字段去引用
	                    另一个表的数据。这样可以使每个表的数据单独管理，互不影响。
	                            
	2.分表后：
		主表：被其它表引用的表；
		从表：引用其它表的表；
	    3.作用：
	            避免主键冲突，减少数据冗余
#### 09.多表_表和表之间的关系：

	1.一对多关系【最常用】：
		1).应用场景：客户和订单；分类和商品；部门和员工
			客户表:主表				订单表：从表                            外键
			----------------------------------------------------------------------------------------
			客户ID	登录名	支付宝			订单ID	订单时间	总金额		客户ID
			001	zhangsan  xxx			001	xxx		xxx		001
						  		002	xxx		xxx		001
								
		2).建表原则：在从表(多方)创建一个字段，字段作为外键指向主表(一方)的主键.
			
	2.多对多关系【较常用】：
		1).应用场景：订单和商品、用户和角色
			订单表					商品表：
			-----------------------------------------------------------------------------------------
			订单ID	订单日期   总金额  		商品ID	名称	单价	
			d001	2017-07-04  100			p001	奥利奥	5.5
			d002	2017-07-05  200			p002	红牛	4
								p003	啤酒	2.00
	
						订单_商品_关系表
						订单Id		商品ID	数量	总价
						d001		p001	2	11
						d001		p002	3	12
						d001		p003	
						d002		p001
		2).建表原则：需要创建第三张表,中间表中至少两个字段，这两个字段分别作为外键指向各自一方的主键。
	3.一对一关系【不建议用】：
		1).客户信息表：						地址表
			姓名	性别	年龄	地址ID			id	省	市	区	街道门牌
			张三	男	22	01			01	北京	北京	顺义	99号
			李四	女	23	02			02	河北	廊坊	安次	88号
		------------------------------------------------------------------------------------------------
		   合并为一个客户表：
			姓名	性别	年龄	省	市	区	街道门牌
			张三	男	22	北京	北京	顺义	99号
			李四	女	23	河北	廊坊	安次	88号
#### 10.外键约束：

	1).作用：设置在"从表"的外键字段上，可以强制外键字段的值必须参考主表中的主键字段的值。
	2).设置外键约束：
		alter table 从表 add [constraint] [外键名称] foreign key (从表外键字段名) references 主表 (主表的主键);
	3).使用外键目的：
		保证数据完整性
#### 学习目标总结：

3，能够使用SQL语句进行排序
a，	说出排序语句中的升序和降序关键字

		order by 字段名 ASC(升序-默认) / DESC(降序)
b，	写出排序语句
		select * from product order by price desc;
4，能够使用聚合函数
a，	写出获取总记录数的SQL语句
		select count(*) from product;
b，	写出获取某一列数据总和的SQL语句
		select sum(price) from product;
c，	写出获取某一列数据平均值的SQL语句
		select avg(price) ...
d，	写出获取某一列数据的最大值的SQL语句
		select max(price) ...
e，	写出获取某一列数据的最小值的SQL语句
		select min(price) ...
5，能够使用SQL语句进行分组查询
a，	写出分组的SQL语句
		group by 字段名
b，	写出分组后条件过滤器的SQL语句
		gruup by 字段名 having 聚合函数 条件;
6，能够完成数据的备份和恢复
	1.备份：在要备份的数据库上右键-->备份/导出-->以SQL转储文件备份数据库
	2.恢复：在SQLYog左边右键-->导入-->以SQL转储文件导入数据库。
7，能够使用可视化工具连接数据库,操作数据库
	使用SQLYog连接数据库。操作数据库

8，能够说出多表之间的关系及其建表原则
a，	说出一对多的应用场景及其建表原则
		1).应用场景：客户和订单，分类和商品，部门和员工.
		2).在从表(多方)创建一个字段，字段作为外键指向主表(一方)的主键.
b，	说出多对多的应用场景及其建表原则
		1).应用场景：学生和课程、用户和角色
		2).需要创建第三张表,中间表中至少两个字段，这两个字段分别作为外键指向各自一方的主键.
9，能够理解外键约束
a，	说出外键约束的作用
		作用：强制外键字段的值必须参考主表中主键字段的值。
b，	写出创建外键的SQL语句
		alter table product add constraint fk_fkname foreign key (categoryid) references category (cid);
c，	通过sql语句能够建立多表及其关系
		创建表的外键，并且创建外键约束。