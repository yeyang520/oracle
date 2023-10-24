# 1.Oracle启动

## 1. 1启动

sqlplus:

启动相关服务：oracleserviceORCL
方法一：控制面板--管理--服务--找到对应服务启动即可。
方法二：命令行方式启动： cmd --net start|stop oracleserviceorcl

输入sqlplus，输入对应的用户名和口令

常用的用户名和口令：

sys: 超级管理员，默认口令change_on_install,当前已修改为：Oracle11. 以sys用户登录，必须指定sysdba或者sysoper身份。
system: 普通管理员，manager;
scott: 普通用户，tiger,从oracle10g开始，该用户默认为锁定状态，需要解锁使用。
sh: 普通用户，sh. 有大量数据

## 1.2 Scott用户

解锁scott: 
以sys登录数据库。sys/Oracle11 as sysdba

执行解锁命令：alter user scott identified by tiger account unlock|lock;

切换用户命令：connect username/password [as sysdba]
connect scott/tiger
show user

## 1.3 SQL分类

DQL:数据查询语言，select 
DDL:数据定义语言，create,alter,drop
DML:数据修改语言，insert,update,delete
DCL:数据控制语言，grant,revoke,commit,rollback

## 1.4 select从句

* 查询所有信息 *  

  * select * from tablename;
  * 注意：oracle是大小写不敏感，sql语句通常以分号作为结束标志。*效率非常低，应尽量少用。

* 查看特定信息，多个属性列之间用逗号分隔即可。

  * 查询所有员工的工号、姓名和参加工作时间。 
    select empno,ename,hiredate from emp;

* 去掉重复信息。 distinct

  * ```sql
     select deptno from emp;
     select distinct deptno from emp;
      select distinct deptno,sal  from emp;
    ```

* 连接运算符：||  

  * 查询每个员工的工资，要求输出形式为：xxx员工的工资为：XXXX 

    select ename||'员工的工资为：'||sal from emp;

## 1.5 脱机文件

```sql
spool path  -- 生成脱机文件
spool off   -- 保存
spool path append -- 追加到当前文件中
```



# 2. 条件查询 

select from where; 

## 2.1 简单比较运算符 >, <,=, !=,<>

查询工资超过2000的员工信息。

```sql
select empno,ename,sal from emp where sal>2000;
```



## 2.2 逻辑运算符：not and or 

查询20、30号部门工资超过2000的员工信息。

```sql
select empno,ename,sal from emp where sal>2000 and (deptno=20 or deptno=30);
```



## 2.3 between and 运算符：

取值是一个连续的值域。

查询工资在2000到3000之间的员工信息。

```sql
select empno,ename,sal from emp where sal>=2000 and sal<=3000;
select empno,ename,sal from emp where sal beween 2000 and 3000;
```



## 2.4 in运算符

取值是几个数值中的一个时，通常用in. 

```sql
select empno,ename,sal from emp where sal>2000 and deptno in(20,30);
```



## 2.5 like运算符

