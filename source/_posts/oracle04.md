---
title: oracle04
date: 2018-02-05 10:57:41
tags: [oracle,sql]
---

### Oracle第四天

#### PL/SQL编程语言

##### 什么是pl/sql语言

​	PLSQL是Oracle对sql语言的过程化扩展,指在sql命令语言中增加了过程处理语句(如分支,循环等),使

sql语言具有过程处理能力. 面向过程

##### PL/SQL的语法

```plsql
[declare]
	--声明   变量（普通变量、引用型变量、记录型变量） 常量  异常  游标
begin 
	-- 语句序列 (DML语句)
	[exception]
	-- 捕获异常
end;
/
```

##### 常量和变量的定义

在sql中用 into来赋值

说明变量(char,varchar2,date,number,boolean,long)

```plsql
declare
    v_name varchar2(30) := 'Tom' ;  --  := 相当于java中的 =, = 相当于java中的== 
    v_age number(9) :=1 ; 
    v_gender constant number(1) := 1;  -- 常量
    v_sal emp.sal%type := 1000;    -- 引用型  
    v_row  emp%rowtype ;   -- 记录型   一行数据
begin 
    v_name:='JERRY'; 
    --v_gender:='0';   -- 常量不可改变 报错

     select ename,sal into v_name,v_sal from emp where empno=7369;
     select * into v_row from emp where empno=7369;
  	 --dbms_output.put_line(v_name||'--'||v_sal||'——'||v_gender);
     dbms_output.put_line(v_row.ename||'--'||v_row.sal||'——'||v_row.job);
end;
```

##### IF语句

语法一：
​	if  条件  then
 		 逻辑处理
​	end if;

语法二：
​	if 条件 then
  		逻辑处理
 	else
​		 逻辑处理
​	end if;

语法三：
​	if 条件 then
 		 逻辑处理
​	elsif 条件
 		 逻辑处理
​		 .......   

​	 else
 		逻辑处理
​	end if;  

if语句 : 

```plsql
-- 输出我是1
declare 
   pnum number := &num;
begin
   if pnum = 1 then 
  dbms_output.put_line('我是1');
  end if;
end;

-- 条件判断
declare 
  mynum number := &num;
begin
  if  mynum = 1 then 
   dbms_output.put_line('我是1');
  else
   dbms_output.put_line('我不是1');
  end if;
end;

-- 未成年人,成年人,老年人
declare 
   age number(3) :=&nums;   --  &表示指向一个地址,弹出一个输入框
begin 
  if age < 18 then
   dbms_output.put_line('未成年');
  elsif age >= 18 and  age <= 60 then
    dbms_output.put_line('中年人');
  else
    dbms_output.put_line('老人');
  end if;
end;
```

##### 循环

###### 1.语法1:无条件循环,有条件退出

​	loop 

​	end  loop;

###### 2.语法2 :条件循环while

​	while 条件
  	     loop
​	
​             end loop;

###### 3.语法3:条件循环for

for 变量名 in 起始值..终止值
   loop

   end loop;

```plsql
-- 语法1:无条件循环,有条件退出
--输出1到100个数
declare 
   v_num number(9) := 1;
begin
   loop 
     --if v_num > 100 then
     --  exit;
     --end if;
     exit when v_num >100;
       dbms_output.put_line(v_num);
       v_num :=v_num +1 ;  -- 不支持 ++  -- 
   end loop;
end;

语法2 :有条件循环 条件循环while
-- 输出1到100个数
declare
  v_num number(9) := 1;
begin
  while v_num <= 100
  loop
     dbms_output.put_line(v_num);
     v_num :=v_num +1 ;
  end loop;
end;


语法3:  条件循环for
declare
  v_num number(9) := 1;
begin
  for v_num in 1..100
  loop
     dbms_output.put_line(v_num);
  end loop;
end;
```

##### 异常 exception

异常的作用: 为了提高语言的容错性和健壮性

no_data_found 没有找到数据

too_many_rows  select....into语句匹配多个行

zero_divide  除数为0

