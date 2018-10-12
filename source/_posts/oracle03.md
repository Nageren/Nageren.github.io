---
title: oracle03
date: 2018-02-05 10:57:36
tags: [oracle,sql]
---

### Oracle03

```plsql
select instance_name from v$instance;    -- 查询 sid
```

count问题

```plsql
 select count(*)  from emp ;
 select count(empno) from emp;   -- 指定了count的列   主键列  有索引
 select count(ename) from emp;   -- 指定了count的列	 普通列
 select count(0) from emp;		 -- 先找主键
 select count(1) from emp; 		 -- 先找主键
 select count(5454515) from emp;  -- 先找主键
 
 select count(comm)  from emp;   -- 4  自动跳过null值
```



| 步骤   | mysql                         | Oracle           |
| ---- | ----------------------------- | ---------------- |
| 1    | 创建数据库 create  database mingzi | 创建表空间同时创建 .dbf文件 |
| 2    | 创建表                           | 创建用户,指定操作的空间     |
| 3    | 操作数据                          | 赋权限              |
| 4    | 操作数据                          | 创建表              |
| 5    | 操作数据                          | 操作数据             |

#### 1.创建表空间用户

当前用户是管理员 ,才有权限创建用户 

```plsql
-- 1.1创建表空间同时创建dbf文件
create tablespace heima_68_space   --表空间的名字
datafile 'c:\\heima_68.dbf'        --数据文件
size 10M                           --指定数据文件的大小
autoextend on                      --数据文件自动增长
next 1M                            --增长的大小

select  *  from  session_privs ;   -- 查看当前用户的权限

--  1.2创建用户，指定操作的表空间
create user	  heima_68     -- 用户名
identified  by  heima_68    -- 密码
default  tablespace  heima_68_space ;  -- 指定操作的表空间

-- 1.3赋权限
select  *  from  session_privs ;   -- 查看当前用户的权限
-- 权限  三种角色
dba
resource
connect
-- 授权
grant   dba to  heima_68 ; 
```

#### 2.Oracle 常见的数据类型

```plsql
--2.1字符型  char varchar2
char        255      固定长度     name  char(10)  'TOM'
varchar2   3999     不固定长度    name  varchar2  'TOM'

-- 2.2数值型  number 
id   number(3)   999
aa   number(3,2)  9.99    总长度  不包括小数点和负号


-- 2.3日期型
date 相当于mysql的datetime
timestamp  时间戳  精度支持秒小数点后9位

-- 2.4 大数据类型
long  相当于mysql longtext  支持2个G内存
clob  支持4G
blob  存放视频,图片等    
```

#### 3.创建表

```plsql
-- 3.1约束   主键  非空  唯一 检查 外键
drop table person;
create  ------ drop

create table person(
 pid number(10) ,
 pname varchar2(30) not null,
 telephone varchar2(11),
 gender char(1),     --  0女  1男
 constraint person_pk_pid primary key(pid),
 constraint person_unique_telephone unique(telephone),
 constraint person_check_gender check(gender in(0,1))
)

insert into person  values(1,'TOM','13898765432','1');
insert into person  values(2,'JERREY','13898765431','1');
insert into person  values(3,'JERREY','13898765433','0');
insert into person  values(4,null,'13898765434','0');


select * from person

create table orders(
 ooid number(10) primary key,
 totalPrice number(10,2)
)
create table orderdetail(
 odid number(10) primary key,
 goodsName varchar2(30),
 ooid number(10),
 constraint orderdetail_fk_ooid foreign key(ooid) references orders(ooid)
)

insert into orders values(1,1000);
insert into orders values(2,5000);

select * from orders

insert into orderdetail values(1,'鼠标',1);
insert into orderdetail values(2,'键盘',2);
delete from orderdetail where ooid=1
delete from orders where ooid=1

-- 3.2 修改表（了解）
alter table person add (address varchar2(10))--添加列
alter table person modify(address varchar2(100)) --修改列
alter table person drop column address  --删除列




-- 3.3 操作数据
drop table person;
create  ------ drop

create table person(
 pid number(10) ,
 pname varchar2(30) not null,
 telephone varchar2(11),
 gender char(1),     --  0女  1男
 birthday date,
 constraint person_pk_pid primary key(pid),
 constraint person_unique_telephone unique(telephone),
 constraint person_check_gender check(gender in(0,1))
)
insert into person values(1,'TOM','12345678909','1',to_date('2008-08-08','yyyy-mm-dd'))


---- 快速复制表
create table myemp as (select * from scott.emp)
create table mydept as (select * from scott.dept)

--  给纽约NEW YORK地区的员工涨100元工资
select * from myemp
select * from mydept

select * from myemp where deptno in (select deptno from mydept where loc='NEW YORK')

  update myemp set sal=sal+100 where deptno in (select deptno from mydept where loc='NEW YORK')
```

