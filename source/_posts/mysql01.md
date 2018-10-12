---
title: mysql学习
date: 2017-11-26 14:23:50
tags: [mysql,sql]
---

#### 01.数据库的概念：

	1).数据库的概念：数据库(Database)，就是存储,维护，管理数据的仓库。
	2).作用：用来存储和管理大量数据的。内部采用了非常便于查询的机制来存储数据，能保证我们在大量数据的情况下
	         可以很快，并且很准确为我们查询到所需记录。
	3).什么是数据库管理系统：指一种操作和管理数据库的大型软件，用于建立、使用和维护数据库，对数据库进行统一管理和控制，以保证数据库的安全性和完整性。用户通过数据库管理系统访问数据库中表内的数据。
#### 02.数据库内部的结构：

	数据库软件：
	   |--逻辑数据库(跟项目相关)
		|--表
		    |--列
		    |--行(记录)
		|--表
		....
	   |--逻辑数据库(跟项目相关)
	   ....
#### 03.Java和数据库的对应关系：

java		数据库

    项目		逻辑数据库
    类		表
    类中成员属性	表的字段(列)
    属性的数据类型	字段的数据类型
    对象		表中的一行记录
#### 04.常见的数据库管理系统

	MYSQL	：开源免费的数据库，小型的数据库.已经被Oracle收购了.MySQL6.x版本也开始收费。
	Oracle	：收费的大型数据库，Oracle公司的产品。Oracle收购SUN公司，收购MYSQL。
	DB2：IBM公司的数据库产品,收费的。常应用在银行系统中.
	SQLServer：MicroSoft 公司收费的中型的数据库。C#、.net等语言常使用。
	SyBase	：已经淡出历史舞台。提供了一个非常专业数据建模的工具PowerDesigner。
	SQLite	: 嵌入式的小型数据库，应用在手机端。
	常用数据库：MYSQL，Oracle．
	这里使用MySQL数据库。MySQL中可以有多个数据库，数据库是真正存储数据的地方。
#### 05.MySQL的安装和客户端连接：

	1.连接MySQL服务器端：
		1).使用命令行：
			mysql -uroot -p密码 (回车)
		2).使用SQLYog客户端:
			直接启动，在连接界面填写：服务器IP，端口，用户名、密码，点击：连接
#### 06.SQL语句的介绍：

	1.普通话：标准的SQL语言，各个数据库厂商必须遵守的。
	2.方言：个数据库厂商自己开发的基于的SQL的一些新功能的语法。只在自己的数据库
	        上有效。
#### 07.SQL语言的分类：

（结构化查询语言）

```sql
1.DDL:数据定义语言,来定义数据库对象：逻辑数据库，表，列等。关键字：
create（创建），alter（修改），drop（删除）等
2.DCL:数据控制语言.用来定义数据库的访问权限和安全级别，及创建用户。
grant ,revoke,if.
3.DML【重点掌握】:数据操作语言。用来对数据库中表的"记录"进行更新。
关键字：insert（添加），delete（删除），update（修改）等
4.DQL【重点掌握】:数据查询语言。用来查询数据库中表的"记录"。
关键字：select，from，where,groud by ,order by,having ,limit 聚合函数等
```
#### 08.SQL通用语法：	

```sql
1.SQL语句可以单行或多行书写，以分号结尾
2.可使用空格和缩进来增强语句的可读性
3.MySQL数据库的SQL语句不区分大小写，关键字建议使用大写
  例如：SELECT * FROM user。
4.注释：
	1).#单行注释
	2).--(空格)单行注释
	3)./* ... */ 多行注释
```
#### 09.数据库操作的相关语句：

```sql
1.创建数据库：
	create database 数据库名;
	或者
	create database 数据库名 character set 字符集;
2.查看所有数据库：
	show databases;
3.查看某个数据库的定义的信息:	
	show create database 数据库名;
4.删除数据库：
	drop database 数据库名;
5.查看当前正在使用的数据库：
	select database();
6.切换数据库：
	use 数据库名；
```
#### 10.表操作的相关语句：

```sql
1.创建表：
	create table 表名(
		字段名1  数据类型[长度]  [约束],
		字段名2  数据类型[长度]  [约束],
		...
		字段名n  数据类型[长度]  [约束]
	);
   例如创建一个学员信息表，存储：学号、姓名、性别、年龄
	create table student(
		stuNo int,
		stuName	varchar(200),
		stuSex	char(5),
		stuAge	int
	);
2.Java的数据类型与MySQL中的数据类型
	java数据类型		MySQL数据类型
  ------------------------------------------------------------
        整数:
	int			int

	小数：
	float/double		float/double/decimal(m,n)
```