value_error 算术或转换错误

timeout_on_resourse 在等待资源时发生超时__

**命名的系统异常** 

| **命名的系统异常**             | **产生原因**                                 |
| ----------------------- | ---------------------------------------- |
| ACCESS_INTO_NULL        | 未定义对象                                    |
| CASE_NOT_FOUND          | CASE 中若未包含相应的 WHEN ，并且没有设置 ELSE 时        |
| COLLECTION_IS_NULL      | 集合元素未初始化                                 |
| CURSER_ALREADY_OPEN     | 游标已经打开                                   |
| DUP_VAL_ON_INDEX        | 唯一索引对应的列上有重复的值                           |
| INVALID_CURSOR          | 在不合法的游标上进行操作                             |
| INVALID_NUMBER          | 内嵌的 SQL 语句不能将字符转换为数字                     |
| **NO_DATA_FOUND**       | 使用 select into 未返回行，或应用索引表未初始化的元素时       |
| **TOO_MANY_ROWS**       | 执行 select into 时，结果集超过一行                 |
| **ZERO_DIVIDE**         | 除数为 0                                    |
| SUBSCRIPT_BEYOND_COUNT  | 元素下标超过嵌套表或 VARRAY 的最大值                   |
| SUBSCRIPT_OUTSIDE_LIMIT | 使用嵌套表或 VARRAY 时，将下标指定为负数                 |
| **VALUE_ERROR**         | 赋值时，变量长度不足以容纳实际数据                        |
| LOGIN_DENIED            | PL/SQL 应用程序连接到 oracle 数据库时，提供了不正确的用户名或密码 |
| NOT_LOGGED_ON           | PL/SQL 应用程序在没有连接 oralce 数据库的情况下访问数据      |
| PROGRAM_ERROR           | PL/SQL 内部问题，可能需要重装数据字典＆ pl./SQL 系统包      |
| ROWTYPE_MISMATCH        | 宿主游标变量与 PL/SQL 游标变量的返回类型不兼容              |
| SELF_IS_NULL            | 使用对象类型时，在 null 对象上调用对象方法                 |
| STORAGE_ERROR           | 运行 PL/SQL 时，超出内存空间                       |
| SYS_INVALID_ID          | 无效的 ROWID 字符串                            |
| TIMEOUT_ON_RESOURCE     | Oracle 在等待资源时超时                          |

###### 预定义 异常

```plsql
--预定义异常
-- 案列 1:
declare
 v_num number(5);
begin
    --v_num:=10/0;  
    v_num:=1000000;  
   exception
     --VALUE_ERROR
     --ZERO_DIVIDE
     when  others then
     v_num:=0;  
     dbms_output.put_line(v_num);   
end;


-- 案列 2:
declare
 v_num number;
begin
    v_num:=1/0;
   exception
     when ZERO_DIVIDE  then
        dbms_output.put_line("除0异常!!"); 
     when VALUE_ERROR  then  
          dbms_output.put_line("数值转换错误!!"); 
     when others  then
        dbms_output.put_line("其他错误!!"); 
end;
```

###### 自定义异常

```plsql
--自定义异常

-- 输入一个年龄数据,大于150就抛出异常
declare 
   v_age number(10) := &nums;
   exc_age exception;
begin
  if v_age > 150 then
      raise exc_age ; 
  end if;  
  exception
    when exc_age then 
     ---p1 错误代码    p2 错误信息
     raise_application_error(-20001,'捕获了异常');
     --v_age:=150;
     -- dbms_output.put_line('捕获了异常，年龄重置成了150');    
end;
```

##### 游标(光标Cursor)

概念: 就是用来接收多条数据的

`需求:按员工的工种涨工资,总裁涨1000,经理涨800,其他人400`

游标的声明语法:

```plsql
cursor 游标名   is    sql查询语句 ;
cursor  c1  is  select  ename  from  emp;    -- 查询多条记录
```

###### 使用游标的语法:

```plsql
open  游标名称
	loop
		exit when 游标名称%notfound;
		fetch 游标名称  into 记录型变量;
		逻辑处理
	end loop;
close 游标名称; 
```