#### 4.事务的保存点savepoint

```plsql
select * from  person

update person set pname='大郎' where pid=1;
savepoint a;
insert into person values(2,'小潘','12345432123','0',null);
savepoint b;
delete from person where pid=2;
rollback to a;

rollback to b;

rollback to a

rollback;

场景
用户注册
 1、insert  user
 2、发送短信 insert 短信记录表
 
 insert  user
 savepoint a;
 insert log
```

#### 5.事务的隔离级别

```sql
（1）Read uncommitted 读未提交
	公司发工资了，领导把5000元打到singo的账号上，但是该事务并未提交，而singo正好去查看账户，发现工资已经到账，是5000元整，非常高兴。
	可是不幸的是，领导发现发给singo的工资金额不对，是2000元，于是迅速回滚了事务，修改金额后，将事务提交，最后singo实际的工资只有2000元，
	singo空欢喜一场。
	
	总结：出现上述情况，即我们所说的脏读，两个并发的事务，“事务A：领导给singo发工资”、“事务B：singo查询工资账户”，事务B读取了事务A尚未提交的数据。

	当隔离级别设置为Read uncommitted时，就可能出现脏读，如何避免脏读，请看下一个隔离级别。
 （2）Read committed 读提已交

	singo拿着工资卡去消费，系统读取到卡里确实有2000元，而此时她的老婆也正好在网上转账，把singo工资卡的2000元转到另一账户，
	并在singo之前提交了事务，当singo扣款时，系统检查到singo的工资卡已经没有钱，扣款失败，singo十分纳闷，明明卡里有钱，为何......

	总结：出现上述情况，即我们所说的不可重复读，两个并发的事务，“事务A：singo消费”、“事务B：singo的老婆网上转账”，事务A事先读取了数据，事务B紧接了更新了数据，并提交了事务，而事务A再次读取该数据时，数据已经发生了改变。

	当隔离级别设置为Read committed时，避免了脏读，但是可能会造成不可重复读。

	大多数数据库的默认级别就是Read committed，比如Sql Server , Oracle。如何解决不可重复读这一问题，请看下一个隔离级别。

  (3) Repeatable read 重复读

	singo的老婆工作在银行部门，她时常通过银行内部系统查看singo的信用卡消费记录。
	有一天，她正在查询到singo当月信用卡的总消费金额（select sum(amount) from transaction where month = 本月）为80元，
	而singo此时正好在外面胡吃海塞后在收银台买单，消费1000元，即新增了一条1000元的消费记录（insert transaction ... ），
	并提交了事务，随后singo的老婆将singo当月信用卡消费的明细打印到A4纸上，却发现消费总额为1080元，singo的老婆很诧异，
	以为出现了幻觉，幻读就这样产生了。
	
	总结：当隔离级别设置为Repeatable read时，可以避免不可重复读。当singo拿着工资卡去消费时，一旦系统开始读取工资卡信息（即事务开始），singo的老婆就不可能对该记录进行修改，也就是singo的老婆不能在此时转账。

	虽然Repeatable read避免了不可重复读，但还有可能出现幻读。


   （4）Serializable 序列化
	Serializable是最高的事务隔离级别，同时代价也花费最高，性能很低，一般很少使用，在该级别下，事务顺序执行，不仅可以避免脏读、不可重复读，还避免了幻像读。	




√: 可能出现    ×: 不会出现

		      	   脏读  不可重复读  幻读
Read uncommitted	√	   	√		√
Read committed		×		√	    √
Repeatable read		×		×		√
Serializable		×		×		×
```

#### 6.数据库的其他对象

##### 视图view

​	概念: 是个虚表,可以把视图当作表来操作

​	作用|好处: 

​	1.可以封装复杂的sql

```plsql
create view view_hireCount as
select    
  sum(t.counts) "Total" ,
  sum(decode(years,'1980',counts)) "1980" ,
  sum(decode(years,'1981',counts)) "1981" ,
  sum(decode(years,'1982',counts)) "1982" ,
  sum(decode(years,'1987',counts)) "1987" 
 from  (select   to_char(hiredate,'yyyy') years  ,count(*)  counts  from emp  group by to_char(hiredate,'yyyy'))  t ;


select *  from view_hireCount;
```

