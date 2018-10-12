title: oracle01
author: 马文磊
tags:
  - oracle
categories: []
date: 2018-01-13 15:26:00
---
### Oracle

sqlplus   system/system

sqlplus  system/system@192.168.227.10:1521/orcl

### Oracle应用开发实战

#### Oracle基本概念以及安装

##### Oracle简介

##### Oracle10g的安装

##### 虚拟网卡的设置

 sqlplus system/system 本机

![mark](http://ozaomob5f.bkt.clouddn.com/images/180113/B2mkAI5B40.png?imageslim)

DML  数据库操作语言 insert  delete update

DQL  数据库查询语言  select  

DDL  数据库定义语言  create drop

DCL	  数据库控制语言 

#### PLSQL Developer客户端工具的安装

##### 中文乱码问题解决

```plsql
1.查看服务器端编码
select userenv('language') from dual;
	我实际查到的结果为:AMERICAN_AMERICA.ZHS16GBK
2.执行语句 select * from V$NLS_PARAMETERS 
	查看第一行中PARAMETER项中为NLS_LANGUAGE 对应的VALUE项中是否和第一步得到的值一样。
	如果不是，需要设置环境变量.
	否则PLSQL客户端使用的编码和服务器端编码不一致,插入中文时就会出现乱码.
3.设置环境变量
	计算机->属性->高级系统设置->环境变量->新建
	设置变量名:NLS_LANG,变量值:第1步查到的值, 我的是 AMERICAN_AMERICA.ZHS16GBK
4.重新启动PLSQL,插入数据正常 
```

### Oracle数据库的体系结构

#### 数据库: database

Oracle数据库  一个操作系统只有一个库	名字 orcl

#### 实例 Instrance

  一个Oracle实例(Oracle instrance)由一系列的后台进程(Background Process)和内存结构(Memory  Structures)组成  .一个数据库可以有n个实例

#### 数据文件(dbf):

数据库中的数据是存储在表空间的,一个表空间有一个或者多个数据文件组成,一个数据文件只能对应一个表空间. 如果要删除某个数据文件,只能删除其所属的表空间了.

#### 表空间

表空间就是数据文件的逻辑映射,一个数据文件只能属于一个表空间	

#### 用户

   用户实在实例下建立的,用来操作表空间的

----System

---- Scott

select * from scott.emp

 用scoot用户登录   默认密码是tiger

使用sql重置scott密码

```plsql
alter user scott identified by tiger;
```

### 基本查询

#### 1.简单查询

##### 1.简单查询

```plsql
select * from  dept; 
```

##### 2.别名的使用

```plsql
select empno as "员工编号" from emp;

select empno as "员工编号" ,ename as 员工姓名 , job "职位",mgr 领导  from emp ;	
```

#### 2.四则运算

​	+     -     *    /

```plsql
select sal , sal*12 from emp;
select 1+1;   -- mysql  2    oracle  报错,确实from关键字
select 1+1 from dept;   -- 查询结果为多列数据 
-- dual  伪表 --
select 1+1  from  dual;   --   目的就是为了配置查询语句 --
```

#### 3.连接符

```plsql
"员工编号是XXX，姓名是XXX，职位是XXX"
select  empno ,ename , job from emp;
select '员工编号是XXX' || '姓名是XXX' || '职位是XXX' from dual;
--  把xxx替换   用来连接符 || 来代替
select '员工编号是'||empno||'姓名是'||ename||'职位是'||job  from emp;
```

#### 4.去重 distinct

​     在条件上加上个  where后 distinct 

### 条件查询

#### 条件查询where

```plsql
--1、查询工资大于1500并且小于3000的员工
select * from emp where  sal>1500 and sal<3000
--2、between ..and   包含临界值
select * from emp where  sal>=1500 and sal<=3000;
select * from emp where  sal between 1500 and  3000;
--  等于
select * from emp where  sal  = 2450.00;
select * from emp where  sal  in (2450.00,2850.00);
--3、null 不是空字符串也不是0，是未知类型的数据并且 null和null不相等
--查询奖金为空的员工
-- 包含null的表达式都是空值  空值永远不等于空值
select * from emp where comm is null;
--查询奖金不为空的员工
select * from emp where comm is  not  null;
--4、not
--查询工资不大于1500
select * from emp where not(sal>1500);
--5、 in
--查询员工编号是7788,7369,7566
select * from emp where empno in(7788,7369,7566);
--查询员工姓名是  SMITH  JONES  SCOTT
select * from emp where ename in('SMITH',  'JONES',  'SCOTT');
-- 查询一个时  相当于等于
```

#### 模糊查询like

**==注意：Oracle中是大小写敏感==**

```plsql
select * from emp  where ename like '_M%'  --查询员工姓名中第二个字母是M的
select * from emp  where ename like '%M%'  --查询员工姓名中包含字母是M的
-- "_" 通配   "%" 多个
-- 查询员工姓名中带_的
-查询员工姓名中带_的
select * from emp  where ename like '%1_%' escape '1'; 
select * from emp  where ename like '%a_%' escape 'a'; 
select * from emp  where ename like '%#_%' escape '#'; 
select * from emp  where ename like '%@_%' escape '@'; 
select * from emp  where ename like '%__%' escape '_'; 
select * from emp  where ename like '%$_%' escape '$'; 
select * from emp  where ename like '%|_%' escape '|';

--错误的
select * from emp  where ename like '%||_%' escape '||';
select * from emp  where ename like '%12_%' escape '12';
select * from emp  where ename like '%&_%' escape '&'; 
select * from emp  where ename like '%%_%' escape '%'; 
```

#### 排序

```plsql
-- order by    desc   asc
-- 按照奖金从高到低排序
select * from emp order by comm desc nulls last
 -- 按照奖金从低到高排序
select * from emp order by comm  nulls first
```

### 函数

#### 单行函数

##### 字符串类型

```plsql
 -- lower--转小写
 select lower('ORACLE') from dual;
 select ename,lower(ename) from emp;
 
 -- upper--转大小
 select ename,upper(ename) from emp;

-- initcap--首字母大写
 select ename,initcap(ename) from emp;

-- substr--截取
 -- oracle截取时起始位置写0和1是一样的
 select substr('qwertyui',0,4) from dual;--qwer
 select substr('qwertyui',1,4) from dual;--qwer
 select substr('qwertyui',2,4) from dual;--wert
 
 -- length--长度
 select length('qwertyui') from dual;--8
 
 --replace--替换
 select replace('qwertyui','qw','aa') from dual;-- aaertyui
 
 -- concat--连接两个字符串函数  推荐使用||
 select concat('aaa' ,'bbb') from dual;
 select concat(concat(concat('aaa' ,'bbb'),'ccc' ),'ddd')  from dual;
```

##### 数值类型

```plsql
数值函数
round--四舍五入
select round(12.456) from dual; --12
select round(12.656) from dual; --13
select round(12.456,2) from dual;--12.46
select round(12.456,-1) from dual;--10
select round(16.456,-1) from dual;--20

-- trunc--截断   TRUNC()函数截取时不进行四舍五入   
select trunc(12.456) from dual; --12
select trunc(12.956) from dual; --12
select trunc(12.456,2) from dual;--12.45
select trunc(12.456,-1) from dual;--10
select trunc(16.456,-1) from dual;--10  

--mod--取余
select mod(10,3) from dual;--1
select mod(10,2) from dual;--0
```

##### 日期类型

```plsql
select sysdate from dual;

--获取员工入职的天数
select ename,hiredate,sysdate-hiredate from emp;

--获取员工入职的周数
select ename,hiredate,round((sysdate-hiredate)/7)  from emp;
select  ename,hiredate,round((sysdate-hiredate)/7) from emp

-- 获取员工入职的月数  
-- months_between--两个日期的月数差
select  ename,hiredate,months_between(sysdate,hiredate),(sysdate-hiredate)/30 from emp;

-- add_months--添加月
select  sysdate  from dual;
select add_months(sysdate,1) from dual;

-- oracle将毫秒装换为当前时间
SELECT TO_CHAR(1511272758348/(1000 * 60 * 60 * 24) + TO_DATE('1970-01-01 08:00:00', 'YYYY-MM-DD HH:MI:SS'), 'YYYY-MM-DD HH24:MI:SS') AS CDATE      
 FROM DUAL; 

```

##### 转换函数

```plsql
-- 转换函数
-- to_char
select 12,to_char(12) from dual;

-- to_number
select 12,to_number('12') from dual;

select empno as "员工编号" ,ename as 员工姓名 , job "职位",mgr 领导  from emp ;	

-- to_date  字符串转成日期
select to_date('2016-01-31','yyyy-MM-dd')  from dual;

select add_months(to_date('2016-01-31','yyyy-MM-dd'),2) from dual;
-- 结果一样
select * from emp where hiredate = to_date('1981-02-20','yyyy-MM-dd');
select * from emp where hiredate = to_date('1981/02/20','yyyy/MM/dd');


-- 日期转为字符串
-- to_char
select * from emp where to_char(hiredate,'yyyy/MM/dd')= '1981/02/20';
select to_char(sysdate,'yyyy-mm-dd hh24:mi:ss') from dual;
select to_char(sysdate,'yyyy'),to_char(sysdate,'mm'),to_char(sysdate,'dd'),to_char(sysdate,'day') from dual
-- 今年是xxx,xxx月,xxx号,第xxx天,第xxx周
select '今天是xxx年','xxx月','xxx号','今年第xxx天','第xxx周'  from dual;
select '今天是' || to_char(sysdate,'yyyy')  ||  to_char(sysdate,'mm') || '月'  from dual;
```

##### 通用函数

```plsql
-- nvl  当comm为null时参与运算时为0
员工年薪   null参与运算结果恒为null
select ename,sal,comm,sal*12+nvl(comm,0) from emp;
CLERK--职员
SALESMAN--销售员
---MANAGER--经理
--ANALYST--分析师
--PRESIDENT--总裁
decode  --是Oracle独有的
decode(列名,'表中的数据','显示的值','表中的数据','显示的值','表中的数据','显示的值')


select ename,job  from emp;
select ename,job ,
decode(job,
'CLERK','职员',
'SALESMAN','销售员',
'其他'
) from emp


--条件表达式  
--任何关系型数据库都支持
case 列名  when 值  then 显示的值   end

select ename,job ,
   case job 
   when 'CLERK' then '职员'
   when 'SALESMAN' then '销售员'
   else '其他'
    end 
 from emp
```

#### 多行函数

```plsql
-- 多行函数  聚合函数  组函数
-- sum  avg  count max min
select avg(sal) from emp;

select max(sal)- min(sal) from emp;

select count(*) from emp;

select sum(sal) from emp;
 
-- 分组
group by
--	查询每个部门的平均工资
select deptno,max(sal) from emp group by deptno;

select deptno,avg(sal) from emp group by deptno;

--	查询部门的平均工资大于2000的部门
select deptno,avg(sal) from emp group by deptno;
having avg(sal)>2000 

--where 和having的区别
--where过滤的是表中的物理列（表中存在的列）
--where出现在group by前面  having出现在group by的后面
```

### 练习

```plsql
1. 查询工资大于12000的员工姓名和工资

select * from employees;

select  first_name|| ' '||last_name ,salary from  employees where salary>12000;


2. 查询员工号为176的员工的姓名和部门号
select first_name|| ' '||last_name,department_id  from employees;


3. 选择工资不在5000到12000的员工的姓名和工资
select first_name|| ' '||last_name as "姓名",salary as "工资"  from employees where salary<5000 or salary>12000;


4. 选择雇用时间在1998-02-01到1998-05-01之间的员工姓名，job_id和雇用时间
select job_id as id ,first_name|| ' '||last_name as "姓名",round((sysdate - hire_date)) as "天"   from employees;


5. 选择在20或50号部门工作的员工姓名和部门号
select first_name|| ' '||last_name as "姓名", department_id as "部门编号" from  employees where  department_id in(20,50) ;


6. 选择在1994年雇用的员工的姓名和雇用时间
select first_name|| ' '||last_name as "姓名",round((sysdate - hire_date)) as "天"  from employees where  
to_char(hire_date,'yyyy') = '1994';


7. 选择公司中没有管理者的员工姓名及job_id 
select first_name|| ' '||last_name as "姓名", job_id as "ID",manager_id  from employees  where manager_id is null;


8. 选择公司中有奖金的员工姓名，工资和奖金级别
select first_name|| ' '||last_name as "姓名",salary as "工资" ,commission_pct as "奖金级别"  from employees
 where commission_pct is not null;


9. 选择员工姓名的第三个字母是a的员工姓名

select first_name  from employees  where first_name like  '__a%' ;

10. 选择姓名中有字母a和e的员工姓名
select first_name from employees where  first_name  like  '%a%' and  first_name  like '%e%';
 
select first_name|| ' '||last_name from employees  where   first_name|| ' '||last_name  like  '%a%' and  
  first_name|| ' '||last_name  like '%e%';


11. 显示系统时间
select sysdate from dual;

12. 查询员工号，姓名，工资，以及工资提高百分之20%后的结果（new salary）
select  employee_id,first_name|| ' '||last_name 姓名,salary  工资, salary*1.2  from employees;


13. 将员工的姓名按首字母排序，并写出姓名的长度（length）
select  first_name|| ' '||last_name 姓名 ,length(first_name|| ' '||last_name ) from employees 
order by  first_name|| ' '||last_name  asc;

14. 查询各员工的姓名，并显示出各员工在公司工作的月份数
select  first_name|| ' '||last_name 姓名,months_between(sysdate,hire_date) "工作的月份数"  from employees;


15. 查询员工的姓名，以及在公司工作的月份数（worked_month），并按月份数降序排列
select  first_name|| ' '||last_name 姓名 ,months_between(sysdate,hire_date) "工作的月份数"  from employees
 order by months_between(sysdate,hire_date) desc;

```