###### 范列

```plsql
-- 范列1: 使用游标方式输出emp中的员工编号和姓名
declare
 cursor cursor_emp is select * from emp;
 v_row emp%rowtype;
begin
   open   cursor_emp;    -- 打开游标,执行查询
   loop
     fetch cursor_emp into v_row;     -- 记录型变量  接收一行数据
     exit when cursor_emp%notfound;   -- 取一行到变量中
     dbms_output.put_line(v_row.ename||'---'||v_row.job); 
   end loop;
  close cursor_emp;  -- 关闭游标
end;


-- 范列2: 查询20号和40号部门的所有人的name和job
--游标传参数 
declare 
 cursor cursor_emp(d1 number ,d2 number) is select * from emp where deptno =d1 or  deptno =d2;
 v_row emp%rowtype;
begin
  open cursor_emp(20,30);
  loop
    fetch  cursor_emp into  v_row;
    exit when cursor_emp%notfound ;
    dbms_output.put_line(v_row.ename||'*********'||v_row.job);
  end loop;
  close cursor_emp;
end;


declare 
 cursor cursor_emp(d1 number ,d2 number) is select * from emp where deptno in (d1,d2);
 v_row emp%rowtype;
begin
  open cursor_emp(20,30);
  loop
    fetch  cursor_emp into  v_row;
    exit when cursor_emp%notfound ;
    dbms_output.put_line(v_row.ename||'*********'||v_row.job);
  end loop;
  close cursor_emp;
end;

-- 范列3: 写一段PL/SQL程序,为部门号为10员工涨工资
select * from emp where deptno = 10;
declare 
   cursor cursor_emp(d1 emp.deptno%type)  is select empno from emp where deptno = d1;
   v_row emp.empno%type;
begin
   open cursor_emp(10);  -- 开始查询
   loop
     fetch cursor_emp into v_row;
     exit when cursor_emp%notfound;
     update emp set sal = sal +100 where empno = v_row; 
   end loop;
   close cursor_emp;
end;
```

#### 存储过程

##### 概念:

​	存储过程(Stroed Procedure)是在大型数据库系统中,一组为了完成特定功能的sql语句集,经编译后,存储在数据库中,用户通过指定存储过程的名字并给出参数(如有参数)来执行它.存储过程是数据库中的一个重要对象,任何一个设计良好的数据库应用程序都应该用到存储过程.

简单说: 就是一段被命名化的plsql   **预编译到数据库中**  用户指定存储过程的名字并给出参数(如有参数)来执行它

##### 创建存储过程的语法:

```plsql
create [or replace]  procedure  存储过程名称([参数1 [in]/out 数据类型 ,[参数2 [in]/out 数据类型],...........)
is|as -- 代替了declare 如果没有变量声明时不能省略
begin
   plsql子程序体
end;                                
```

##### 存储过程的使用

```plsql
--打印指定员工的年薪   
-- 参数中的in 可以省略   out不能省略
create or replace procedure pro_yealsal(empno1 number)
is    -- 代替了declare
  v_yearsal number(10,2);
begin 
  select sal*12+nvl(comm,0) into v_yearsal from emp where empno =empno1;
  dbms_output.put_line('年薪是：'||v_yearsal);
end;
-- 调用方式1:
call pro_yealsal(7369);
-- 调用方式2:
begin
   pro_yealsal(7499);
end;



-- 定义了  emp.empno%type  emp.sal%type
create or replace procedure pro_yealsal(empno1  emp.empno%type)
  is    -- 代替了declare
    v_yearsal emp.sal%type;
  begin 
    select sal*12+nvl(comm,0) into v_yearsal from emp where empno =empno1;
    
    dbms_output.put_line('年薪是：'||v_yearsal);
  end;
 -- 调用方式1:
  call pro_yealsal(7369);
  -- 调用方式2:
  begin
     pro_yealsal(7499);
  end;
  
  
--使用out参数接收指定员工的年薪
create or replace procedure pro_yealsal2(empno1 number,v_yearsal out number)
is
begin
  select sal*12+nvl(comm,0) into v_yearsal from emp where empno = empno1;       
end;
--使用带out类型参数的存储过程
declare
  yearsal number(10,2); 
begin 
  pro_yealsal2(7369,yearsal);
  dbms_output.put_line('年薪是：'||yearsal);
end;
```