​	2.可以隐藏敏感列

```plsql
create view view_emp as select empno,ename,job,mgr,hiredate,deptno  from  myemp ;

select * from view_emp;
update view_emp2 set ename='bbbb' where empno =7369;
select * from myemp;

--只读视图  with  read only
create view view_emp2 as select empno,ename,job,mgr,hiredate,deptno  from myemp with  read only ;
select * from view_emp;
update view_emp2 set ename='bbbb' where empno =7369;
select * from myemp2;
```

##### 序列sequence

​	概念:  独立于表之外的对象,主键自增

​	作用: 主要用来做主键自增;

```plsql
--序列的简单语法

--使用序列
create  sequence seq_person ;

--第一次使用,只能使用seq_person.nextval 
select seq_person.nextval from dual;
select seq_person.currval from dual;

select * from person;
insert into person values(seq_person.nextval,'小明','1232','0','null')

--序列的复杂语法
5 7 9 11 13 15 3 5 7 9
create  sequence sel_test 
increament by 2 -- 递增值,默认是1
minvalue  3 -- 最小值,默认是1
maxvalue --15 --最大值  默认是19个9   
start width 5 --起始值 默认是1
cycle  --循环   默认nocycle
cache 10 --缓存
```

##### 索引 index

​	概念: 相当于一本数的目录

​	作用|好处: 为了提高查询效率

​	主键和唯一约束默认就会创建索引

​	怎么用

```plsql
create index index_emp_ename on myemp(ename);

select *  from myemp where ename = 'SCOTT';

1.创建一个表
create table t_test(
	tid number(10),
  	tname varchar2(20)
);
2.插入500万条数据
begin 
  for i  in 1..5000000
    loop 
  	 	insert into t_test  values(i,'测试数据'||i )
  	end loop;
  	commit;
end;

3.查询记录时间
select * form t_test where tname='测试数据566899686';

4.创建索引
create index index_test on t_test(tname);

5.查询 记录时间(索引)
select * form t_test where tname='测试数据56615186';

```

##### 提高查询效率(面试)

1. 创建索引
2. 子查询  (笛卡尔积)
3. 把经常用的多表数据放在一张表中 (创建)

##### 同义词 synonym

概念

作用: 缩短访问对象的名称

```plsql
create synonym e from scott.emp;

create synonym  e  from scott.emp;

select * from e;
drop synonym e;   -- drop 自己的
drop public synonym e;   

```

#### 7.数据的导入导出

1. 全部导出

   ...

2. 按照用户导出(重点)

   ```plsql
   1、全部导出
   2、按照用户导出
   注意 : exp和 imp 使用命令的前提是当前电脑上安装了Oracle
   按照用户导出

   把scott用户下的所有对象导入到heima_68用户
   导出:exp scott/tiger@192.168.227.10:1521/orcl file=c:\\scott20171018.dmp owner = scott;

   导入:imp heima_68/heima_68@192.168.66.10:1521/orcl  file=c:\\scott20171018.dmp full=y
   3、导出某表
   ```

3. 导出某表

   ....

### 小结

1. 创建表空间用户 ==保存语句==

   ```plsql
   create tablespace heima_68_space   --表空间的名字
   datafile 'c:\\heima_68.dbf'        --数据文件
   size 10M                           --指定数据文件的大小
   autoextend on                      --数据文件自动增长
   next 1M                            --增长的大小
   ```

```
​```plsql
select  *  from  session_privs ;   -- 查看当前用户的权限

create user heima_68    --用户名
identified  by heima_68  -- 密码
default  tablespace heima_68_space   -- 指定表空间
​```
```

1. 创建表

   ​     数据类型  ==重点==

   ​     创建表     ==重点==

   ​     修改表结构  了解

2. 操作数据  ==重点==

   insert  

   delete

    update

3. 事务保存点 了解

4. 视图  ==重点==

5. 序列 ==重点==

6. 索引==重点Oracle独有==

7. 同义词  了解

8. 导入导出 ==重点==   ==保存语句==

   注意 : exp和 imp 使用命令的前提是当前电脑上安装了Oracle

   ```plsql
   把scott用户下的所有对象导入到heima_68用户

   导出:exp scott/tiger@192.168.227.10:1521/orcl file=c:\\scott20171018.dmp owner = scott;

   导入:imp heima_68/heima_68@192.168.66.10:1521/orcl  file=c:\\scott20171018.dmp full=y
   ```

   ​