```sql
	字符：
	char			char

	字符串：
	String			char(定长)/varchar(不定长)
	在Java中char表示一个字符；而MySQL中的char表示：可变的字符串；

        =============================================================================================================
	在MySQL中char和varchar的区别：
	1.char:定长字符串：例如定义字段为：char(5):
	   表示最多存储5个字符，如果不足5个字符，剩下的用空字符填充。
	   例如：定义char(5)-->要存储字符串"abc"-->在硬盘上存储的格式-->"abc  "
			        要存储字符串"abcd"-->在硬盘上存储的格式-->"abcd "

	2.varchar:不定长字符串：例如定义字段为：
		varchar(5)表示最多存储5个字符，如果不足5个字符，不填充空字符。
		例如：定义varchar(5)-->要存储字符串"abc"-->在硬盘上存储的格式-->"abc"
			     要存储字符串"abcd"-->在硬盘上存储的格式-->"abcd"

	注意：
	1.char类型的在查询效率上要高于varchar，所以，尽量选择char类型；
	2.对于字段的平均长度相同或者变化不大的数据，优先使用char类型。
		例如：手机号码、身份证号、银行卡号....
	3.对于字段的平均长度相差比较大的数据，建议使用varchar类型。
		例如：个人介绍......

	=============================================================================================================
	日期类型：
	String
	Date		date(日期常用)	范围：YYYY-MM-DD 1000-01-01~9999-12-3
				datetime【常用】(日期和时间)范围：YYYY-MM-DD HH:MM:SS 1000-01-01 00:00:00~ 9999-12-31 23:59:59
	大文本：
	String			Text

	文本二进制
	byte[]			binary

	二进制(图片，视频)
	byte[]			Blob
3.查看数据库中的所有表：
	show tables;
4.查看表结构：
	desc 表名;
5.删除表：
	drop table 表名;
6.修改表：
	alter table 表名 add 列名 类型(长度) [约束];	
	作用：修改表添加列. 
	例如：
	#1，为分类表添加一个新的字段为分类描述 varchar(20)
	ALTER TABLE category ADD `desc` VARCHAR(20);

	alter table 表名 modify 列名 类型(长度) 约束;
	作用：修改表修改列的类型长度及约束.
	例如：
	#2, 为分类表的描述字段进行修改，类型varchar(50) 添加约束 not null
	ALTER TABLE category MODIFY `desc` VARCHAR(50) NOT NULL;

	alter table 表名 change 旧列名 新列名 类型(长度) 约束; 
	作用：修改表修改列名.
	例如：
	#3, 为分类表的分类名称字段进行更换更换为 snamesname varchar(30)
	ALTER TABLE category CHANGE `desc`description VARCHAR(30);

	alter table 表名 drop 列名;	
	作用：修改表删除列.
	例如：
	#4, 删除分类表中snamename这列
	ALTER TABLE category DROP description;

	rename table 表名 to 新表名;
	作用：修改表名
	例如：
	#5, 为分类表category改名成 category2
	RENAME TABLE category TO category2;

	alter table 表名 character set 字符集;
	作用：修改表的字符集
	例如：
	#6, 为分类表 category 的编码表进行修改，修改成 gbk
	ALTER TABLE category CHARACTER SET gbk;
```
#### 11.表记录操作相关的语句：