#### 存储函数

##### 语法:

```plsql
create or replace function 存储函数的名称(name in type,name out type .......)
return  数据类型
is|as 
begin 
	return 具体的值;
end[函数名称];
```

##### 存储函数的使用

```plsql
--  返回指定员工的年薪 empno
create or replace function  get_yearsal(empno3 number)
return number
is
   yearsal number(10,2);
begin
  select  sal*12 +nvl(comm,0) into  yearsal from emp where empno = empno3;
  return yearsal;
end;

--使用存储函数
declare
  year_sal number(10,2);
begin
  year_sal := get_yearsal(7369);
  dbms_output.put_line('年薪是'||year_sal);
end;


begin
  dbms_output.put_line( get_yearsal(7369) );
end;
```

##### ==存储函数和存储过程的区别==

1.语法不通

2.使用场景不通

   存储函数一般多被存储过程调用

   存储过程多用于项目之间的数据访问

3.sql中可以直接使用存储函数,但是不能直接使用存储过程

```plsql
-- sql中可以直接使用存储函数
select ename,func_yealsal(empno)  from emp;
```

存储过程和存储函数的区别在于函数可以有一个返回值;而过程没有返回值,但过程和函数都可以通过out指定一个或多个输出参数.我们可以在利用out参数,在过程和函数中实现多个返回值.

```plsql
-- out参数类型是游标的存储过程
-- 场景：游标中放的是指定部门的员工
create or replace procedure proc_cursor(d1 number,cursor_emp out sys_refcursor)
is 
begin
      open cursor_emp from select * form emp where deptno = d1;
end;
```

#### Java程序调用存储过程

##### JDBC连接Oracle

###### 1.java链接oracle的jar包

​	可以在虚拟机的 C:\oracle\product\10.2.0\ db_1 \ jdbc\lib :   ojbbc14.jar

###### 2.数据库的连接

BaseDao.classs

```java
package com.itcast.dao;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class BaseDao {
	/**
	 * 加载驱动
	 */
	static{
		try {
			Class.forName("oracle.jdbc.driver.OracleDriver");
		} catch (ClassNotFoundException e) {
			e.printStackTrace();
		}
	}
	
	/**
	 * 获取连接
	 * @return
	 * @throws SQLException
	 */
	public static Connection getConn() throws SQLException{
		String url="jdbc:oracle:thin:@192.168.227.10:1521:orcl";
		String user="heima_68";
		String password="heima_68";
		return DriverManager.getConnection(url, user, password);
	}
	
	public static void closeAll(ResultSet rs,Statement stmt,Connection conn){
		if(rs!=null){
			try {
				rs.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
		if(stmt!=null){
			try {
				stmt.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
		if(conn!=null){
			try {
				conn.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
	}
}
```

EmpDao.class

```java
package com.itcast.dao;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class EmpDao {
	
	/**
	 * 输出指定部门的员工信息
	 * @param deptno
	 */
	public static void getEmp(Long deptno){
		Connection conn =null;
		PreparedStatement pst = null;
		ResultSet rs = null;
		try {
			conn = BaseDao.getConn();
			String sql = "select * from emp where deptno=?";
			pst =conn.prepareStatement(sql);
			pst.setLong(1, deptno);
			rs  = pst.executeQuery();
			while(rs.next()){
				System.out.println(rs.getLong(1) + "---" + rs.getString("ENAME"));
			}
		} catch (SQLException e) {
			e.printStackTrace();
		}
	}
}
```

###### 3.测试连接

```java
package com.itcast.dao;

public class Test {
	public static void main(String[] args) {
		EmpDao.getEmp(10L);
	}
}
```

