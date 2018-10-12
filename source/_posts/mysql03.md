---
title: mysql学习
date: 2017-11-28 14:23:50
tags: [mysql,sql]
---

#### 01.多表查询_交叉查询【了解】

	1.查询结果=左表的总记录数 * 右表的总记录数 -- 笛卡尔积
#### 02.多表查询_内连接查询【重点掌握】

	1.隐式内连接【常用】：
		1).格式：select 字段列表 from 表1,表2 where 表1和表2的等值关系;
		2).例如：查询商品信息，要显示所对应类别信息
			select * from products , category where products.category_id = catetory.cid;
			只保留两个表的部分字段，使用表别名：
			SELECT p.pname,p.price,c.cname FROM products p , category c WHERE p.category_id = c.cid;
		3).练习：查询"市"的所有信息，并且显示对应的"省名"
			SELECT c.cname AS '市',p.pname AS '省' FROM city c,province p WHERE c.pid = p.pid;
			
	2.显示内连接：
		1).格式：select 字段列表 from 表1 INNER JOIN 表2 ON 等值关系；
		2).例如：查询商品信息，要显示所对应类别信息
			select * from products p inner join category c on p.category_id = c.cid;
		3).练习：查询"市"的所有信息，并且显示对应的"省名"
			SELECT c.cname AS '市',p.pname AS '省' from city c inner join province p on c.pid = p.pid;
	注意：
	1.内连接的查询结果：两个表中的等值记录；
	2.两种内连接都可以再添加其它where条件：
		隐式内连接：select .. from 表1,表2 where 等值条件 and 其它条件...
		显示内连接：select .. from 表1 inner join 表2 on 等值条件 where 其它条件....
	3.两种查询的格式说明：
		隐式内连接：select .. from 表1,表2 on 等值条件//错误
		显示内连接：select .. from 表1 inner join 表2 where 等值条件//OK的
#### 03.多表查询_外连接查询【重点掌握】

	1.左外连接查询：
		1).格式：select 字段列表 from 表1 left join 表2 on 等值关系;
		2).查询结果：左表的所有记录，和右表的等值记录;
		3).例如：需求：查询出所有商品(包括没有类别的商品)，有类别的商品要显示类别名称。
			SELECT * FROM products p LEFT JOIN category c ON p.category_id = c.cid;
	
	2.右外连接查询：
		1).格式：select 字段列表 from 表1 right join 表2 on 等值关系；
		2).查询结果：右表的所有记录，和左表中的等值记录；
		3).例如：需求：查询出所有的商品类别，如果类别下有商品的，要同时显示商品信息；
			SELECT * FROM products p RIGHT JOIN category c ON p.category_id = c.cid;
#### 04.子查询【重点掌握】

	1.在一个查询内部，可以再写一个查询，这个写在内部的查询就叫：子查询；
	2.子查询的结果可以作为另一个查询：判断条件，表使用。
	3.例子：查询价格高于"劲霸"的商品信息；
		SELECT * FROM products WHERE price > (SELECT price FROM products WHERE pname = '劲霸');
	4.练习：
		1).查询化妆品类别的商品信息
		   a).使用多表连接查询：
			select * from products p , category c where p.category_id = c.cid and c.cname = '化妆品';
		   b).使用子查询(单表查询)
			SELECT * FROM products WHERE category_id = (SELECT cid FROM category WHERE cname = '化妆品');
		   c).使用子查询作为第三张表：select * from (子查询)
			SELECT * FROM products p ,(SELECT * FROM category WHERE cname = '化妆品') c WHERE p.category_id = c.cid;
		2).查询所有"家电","服饰"类商品的信息：
			select * from products where category_id = 1 or category_id = 2;
			改进：
			select * from products where category_id in (1,2);
			改进：
			select * from products where category_id in (select cid from category where cname in ('家电','服饰'));


#### 学习目标总结：

1，能够使用内连接进行多表查询
a，	说出内连接的两种查询方式

		1.隐式内连接
		2.显示内连接
b，	写出显式内连接的SQL语句
		select * from products p inner join category c on p.category_id = c.cid;
c，	写出隐式内连接的SQL语句
		select * from products p , category c where p.category_id = c.cid;
2，能够使用外连接进行多表查询
a，	说出外连接的两种查询方式
		1.左外查询
		2.右外查询
b，	写出左外连接的SQL语句
		select * from products p left join category c on p.category_id = c.cid;//所有左表中的记录，和右表的等值记录
c，	写出右外连接的SQL语句
		select * from products p right join category c on p.category_id = c.cid;//所有右表中的记录，和左表的等值记录
3，能够使用子查询进行多表查询
	select * from products where category_id in (select cid from category where cname in ('家电','服饰'));

扩展：三表联查：
	1.隐式内连接：使用user表，role表，user_role表进行测试
		select * from users u , role r,user_role ur where u.uid = ur.uid and ur.rid = r.rid;
	2.显示内连接：
		select * from users u inner join user_role ur on u.uid = ur.uid inner join role r on ur.rid = r.rid;