```sql
1.添加数据：insert into
	两种格式：
	1.insert into 表名 values(值1，值2，.....，值n)--全字段添加
	  注意：
	  1).后面值列表中的数量必须跟表中列的数量匹配，而且顺序也要匹配。
	  2).值：数值类型，可以不用单引号(用也可以)
	         字符串类型，一定要使用单引号。
        2.insert into 表名(字段1,字段2,.....,字段n) values(值1,值2,....,值n)--部分字段添加，剩余字段添加：NULL
	  注意：
	  1).字段列表：可以是表的部分字段，也可以不按照定义顺序；
	  2).值列表：必须跟字段列表的数量和顺序要匹配。
	  3).未指定的字段，添加：NULL值。(前提是：字段允许NULL值)
2.修改数据：update
	格式：update 表名 set 字段名1 = 值1 , 字段名2 = 值2 , .... , 字段名n = 值n   where 条件；
3.删除数据：delete from
	格式：delete from 表名 where 条件;
  清空表：
	1).delete from 表名;逐行删除，效率低；不清空auto_increment记录数
	2).truncate 表名;先摧毁表，然后按照原结构再创建一个新表，效率高；auto_increment将置为零，从新开始

4.查询数据：select 字段名 from 表名 where 字段的筛选条件
	1.简单查询：
		1).查询所有字段的所有记录：
			select pid,pname,price,categoryName from product;
			或者
			select * from product;
		2).查询部分字段的所有记录：
			select pname,price from product;
		3).使用别名：
			a).列别名：
				SELECT pname AS '商品名称' , price AS '价格' FROM product;
			b).表别名：
				SELECT p.pname,p.price FROM product p;//一般在多表中使用别名
		4).去掉重复值
				SELECT DISTINCT price FROM product;
		5).对查询结果进行运算：
			例如：将所有查询结果的商品的价格加100显示：
			select pname,price + 100 from product;
		        注意：只对查询结果进行更改，原数据没有更改。
	2.条件查询：
		1).比较运算符：
			1).">":大于。例如：查询价格大于2000元的商品		--针对数值类型查询
				select * from product where price > 2000;
			2)."<":小于。例如：查询价格小于2000元的商品		--针对数值类型查询
				select * from product where price < 2000;
			3).">=":大于等于。例如：查询价格大于等于2000元的商品	--针对数值类型查询
				select * from product where price >= 2000;
			4)."<=":小于等于。例如：查询价格小于等于2000元的商品	--针对数值类型查询
				select * from product where price <= 2000;
			5)."<>":不等于。例如：查询价格不等于2000元的商品	--针对各种类型
				select * from product where price <> 2000;
			   "!=":不等于						--针对各种类型
				select * from product where price != 2000;
			6)."=" :等于.例如：查询价格等于2000元的商品		--针对各种类型
				select * from product where price = 2000;
		2).逻辑运算符：
			1).and : 语义：并且
				例如：查询所有商品价格大于2000元的电脑类商品
				select * from product where price > 2000 and categoryName = '电脑';
			2).or  : 语义：或者
				例如：查询所有商品价格大于2000元，或者价格低于1000元的所有商品
				select * from product where price > 2000 or price < 1000;
			3).not : 语义：非
				例如：查询商品价格不大于2000元的所有商品
				select * from product where not(price > 2000);

			注意：如果多个and和or运算，中间不要加逗号，可以使用()改变运算顺序。
			例如：查询所有价格大于2000元的电脑类商品或者服装类商品
				select * from product where price > 2000 and (categoryName = '电脑' or categoryName = '服装');
		3).范围查询：between ... and ...(可以用于数值类型，也可以用于日期类型)
			1).用于查询数值范围：between(包含)....and(包含)...
			   例如：查询价格在1000元(包含)到2000元(包含)之间的所有商品
				select * from product where price >= 1000 and price <= 2000;
				或者
				select * from product where price between 1000 and 2000;
			2).用于查询日期范围：
			    例如：查询生产日期在2017年1月份的所有商品
				select * from product where proDate between '2017-01-01' and '2017-01-31';
		4).多个值的判断：in(值列表)
			例如：查询商品价格为200元，500元，1000元，2000元的商品信息
				select * from product where price = 200 or price = 500 or price = 1000 or price = 2000;
				或者使用in查询
				select * from product where price in (200,500,1000,2000);

		5).模糊查询：like 两个通配符：1)"%" : 任意的0到多个字符；2)"_":任意的1个字符
			例如：查询商品名称中包含"花"的商品信息
				select * from product where pname like '%花%';
			      查询商品名称中以"花"字开头的商品：
			        select * from product where pname like '花%';
			      商品名称以"花花"开头，全名是四个字的商品：
			        select * from product where pname like '花花__';
		6).查询空字段：
			1).添加一条记录时，不添加的字段可以指定为NULL值，例如：
				insert into product values(14,'果10',200,'食品',NULL);
			   要查询所有"生产日期"没有添加的所有商品：
				select * from product where proDate IS NULL;
			3).添加一条记录：
				insert into product values(15,'果11',300,'',null);
			   要查询出来这条记录：
				select * from product where proDate = '';
```




##### 

##### 