##### Java调用存储过程

###### 1.定义好的存储过程:

```plsql
--使用out参数接收指定员工的年薪
create or replace procedure pro_yealsal2(empno1 number,v_yearsal out number)
is
begin
  select sal*12+nvl(comm,0) into v_yearsal from emp where empno = empno1;       
end;
--使用带out类型参数的存储过程
declare
  yearsal number(10,2); 
begin 
  pro_yealsal2(7369,yearsal);
  dbms_output.put_line('年薪是：'||yearsal);
end;


declare
v_sal number(10,2);
begin
  pro_yearsal(7369,v_sal);
  dbms_output.put_line(v_sal);
end;
```

###### 2.java调用

```java
package com.itcast.dao;
import java.sql.CallableStatement;
import java.sql.Connection;
import java.sql.SQLException;

import oracle.jdbc.driver.OracleTypes;
public class ProcedureDao {
	/**
	 *  获取指定部门员工的年薪
	 *  
	 *  {?= call <procedure-name>[(<arg1>,<arg2>, ...)]}   // 给存储函数使用
   	 *  {call <procedure-name>[(<arg1>,<arg2>, ...)]}   // 给存储过程使用
	 *  
	 */
	public static Long getYearSal(Long deptno){
		Connection conn = null;
		CallableStatement cstm = null;    // CallableStatement用于执行 SQL 存储过程的接口
		Long yearSal = null;
		
		try {
			conn = BaseDao.getConn();
			cstm = conn.prepareCall("call pro_yealsal2(?,?)"); // 调用存储过程 
			cstm.setLong(1, deptno);
			// 第二个 ? 是需要指定参数类型即可
			cstm.registerOutParameter(2,OracleTypes.NUMBER);
			cstm.execute();
			yearSal = cstm.getLong(2);
		} catch (SQLException e) {
			e.printStackTrace();
		}
		return yearSal;
	}
} 

```

###### 3.测试

```java
package com.itcast.dao;

public class Test {
	public static void main(String[] args) {
		//EmpDao.getEmp(10L);
		Long yearSal = ProcedureDao.getYearSal(7934L);
		System.out.println("年薪 :   " + yearSal);
	}
}
```

#### Java程序调用存储函数

##### 1.准备好的存储函数

```plsql
--  返回指定员工的年薪 empno
create or replace function  get_yearsal(empno3 number)
return number
is
   yearsal number(10,2);
begin
  select  sal*12 +nvl(comm,0) into  yearsal from emp where empno = empno3;
  return yearsal;
end;

--使用存储函数
declare
  year_sal number(10,2);
begin
  year_sal := get_yearsal(7369);
  dbms_output.put_line('年薪是'||year_sal);
end;

begin
  dbms_output.put_line( get_yearsal(7369) );
end;
```

##### 2.调用

```sql
package com.itcast.dao;
import java.sql.CallableStatement;
import java.sql.Connection;
import java.sql.SQLException;
import oracle.jdbc.driver.OracleTypes;
public class FunctionDao {
	/**
	 *  获取指定部门员工的年薪
	 *  
	 *  {?= call <procedure-name>[(<arg1>,<arg2>, ...)]}   // 给存储函数使用
	 *	{call <procedure-name>[(<arg1>,<arg2>, ...)]}   // 给存储过程使用
	 * 
	 *  存储函数:
	 *  create or replace function  get_yearsal(empno3 number)
	 */
	public static Long getYearSal(Long deptno){
		Connection conn = null;
		CallableStatement cstm = null;    // CallableStatement用于执行 SQL 存储过程的接口
		Long yearSal = null;
		try {
			conn = BaseDao.getConn();
			cstm = conn.prepareCall("{?=call get_yearsal(?)}");
			cstm.setLong(2, deptno);
			cstm.registerOutParameter(1, OracleTypes.NUMBER);
			cstm.execute();
			yearSal = cstm.getLong(1);
		} catch (SQLException e) {
			e.printStackTrace();
		}
		
		return  yearSal;
	}
}
```

##### 3.测试