只了解信息的一部分，这时可以用like运算符，通常称为模糊查询。

 两个通配符：%(任意个字符） _（任意一个字符）

查询姓名第二个字符为A的员工信息。 ename like '_A%'   '%A%'

模糊查询的效率非常低，应少使用。

## 2.6 is运算符

用于空值的查询。   is null   is not null     comm is null   comm=null(不行)

空值：null, 空值不代表任何一个具体的数值，空值与任何值运算，结果永远是空值。 
null+30  null>30  null and 30  null||'null'

空值处理函数：nvl(a,b), a为空，返回b, a非空，返回a
             nvl2(a,b,c)

查询每个员工的月收入。 

```sql
select ename,sal+comm from emp;
select ename,sal+nvl(comm,0) from emp;
```



## 2.7 查询结果的排序

select from where order by ;  默认是升序asc,可以用desc指定降序输出。

```sql
select empno,ename,sal from emp where sal beween 2000 and 3000 order by ename asc,sal desc;
```

```sql
select empno,ename,sal from emp where sal beween 2000 and 3000 order by 2 asc,3 desc;
```

## 2.8 Oracle中日期型数据的表示

默认日期函数：sysdate  默认日期格式：dd-mm-yyyy  dd-mm月-yy

注意：日期型数据与字符型数据一样，需要用单引号括起来。

```sql
-- 查询系统当前时间：
select sysdate from dual;

-- 查询每个员工的工龄。 
select ename,(sysdate-hiredate)/365 from emp;
```

# 3. 函数

## 3.1 函数分类

* 单行函数
  * 字符函数
  * 数字函数
  * 转换函数
  * 日期函数
* 聚合函数

## 3.2 单行函数

#### 3.2.1 字符函数

```sql
ascii(x)   -- 返回字符x的ascii码

concat(x, y) -- 连接字符串x和y

instr(x, str, [start], [n])  -- 从x中查找str，可从start开始查找，第n次

leng(x)	  -- 返回x的长度

lower(x)  -- 将x转换成小写

upper(x)  -- 将x转换成大写

ltrim(x， [trim_str])  -- 把x的左边截去trim_str，缺省为空格

rtrim(x,  [trim_str])  -- 右边

trim(x)	 -- 空格

replace(x, old, new) 　-- 在x中查找old，并替换成new

substr(x, start, [length])  -- 返回x的字符串，从start处开始， 截取length个字符，缺省默认到结尾
```

### 3.2.2 数字函数

```sql
abs(x) -- 绝对值
```

### 3.2.3 日期函数

```sql
add_months(d, n) -- 在一个日期d上， 加上指定的月数n，返回计算后的新日期

last_day(d) -- 返回指定日期最后一天

round(d, [fmt]) --　返回fmt格式的四舍五入日期值

extract(fmt from d) -- 提取日期
```

### 3.2.3 其他单行函数

```sql
nvl(x, value) -- 如果x为空，返回value，否则返回x

nvl2(x, value1, value2) --　如果x非空返回1，否则返回2
```

### 3.2.4 聚合函数

```sql
avg

sum

min

max

count
```

## 3.3 工龄计算

```sql
(sysdate-hiredate)/365  ceil floor round 
months_between(sysdate,hiredate)/12
to_char(sysdate,'yyyy')-to_char(hiredate,'yyyy')
```

## 3.4 日期函数

```sql
-- 获取日期数据中的年、月、日：
to_char,to_date,to_number

to_char(日期型数据,'format')   
-- format: yyyy,mm,ddd,dd,d,hh,mi,ss

to_char(sysdate,'yyyy')
```

## 3.5 聚类函数

count,sum,avg,max,min

注意：

* 有一个函数参数可以是*，count
* 有两个函数参数要求必须是数值型。sum,avg
* 聚集函数可以出现在select、having和order by 从句中。

```sql
-- 查询emp表有多少记录。
select count(empno) from emp;
select deptno,avg(sal) from emp group by deptno;

select deptno from emp; -- 返回值有多行
select avg(sal) from emp; -- 返回值只有1行.
```

**注意：当查询中即有单值列，又有聚集函数时，所有出现的单值列必须作为分组列处理。 分组关键字group by**

```sql
select deptno,avg(sal) 
from emp 
where avg(sal)>2000
group by deptno;
```

```sql
-- 对分组后的结果进一步筛选，需要用having子句。having是随着group by的出现而出现的。
select deptno,avg(sal) from emp  group by deptno having avg(sal)>2000;
```

```sql
-- 区分一下where和having的用法： 只有包含聚集函数的条件表达式出现在having子句中，否则出现在where. 
select deptno,avg(sal) from emp where deptno in(20,30) group by deptno having avg(sal)>2000;
select deptno,avg(sal) from emp  group by deptno having avg(sal)>2000 and deptno in(20,30);
```

# 4. 集合运算

## 4.1 查询集合运算： 并，交，差

```sql
并：union all,union 
select distinct deptno from emp; 10,20,30
select distinct deptno from dept;10,20,30,40                                                                                                        
select distinct deptno from emp
 union all
select distinct deptno from dept;        10,20,30,10,20,30,40     
```

```sql
10,20,30,10,20,30,40     

-- 并 union         10,20,30,40                       -- 交：intersect    10,20,30   
-- 差：minus        40                        
```

## 4.2 查询表的特定记录

rownum: 伪列，给查询结果添加行号。当其出现在where子句中时，其值总是从1开始。   dual 伪表  

```sql
-- 查询emp表的第1条记录
select rownum,ename,sal from emp where rownum=1;
-- 查询emp表的前3条记录    
select rownum,ename,sal from emp where rownum<4;                                                           -- 查询emp表的第3条记录                    
select rownum,ename,sal from emp where rownum<4
minus    
select rownum,ename,sal from emp where rownum<3;    
```

## 4.3 查询中实现if else功能

查询每个部门的名称，若部门名为accounting，则显示财务处，若部门名为sales，显示销售部，否则显示其它部门。 

### 4.3.1 decode函数

```sql
decode(a,b,c)
decode(a,b,c,d)
decode(a,b,c,d,e)
decode(a,b,c,d,e,f)
select dname,decode(lower(dname),'accounting','财务处','sales','销售部','其它部门') from dept;
```

### 4.3.2 case语句

```sql
-- 简单case语句： 
case a when b then c
       when d then e
       ......
       else n
end

-- 示例
select dname,case lower(dname) when 'accounting' then '财务处'
                               when 'sales'      then '销售部'
                               else '其它部门'
                   end  from dept;
```



```sql
-- 搜索case语句： 

case when 条件表达式1 then  输出1
     ......
     else 输出n
end

-- 示例
select dname,case  when lower(dname)'=accounting' then '财务处'
                   when lower(dname)='sales'     
then '销售部'
                   else '其它部门'
             end  case from dept;
```

# 5. 连接

## 5.1 别名

列别名：列名 空格 别名，  列名 as 别名.  列别名可以出现在select从句和order by 从句中。 
表别名：表名 空格 别名 , 表别名一经定义，在该查询中只能使用表别名，不能使用表原名。

## 5.2 连接查询

数据来源多个。 from a,b,c,d

注意：连接查询的来源有多个，书写连接查询，必须要有连接条件，N个表连接，至少需要N-1个连接条件。

**分类：内连接和外连接**
内连接：只有满足连接条件的记录输出，不满足连接条件的记录不输出。内连接分为等值连接、自连接和不等连接。

**等值连接**：要连接的两个表有公共列(列的数据类型和宽度一致），则两个表可以进行等值连接。  a.公共列列名=b.公共列列名

```sql
-- 查询每个员工所在部门名称。 
select ename 姓名,deptno,dname 部门名
from emp,dept
where emp.deptno=dept.deptno;

-- 注意：连接查询要避免出现二义性问题，建议在书写SQL连接查询语句时，给出现的每个属性列添加归属对象。
```

**自连接**：要连接的两个表是同一个表。

**不等连接**：连接条件用不等号进行连接。  salgrade 

```sql
 select e.ename,e.sal,s.grade
 from emp e,salgrade s
 where e.sal between s.losal and s.hisal
```

**外连接**：除了满足连接条件的记录输出外，部分不满足连接条件的记录也会输出。oracle中引入外连接运算符(+),该运算符添加到信息缺失方。外连接分为左外连接和右外连接。 

```sql
select e.ename 员工名 ,m.ename 经理名  from emp e,emp m where e.mgr=m.empno(+); --左外连接     

select e.ename 员工名 ,m.ename 经理名  from emp e,emp m where m.empno(+)=e.mgr; --右外连接          
```

左外连接、右外连接、全外连接  outer join               

# 6. 子查询

## 6.1 引例

```sql
-- 查询出每个雇员的姓名，工资，部门名称，工资等级(salgrade)及其领导的姓名,领导的工资,以及领导工资所对应的等级；
select e.ename,e.sal,se.grade,d.dname,m.ename,m.sal,sm.grade
from emp e,dept d,salgrade se,emp m,salgrade sm
where e.deptno=d.deptno and 
      e.sal between se.losal and se.hisal and
      e.mgr=m.empno(+) and
      nvl(m.sal,800) between sm.losal and sm.hisal;
```

## 6.2 子查询

**子查询**：查询a作为查询b的一部分出现，则a称为子查询。子查询通常用括号括起来。子查询可以出现在from,where,having子句中。

```sql
-- 查询哪些员工工资比7788员工工资高。 

select ename from emp where sal>(select sal from emp where empno=7788);
```

* 单行子查询：子查询的返回值最多只有一个，通常使用简单比较运算符。 

* 多行子查询：需要引入多行子查询比较运算符：in,any,all,其中,in单独使用，any和all需要结合简单比较运算符使用。

```sql

-- 查询哪些员工工资比20号部门任意员工工资高。 
select ename from emp where sal>all(select sal from emp where deptno=20);
                            sal>(select max(sal) from emp where deptno=20)

-- 查询哪些员工工资比20号部门任一员工工资高。
select ename from emp where sal>any(select sal from emp where deptno=20);
                            sal>min(sal)
```

## 6.3 查询特定行

```sql
-- 查询emp表的第2，4，6行。

select * from (select rownum r,ename,sal,hiredate from emp) a where r in(2,4,6);

select rownum r,emp.* from emp where r in (2,4,6); 
-- 错误
```

# 7. 分析函数

**分析函数**：rank,row_number() over()

## 7.1 row_number() over

**形式1**： 

```sql
row_number() over(order by column1 desc) 
```

 把查询信息按照column1列排序后，添加对应的序号。

```sql
select ename,hiredate,sal from emp order by sal;

select ename,hiredate,sal,row_number() over(order by sal ) from emp;

select rownum,a.* from (select ename,hiredate,sal from emp order by sal desc)a;
```

```sql
-- 求薪水最高的第6到第10名雇员

select * from (select ename,hiredate,sal,row_number() over(order by sal desc) r from emp) b where r between 6 and 10;

select * from (select rownum r,a.* from (select ename,hiredate,sal from emp order by sal desc)a)b where r between 6 and 10;
```

**形式2**

```sql
row_number() over(partition by column1 order by column2)
```

把查询信息先按照column1分组，在每个分组内部按照column2排序后，添加相应序号。

```sql
select ename,hiredate,sal,row_number() over(partition by deptno order by sal ) r from emp;

select * from (select ename,hiredate,sal,deptno,row_number() over(partition by deptno order by sal desc) r from emp) where r<3;
```

# 8. 操作表

**DDL**：数据定义语言， 创建数据库中的对象（表，视图，索引，序列号，链接，同义词）

表的管理：创建、修改和删除表。 

## 8.1 创建表

* 方法一：通常查询建表。 

  * ```sql
    create table tablename as select;
    ```

```sql
create table dept_bak as select * from dept;
select * from dept_bak;

select * from (select * from dept) dept_bak1;

create table avg_sal as select deptno,avg(sal) avgsal from emp group by deptno;
```

* 方法二：建新表。 

  * ```sql
    create table tablename
    (列名 数据类型 [约束类型],,,,);
    ```

## 8.2 常用数据类型

* 数值型：number(a[,b]) a代表总的有效数字位数，b代表小数点后数字位数。
* 字符型: char(n),varchar2(n)  name char(20)  name1 varchar2(20)  'smith'  varchar(n)
  日期型: date  1999-1-1  '1-1月-1999'  date'1999-1-1'
* LOB（大对象）

## 8.3 建表示例

```sql
-- 学生成绩管理系统：学生表student(姓名、学号、性别、出生日期、入学时间），课程表，成绩表，选课表，教师表

-- 学生表student(姓名 s_name 、学号 s_id、性别 s_gender、出生日期 s_birth、入学时间 s_come）

create table student
(s_id char(12),
 s_name varchar2(30),
 s_gender char(1),
 s_birth date,
 s_come date);

```

## 8.4 约束

* 如何保证表有用？可以利用完整性方法确保表中数据的准确性。

* 如何实现完整性？约束，触发器，门电路

* 约束：最直接，最简单的实现数据完整性的方式，直接添加到表上的。分为主键、唯一性、检查、非空和外键5种约束。 

### 8.4.1 约束分类

**1. 主键约束**

```sql
-- 主键约束：primary key, 建议约束名以pk_开头，约束列特点：列值不能为空，不能重复。一个表最多有1个主键约束。

create table student1
(s_id char(12) primary key,
 s_name varchar2(30),
 s_gender char(1),
 s_birth date,
 s_come date);

```

**2. 唯一性约束**

```sql
-- 唯一性约束：unique,建议以uk_开头， 约束列特点：列值不能重复。  约束列列值能否为空值？可以  
                                                     -- 能不能包含多个空值？  可以

create table student1
(s_id char(12) primary key,
 s_name varchar2(30) unique,
 s_gender char(1),
 s_birth date,
 s_come date);
```

**3. 非空约束**

```sql
-- 非空约束：not null,约束列特点：列值不能为空。
create table student1
(s_id char(12) primary key,
 s_name varchar2(30) unique not null,
 s_gender char(1),
 s_birth date not null,
 s_come date);
```

**4. 检查约束**

```sql
-- 检查约束：check(条件)，建议以ck_开头， 约束列特点：列值要满足指定条件。

create table student1
(s_id char(12) primary key,     --列级约束
 s_name varchar2(30) unique not null,
 s_gender char(1) check(s_gender in('f','m')) ,
 s_birth date not null,
 s_come date);
 
 --  s_gender能不能取空值？可以
```

**5. 外键约束**

```sql
-- 外键约束：foreign key(约束列) references 引用表(引用列)  ,约束名通常以fk_开头
-- 注意：引用列和约束列的数据类型和宽度一定保持完全一致。  约束列列值取值范围在引用列列值范围内。约束列列值可以为空值。引用表中的引用列列值具有唯一性。


course(c_id,c_name,c_credit)
score(s_id,c_id,s_score)

create table score
(s_id char(12) ,
 c_id varchar2(20) references course(c_id) ,
 s_score number(4,1),
 constraint pk_s_id_c_id primary key(s_id,c_id)，  
 -- 表级约束
 constraint fk_s_id foreign key(s_id) references student1(s_id)
 );
```

**约束完整格式**

```sql
-- 约束完整格式：
constraint constraint_name constraint_type(constraint_column,,)
```

# 9. 修改数据

**DML**：数据修改语言，insert,update,delete.用来对表中数据的增、改、 删

## 9.1 增

**insert**:  insert into 表名[(列名1,,,)] values(值1,,,);  一次只能添加一条。

```sql
-- dept表增加11号部门 11,computer,linyi
insert into dept(deptno,dname,loc) values(11,'computer','linyi');

-- 添加12号部门，  12 

insert into dept(deptno) values(12);
```

**方法二**：insert into  表名[(列名1,,,)] select;

```sql
insert into dept(deptno) select 12 from dual union select 13 from dual union select 14 from dual;
```

## 9.2 改

**update**: update 表名 set 列名1=新值,,,,,,, [where 条件];

```sql
update emp set sal=sal*1.1;

update emp set sal=sal*1.1 where deptno=20;
```

```sql
-- 以下两个操作都会失败：外键在起作用。

-- 把emp表中的10号部门部门号更新为15号部门。
update emp set deptno=15 where deptno=10;


-- 把dept表中的10号更新为15号。
update dept set deptno=15 where deptno=10;
```

## 9.3 删

**delete**: delete from 表名 [where 条件];

```sql
delete from emp;
delete from emp where deptno=10; 
```

## 9.4 事务

事务：ACID 。  每一个DML操作都不是一个完整的事务，必须有一个确认过程（commit, rollback). 每个DDL操作都是一个完整的事务。

# 10. 修改表

alter table tablename ....(针对表的结构的修改：添加新列，修改已有列属性，删除已有列)

## 10.1 增

添加新列： alter table tablename add (列名 数据类型,,,,);

```sql
create table test(id number(3));

-- 向test表添加name列.
alter table test add (name varchar2(20));
```

## 10.2 改

修改已有列属性：alter table tablename modify 列名 新的数据类型;

```sql
-- 把name 列宽度修改为30

alter table test modify name varchar2(30);
```

## 10.3 删

```sql
-- 删除已有列：
alter table tablename drop column 列名;  


drop table tablename [cascade constraint];-- 删除操作是不可逆的。  

delete from test; -- 表中没有数据，表还在，可逆的（rollback)
truncate table test;-- 截断表，表中没有数据，表还在，不可逆
drop table test;-- 表中没有数据，表没了，不可逆
```

## 10.4 约束的修改

* 添加约束：

  ```sql
  alter table tablename add constraint constraint_name constraint_type(constraint_column,,)，
  ```

  该命令可以添加除非空约束之外的其它约束。

```sql
-- 向test表id列添加主键约束。 
alter table test add constraint pk_test_id primary key(id);

test1(id number(3))
 -- 给test1添加外键约束，引用test中的id
 alter table test1 add constraint fk_id foreign key(id) references test(id);

-- 添加非空约束： alter table tablename modify 列名 not null;
```

* 删除|禁用|启用约束： 

  ```sql
  alter table tablename drop|disable|enable constraint constraint_name;
  ```

* 查询约束信息：user_constraints

  ```sql
  select constraint_name,constraint_type,table_name from user_constraints;
  ```



# 11. 视图

**视图**：本质上是一个虚表。其不包含任何数据，所有数据来源于创建视图的基表。引入视图的目的是为了保护基表数据。

## 11.1 创建视图

```sql
-- 创建视图： 
create [or replace] view view_name 
as select 
[with check option]
[with read only];



create view view_name as select;
```

```sql
-- 创建一个视图，查询部门表的信息。

create view view_dept as select * from dept;
desc view_dept
select * from view_dept;
```

**创建视图时，需要由管理员授予相应权限：**

```sql
conn sys/Oracle11 as sysdba
grant create view to scott;
```

**通过视图修改基表数据：**

```sql

insert into view_dept(deptno) values(11);

create view view1 as select ename,sal from emp where deptno=20;

insert into view1 values('111',1200);  添加失败

create view view2 as select deptno,avg(sal) avgsal from emp group by deptno;

create view view_dept1 as select * from dept with read only;

insert into view_dept1(deptno) values(12);
```

## 11.2 with check option

当对视图进行DML(insert)操作时，必须满足创建视图时指定的where子句条件。 

```sql
create view view3 as select empno,ename,sal,deptno from emp where deptno=20;

insert into view3(empno,deptno) values(1111,10);

create or replace view view4 as select empno,ename,sal, deptno from emp where deptno=20 with check option;

-- 只有deptno = 20 才能插入，需要满足where条件
insert into view4(empno,deptno) values(1112,10); -- 失败
insert into view4(empno,deptno) values(1112,20); -- 成功
```

## 11.3 删除视图

```sql
drop view view_name;
```

## 11.4 保存的表

```sql
user_views  
user_tables 
user_objects 
user_indexes 
user_constraints
```

# 12. 索引和序列号

## 12.1 索引

**索引**：主要作用提升查询效率。索引越多越好？不是，因为索引需要维护。应尽量保证索引列按顺序出现在条件表达式中。

```sql
-- 创建索引：
create [unique] index index_name on tablename(columnname desc,,,);
```

```sql
-- 在emp(ename)创建一个简单索引
create index idx_ename on emp(ename desc);

-- 判断以下select语句，哪个有可能会用到idx_ename索引？
select * from emp;
select * from emp where deptno=20;
select * from emp where ename='SMITH';
select * from emp where lower(ename)='smith';
```

**索引类型**

```sql
create index idx1 on emp(lower(ename)); 
--基于函数的索引。

create index idx2 on emp(ename desc,deptno,sal);
-- 复合索引
```

```sql
-- 判断以下select语句，哪些有可能会用到idx2索引？

select * from emp where deptno=20;
select * from emp where ename='SMITH';
select * from emp where deptno=20 and ename='SMITH';
select * from emp where ename='SMITH' and deptno=20;
select * from emp where ename='SMITH' and sal>2000;
select * from emp where ename='SMITH' and deptno=20 and sal>2000;
```

**注意**：主键约束和唯一性约束，系统自动为其约束列创建唯一性索引。

* 重建索引：

  ```sql
  alter index indexname rebuild;
  ```

* 删除索引：

  ```sql
  drop index indexname;  user_indexes
  ```

## 12.2 序列号

**序列号** sequence,自动生成一个序列，通常用于给主键约束列自动生成序列号。

```sql
-- 创建序列号：
create sequence sequence_name;  create sequence seq1;
create sequence sequence_name
[start with m increment by n
maxvalue A
minvalue B
cycle|nocycle
cache C];


create sequence seq2 start with 3 increment by 2  maxvalue 20;
```

**使用**

```sql
-- 使用序列号： 需要用到两个伪列：序列名.currval,nextval,序列号初次使用，需要用nextval初始化。


-- 创建了一个seq1序列，插入seq1.nextval作为主键，每次都会+1
create table test(id number primary key);
insert into test(id) values(seq1.nextval);
```

# 13. 用户管理

oracle数据库的工作模式：1个数据库--包含了多个用户--每个用户下都有自己的对象

用户管理：一定是由管理员完成(sys).创建用户、修改用户和删除用户。其是oracle数据库安全管理的一部分。

目的：一个新建用户，能够创建自己的对象，拥有属于自己的数据。

## 13.1 创建用户

```sql
create user username identified by password [account unlock|lock];
```

```sql
-- 新建test用户，口令是oracle

create user test identified by oracle;
```

## 13.2 修改用户

```sql
-- 修改用户： 
alter user username identified by password [account unlock|lock];
```

## 13.3 删除用户

```sql
-- 删除用户： 
drop user username;
```

## 13.4 权限管理

**权限管理（授权、回收）**：权限分为系统权限和对象权限。系统权限通常由管理员负责管理，对象权限由对象的所有者进行管理。

系统权限：对数据库进行操作的能力。
常见： 

* create table,
* create view,
* create session,
* create function,
* drop table,
* drop any table,,,,,
* unlimited tablespace

### 13.4.1 授予系统权限

```sql
-- 系统权限的授权：
grant 权限名,,,, to username [with admin option];
```

```sql
-- 给test用户访问数据数据库（创建会话) 的权限。
grant create session,create table,unlimited tablespace to test;  --普通授权
grant create session,create table,unlimited tablespace to test with admin option; --可以实现级联授权
```

### 13.4.2 回收系统权限

```sql
回收系统权限：revoke 系统权限名,,,, from username;

revoke create table from test;
```

系统权限的回收不具有级联性。

### 13.4.3 授予对象权限

```sql
-- 对象权限的授权：
grant 权限名 on 对象名  to username [with grant option]; 通常由对象的所有者完成授权。
```

常见的对象权限：

* select,
* insert
* update,
* delete,
* execute

```sql
-- 给test用户查询scott用户dept表的权限。
 conn scott/tiger
 grant select on dept to test;
 grant select on dept to test with grant option;
 conn test/oracle
 select * from scott.dept;
```

### 13.4.4 回收对象权限

```sql
-- 对象权限的回收：
revoke 权限名 on 对象名  from username;
```

对象权限的回收具有级联性。

### 13.4.5 角色的管理

角色管理：role. 权限的集合，为了更有效地管理权限。系统的默认角色：connect,resource,dba

举例：给100个用户，授权100个权限，一次只能给1个用户授予1个权限。

不使用角色方式，需要权限授权多少次？100*100
使用角色，先创建一个角色，把100个权限授权给角色(100次），给角色授权给用户（100次）。 100+100

user_sys_privs