```java
package com.itcast.dao;

public class Test {
	public static void main(String[] args) {
		//EmpDao.getEmp(10L);
		
		//Long yearSal = ProcedureDao.getYearSal(7934L);
		//System.out.println("年薪 :   " + yearSal);
		
		Long yearSal2 = FunctionDao.getYearSal(7934L);
		System.out.println("年薪 :   " + yearSal2);
		
		//Cursor.getEmp(10L);
		
	}
}
```

#### Java程序调用out类型游标 存储过程

##### 1.创建游标类型的存储过程

```plsql
-- out参数类型是 游标的存储过程
-- 场景：游标中放的是指定部门的员工   deptno     sys_refcursor 系统引用型游标
create or replace procedure proc_cursor(d1 number,cursor_emp out sys_refcursor)
is 
begin
     open cursor_emp for select * FROM  emp where deptno = d1;
end;
```

##### 2.调用

```java
package com.itcast.dao;
import java.sql.CallableStatement;
import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import oracle.jdbc.driver.OracleCallableStatement;
import oracle.jdbc.driver.OracleTypes;
public class Cursor {
	/**
	 *  获取指定部门员工的年薪
	 *  
	 *  {?= call <procedure-name>[(<arg1>,<arg2>, ...)]}   // 给存储函数使用
   		{call <procedure-name>[(<arg1>,<arg2>, ...)]}   // 给存储过程使用

	 *  
	 */
	public static void getEmp(Long deptno){
		Connection conn = null;
		CallableStatement cstm = null;    // CallableStatement用于执行 SQL 存储过程的接口
		ResultSet rs = null;
		
		try {
			conn = BaseDao.getConn();
			cstm = conn.prepareCall("call proc_cursor(?,?)"); // 调用存储过程 
			cstm.setLong(1, deptno);
			cstm.registerOutParameter(2,OracleTypes.CURSOR);
			cstm.execute();
			rs = ((OracleCallableStatement)cstm).getCursor(2);
			while(rs.next()){
				System.out.println(rs.getLong("EMPNO")+"----"+ rs.getString("ENAME"));
			}
			
		} catch (SQLException e) {
			e.printStackTrace();
		}
	}
} 

```

##### 3.测试

```java
package com.itcast.dao;
public class Test {
	public static void main(String[] args) {
		//EmpDao.getEmp(10L);
		
		//Long yearSal = ProcedureDao.getYearSal(7934L);
		//System.out.println("年薪 :   " + yearSal);
		
		//Long yearSal2 = FunctionDao.getYearSal(7934L);
		//System.out.println("年薪 :   " + yearSal2);
		
		Cursor.getEmp(10L);
	}
}
```



#### 触发器

##### 触发器的作用

​	数据确认

​	实施复杂的安全型检查

​	做审计,跟踪表上所做的数据操作等

​	数据的备份和同步

当表中的数据发生改变时，触发了某些操作

##### 触发器的类型

###### 语句级触发器

​	在指定的操作之前或者之后执行一次,不管这条语句影响了多少行

###### 行级触发器

​	触发语句作用的每一条记录都被触发.在行级触发器中使用old 和 new 伪记录变量,识别值的状态

###### 触发器的使用

###### 1.语法:

```plsql
delete  insert 	update
语法:
create  or  replace   trigger  触发器名称
before|after
delete | insert | update 
on 表名
[for each row]
	
[declare]
begin
	
end;


create  or replace trigger tri_emp
after
insert
on emp
--[for each row]
declare
begin
  dbms_output.put_line('新入职了一名员工');
end;

-- 测试
insert into emp(empno,deptno) values(1,10); 
```

###### 2.使用:

```plsql
-- 案列一 :
--假如'2017-10-20'系统维护，不能修改emp中的数据
create or replace trigger tri_emp
before 
delete or insert or update 
on emp
--[for each row]
declare 
  v_dateStr varchar2(20); 
begin
  ----判断当天sysdate是否是'2017-10-21'
  select to_char(sysdate,'yyyy-mm-dd')  into v_dateStr  from dual;
  if v_dateStr = '2017-10-21' then 
      raise_application_error(-20002,'今天系统维护，不能修改emp中的数据');
  end if;
end;

-- 测试
insert into emp(empno,deptno) values(2,10);
update emp set ename='AAAA' where empno =1 ;
delete from emp where empno=1;
select * from emp; 


--案例二:
---备份员工的工资数据
--1.创建表
create table emp_sal_log(
    eid number(10) PRIMARY KEY ,
    empno number(10),
    sal0 number(10,2),
    sal1 number(10,2),
    logDate date
)
--2.创建序列
create sequence seq_emp_sal_log;
--3.创建触发器
create or replace trigger  tri_emp_sal
after
update of sal 
on emp 
for each row  -- 出现了 :old 或者 :new 使用for each row
--  :new   :old 伪记录
declare
begin
  insert into emp_sal_log  values(seq_emp_sal_log.nextval,:new.empno, :old.sal,:new.sal,sysdate);
end;
  
             :new    :old
update               
delete       null    
insert               null

-- 测试
update emp set sal=10000 where empno=7369;
select * from emp_sal_log;
select * from emp;

update emp set deptno=10 where empno=7369;


-- 案列三:
-- 解决主键为null插入数据
create table t_demo(
  tid number(10) primary key,
  tname varchar2(10)
)
-- 这句话执行失败   主键不能为空
insert into t_demo(tname) values('aaaa');

-- 解决问题
create or replace trigger tri_demo
before
insert 
on t_demo
for each row 
declare
begin
  -- 在插入之前对即将插入的对象赋值id
  select seq_emp_sal_log.nextval into :new.tid from dual;
end;

-- 测试
insert into t_demo(tname) values('aaaa');
select * from t_demo;
```

### 实际情况

#### 对数据的误操作一:

```plsql
 --误操作一
 drop table emp;    -- 误删了一张表数据
 
 -- 解决办法 
 -- 在Recycle bin (Oracle回收站) 可以恢复数据
```



#### 对数据的误操作二:

```plsql
-- 误操作二:
delete 	 *   from  	emp  	where 	empno = 1;

 -- 解决办法 :
create table emp_bak
as
select * from emp as of TIMESTAMP to_timestamp('20171020 103435','yyyymmdd',
  hh24miss);
select * from emp_bak;
```

### Mysql&Oracle

#### 触发器:

| 差异       | MYSQL                                    | ORACLE                                   | 说明                                       |
| -------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| 创建语句不同   | create trigger 'AA' BEFORE  INSERT on  'BB'   for each row | create or replace trigger AA    before insert or update or delete on BB    for each row | 1.Oracle可以在一个触发器触发insert,delete,update事件.      Mysql每个触发器只支持一个事件. 也就是说,目前每个trigger需要拆分成3个mysql trigger. |
| 引用新旧数据不同 | 取得新数据:  NEW.aa  取得老数据:  OLD.bb           | 取得新数据:  :new.aa  取得老数据:  :old.bb         | 1.oracle 多一对冒号                           |



#### 存储过程:

| 差异     | MYSQL                                    | ORACLE                                   | 说明                                       |
| ------ | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| 创建语句不同 | DROP PROCEDURE IF EXISTS  'SD_USER_P_ADD_USR';  create procedure AA(aa varchar(100)) | create or replace procedure AA(     varchar aa) is | 1.oracle创建语比较简洁,mysql要先执行drop  2.mysql先变量再类型,oracle相反,且不必限定长度  3.如果是number或varchar2的话不需要定义长度。否则编译不能通过 |
|        | DECLARE EXIT HANDLER FOR   AAEXCEPTION    BEGIN     ...   END; | EXCEPTION      WHEN OTHERS THEN      ROLLBACK ;      .... | 1.mysql不能自定义异常,且使用内部异常时需要先定义             |

### 小结

1.plsql基本语法	**

2.存储过程和存储函数  ***

3.jdbc调用存过程和存储函数    *****

4.触发器	 	****





