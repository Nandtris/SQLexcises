# MySql_Key oldboy

- 数据库是什么
- MySql安装
- 用户授权
- 数据库
  - 数据库操作
    - `show databases;'
    - `create database db2;`
    - `drop database db2;`
    - `use database db1;`
  - 数据表操作
    - 数据类型
    - 是否可以为空
    - 自增
    - 主键
    - 外键
      - 一对一
      - 多对多
      - 一对多
    - 唯一索引
    - `drop table userinfo;`
    ```MySql
    create table userinfo(
    	id bigint not null auto_increment,
		name varchar(99) not null,
		gender int(2) not null,
		email varchar(1000) not null,
		primary key(id),
		unique(name, email),
		constraint fk_userinfo_gender (gender) references genderinfo(id)
    )engine=innodb default charset=utf8;
    
    create table userinfoM select * from userinfo where gender='M';
    
    ALTER table userinfo ADD column birth varchar(10) not null;
    alter table userinfo CHANGE column birth birthday varcahr(20) not null;
    alter table userinfo DROP column birthday;
    
    -- ? bigint to timestamp
    alter table age_of_barbarians MODIFY register_time timestamp(0);
    alter table student ADD [UNIQUE]INDEX uni-name(name);
    ```
  - 数据行操作
    - 增删改
      ```MySql
      insert into student(sname, gender, class_id) values('Alex', '男', 3);
      insert into student(sname) select name from student2;
	  -- 实用SQL语句（廖）
	  insert into statistics(class_id, average)
	      select calss_id, avg(score)
		  from student group by class_id;
	  
	  --有自增则从删除后的序号后继续进行后面的插值
      DELETE FROM table_name [WHERE Clause]
	  --删除内容、释放空间但不删除定义, 自增咧初始值为1
	  truncate table student;
	  --删除内容和定义，释放空间;不能再次新增数据，除非新建一个表
	  drop table student;
	  
	  update userinfo set name='Alex', email='xx@oo.com' 
	      where id>5 and name='xx';
	  
	  
	  ```
  
    - 查
      - 排序
      - 分组
      - 条件
      ```MySql
	  
	  where id !=1
	  		id in (1, 5, 11)
			id in (select sid from student)
			id not in(1, 5, 11)
			id between 1 and 11
			
	  ```
      
      - 临时表
      ```MySql
	  
	  --动态查询
	  select student_id,
	  	(select number from score s2 where s1.student_id=s2.student_id and course_id=1) as 生物,
		(select number from score s2 where s1.student_id=s2.student_id and course_id=2) as 体育,
		count(number) cnum,
		avg(number) avgnum
	  from score s1 group by student_id;
	  
	  --case when 统计及格率
	  select course_id, avg(number) avgnum,
	  	sum(CASE WHEN number < 60 THEN 0 ELSE 1 END)/sum(1) passRate
	  from score group by course_id
	  order by avg(number) asc, passRate desc;
	  
	  --if(isnull(x), 0, 1)
	  avg(if(isnull(score.number), 0, score.number))
	  
	  ```
      - 通配符 `like 'a%'; like 'a_';`
      - 分页: `limit 0, 10 == limit 10 offset 0` -- 0=起始值
      - 上下组合：`union[all]`
      - 笛卡尔集
      ```MySql 8
	  select * from score s1, score s2;
	  ```
	  - 连表 left/right/inner join
	  ```MySql 8
	  --5 tables join
	  select * from score -- * EORRO 列名ID重复，可以`score.id`
	  	left join student on score.student_id = student.sid
		left join course on score.course_id = course.cid
		left join class on student.class_id = class.cid
		left join teacher on course.teacher_id = teacher.tid;
	  ```
- 视图（虚拟）
- 触发器
- 函数：`select f(x);`
- 存储过程
  - 游标
  - 事务
  - 结果集+返回值
- pymysql
  - 链接 connect()
  - 操作（游标）
    - 增删改 -> commit()
    - 查询   -> fecthall()/fetchone
    - SQL注入
      - `select * from user where username='x' and pwd='asd'`
      - `select * from user where username='x' or 1=1 -- ' and pwd='asd'`
    - 调用存储过程
      - `callproc('p1', (args1, args2, ...))`
      - `select @_存储过程名称_0`
    - 关闭游标
    - 关闭连接
- p15 自增步长
  - session
  ```MySql
  set session auto_increment=2; --以2为步长递增
  alter table set auto_increment=10;
  show create table class\G; --竖向显示表属性
  shoe session variable like '%auto_inc%'; --会话级别变量查看
  ```
  - global
  ```MySql 
  set global auto_increment=2;
  set global auto_increment_offset=10;--起始值
  ```
  
# MySQLexcises(牛客+oldboy)

> https://www.nowcoder.com/activity/oj  牛客SQL<br>
> https://blog.csdn.net/zzzgd_666/article/details/80594087 时间相关<br>
> https://www.bilibili.com/video/BV1fx411Z7CV oldboy<br>

## Review 1

### 1 程序运行顺序 SQL11
- https://database.51cto.com/art/201911/605471.htm
- refer to: https://database.51cto.com/art/201911/605471.htm

```SQL
(9) select (10) distinct column,
(6) agg_func(column or expression), ...
(1) from left_table
    (3) join right_table
    (2) on tablename.column = 
           other_tablename.column
(4) where constraint_expression
(5) group by column
(7) WITH CUBE|ROLLUP
(8) having constraint_expression
(11) order by column asc|desc
(12) limit count offset count;

```
```
从这个顺序中我们可以发现，所有的查询语句都是从 FROM 开始执行的。
在实际执行过程中，每个步骤都会为下一个步骤生成一个虚拟表，
这个虚拟表将作为下一个执行步骤的输入。 
接下来，我们详细的介绍下每个步骤的具体执行过程。

1 FROM 执行笛卡尔积
FROM 才是 SQL 语句执行的第一步，并非 SELECT 。
对FROM子句中的前两个表执行笛卡尔积(交叉联接），
生成虚拟表VT1，获取不同数据源的数据集。

FROM 子句执行顺序为从后往前、从右到左，
FROM 子句中写在最后的表(基础表 driving table)将被最先处理，
即最后的表为驱动表，当FROM 子句中包含多个表的情况下，我们需要选择数据最少的表作为基础表。

2 ON 应用ON过滤器
对虚拟表VT1 应用ON筛选器，ON 中的逻辑表达式将应用到虚拟表 VT1中的各个行，
筛选出满足ON 逻辑表达式的行，生成虚拟表 VT2

3 JOIN 添加外部行
如果指定了OUTER JOIN保留表中未找到匹配的行将作为外部行添加到虚拟表 VT2，生成虚拟表 VT3。
保留表如下：
    LEFT OUTER JOIN把左表记为保留表
    RIGHT OUTER JOIN把右表记为保留表
    FULL OUTER JOIN把左右表都作为保留表

在虚拟表 VT2 表的基础上添加保留表中被过滤条件过滤掉的数据，
非保留表中的数据被赋予NULL值，最后生成虚拟表 VT3。
如果FROM子句包含两个以上的表，则对上一个联接生成的结果表和下一个表重复执行步骤1~3，
直到处理完所有的表为止。

4 WHERE 应用WEHRE过滤器
对虚拟表 VT3应用WHERE筛选器。根据指定的条件对数据进行筛选，并把满足的数据插入虚拟表 VT4。
    由于数据还没有分组，因此现在还不能在WHERE过滤器中使用聚合函数对分组统计的过滤。
    同时，由于还没有进行列的选取操作，因此在SELECT中使用列的别名也是不被允许的。

5 GROUP BY 分组
按GROUP BY子句中的列/列表将虚拟表 VT4中的行唯一的值组合成为一组，生成虚拟表VT5。
如果应用了GROUP BY，那么后面的所有步骤都只能得到的虚拟表VT5的列或者是聚合函数（count、sum、avg等）。
原因在于最终的结果集中只为每个组包含一行。

同时，从这一步开始，后面的语句中都可以使用SELECT中的别名。

6 AGG_FUNC 计算聚合函数
SQL Aggregate 函数计算从列中取得的值，返回一个单一的值。常用的 Aggregate 函数包涵以下几种：
    AVG：返回平均值
    COUNT：返回行数
    FIRST：返回第一个记录的值
    LAST：返回最后一个记录的值
    MAX： 返回最大值
    MIN：返回最小值
    SUM： 返回总和

7 WITH 应用ROLLUP或CUBE
对虚拟表 VT5应用ROLLUP或CUBE选项，生成虚拟表 VT6。
CUBE 和 ROLLUP 区别如下：

    CUBE 生成的结果数据集显示了所选列中值的所有组合的聚合。
    ROLLUP 生成的结果数据集显示了所选列中值的某一层次结构的聚合。

8 HAVING 应用HAVING过滤器
对虚拟表VT6应用HAVING筛选器。
根据指定的条件对数据进行筛选，并把满足的数据插入虚拟表VT7。
HAVING 语句在SQL中的主要作用与WHERE语句作用是相同的，但是HAVING是过滤聚合值，
在 SQL 中增加 HAVING 子句原因就是，WHERE 关键字无法与聚合函数一起使用，
HAVING子句主要和GROUP BY子句配合使用。

9 SELECT 选出指定列
将虚拟表 VT7中的在SELECT中出现的列筛选出来，并对字段进行处理，
计算SELECT子句中的表达式，产生虚拟表 VT8。

10 DISTINCT 行去重
将重复的行从虚拟表 VT8中移除，产生虚拟表 VT9。
DISTINCT用来删除重复行，只保留唯一的。

11 ORDER BY 排列
将虚拟表 VT9中的行按ORDER BY 子句中的列/列表排序，生成游标 VC10 ，注意不是虚拟表。
因此使用 ORDER BY 子句查询不能应用于表达式。
同时，ORDER BY子句的执行顺序为从左到右排序，是非常消耗资源的。

12 LIMIT/OFFSET 指定返回行
从VC10的开始处选择指定数量行，生成虚拟表 VT11，并返回调用者。
```


 

### SQL25 四连表-员工经理薪水比较

```MySQL 8.0
drop table if exists  `dept_emp` ; 
drop table if exists  `dept_manager` ; 
drop table if exists  `salaries` ; 

-- 有一个，部门关系表dept_emp简况如下: 
CREATE TABLE `dept_emp` (
`emp_no` int(11) NOT NULL,
`dept_no` char(4) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));

-- 有一个部门经理表dept_manager简况如下:
CREATE TABLE `dept_manager` (
`dept_no` char(4) NOT NULL,
`emp_no` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));

-- 有一个薪水表salaries简况如下: 
CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));

-- 获取员工其当前的薪水比其manager当前薪水还高的相关信息，
-- 第一列给出员工的emp_no，
-- 第二列给出其manager的manager_no，
-- 第三列给出该员工当前的薪水emp_salary,
-- 第四列给该员工对应的manager当前的薪水manager_salary.
INSERT INTO dept_emp VALUES(10001,'d001','1986-06-26','9999-01-01');
INSERT INTO dept_emp VALUES(10002,'d001','1996-08-03','9999-01-01');
INSERT INTO dept_emp VALUES(10003,'d004','1995-12-03','9999-01-01');
INSERT INTO dept_emp VALUES(10004,'d004','1986-12-01','9999-01-01');
INSERT INTO dept_emp VALUES(10005,'d003','1989-09-12','9999-01-01');
INSERT INTO dept_emp VALUES(10006,'d002','1990-08-05','9999-01-01');
INSERT INTO dept_emp VALUES(10007,'d005','1989-02-10','9999-01-01');
INSERT INTO dept_emp VALUES(10008,'d005','1998-03-11','9999-01-01');
INSERT INTO dept_emp VALUES(10009,'d006','1985-02-18','9999-01-01');
INSERT INTO dept_emp VALUES(10010,'d006','2000-06-26','9999-01-01');
INSERT INTO dept_manager VALUES('d001',10002,'1996-08-03','9999-01-01');
INSERT INTO dept_manager VALUES('d002',10006,'1990-08-05','9999-01-01');
INSERT INTO dept_manager VALUES('d003',10005,'1989-09-12','9999-01-01');
INSERT INTO dept_manager VALUES('d004',10004,'1986-12-01','9999-01-01');
INSERT INTO dept_manager VALUES('d005',10010,'1996-11-24','9999-01-01');
INSERT INTO dept_manager VALUES('d006',10010,'2000-06-26','9999-01-01');
INSERT INTO salaries VALUES(10001,88958,'2002-06-22','9999-01-01');
INSERT INTO salaries VALUES(10002,72527,'2001-08-02','9999-01-01');
INSERT INTO salaries VALUES(10003,43311,'2001-12-01','9999-01-01');
INSERT INTO salaries VALUES(10004,74057,'2001-11-27','9999-01-01');
INSERT INTO salaries VALUES(10005,94692,'2001-09-09','9999-01-01');
INSERT INTO salaries VALUES(10006,43311,'2001-08-02','9999-01-01');
INSERT INTO salaries VALUES(10007,88070,'2002-02-07','9999-01-01');
INSERT INTO salaries VALUES(10009,95409,'2002-02-14','9999-01-01');
INSERT INTO salaries VALUES(10010,94409,'2001-11-23','9999-01-01');
INSERT INTO salaries VALUES(10011,25828,'1990-01-22','9999-01-01');
```

- Solution
- 四连表-筛选员工薪水 > 经理薪水
```MySQL 8.0
select de.emp_no, dm.emp_no as manager_no,
sa.salary emp_salary, sa2.salary manager_salary
from dept_emp de
left join dept_manager dm on de.dept_no = dm.dept_no -- refer to where 
left join salaries sa on de.emp_no = sa.emp_no -- emp_salary
left join salaries sa2 on dm.emp_no = sa2.emp_no -- manager_sa
where de.emp_no != dm.emp_no -- 筛出经理-经理行，形成职工-经理行
and sa.salary > sa2.salary
and sa.to_date = '9999-01-01' -- 保证是当前职工
and sa2.to_date = '9999-01-01';
```




### SQL21 select子查询-动态查询
```MySQL 8.0
drop table if exists  `employees` ; 
drop table if exists  `salaries` ;

-- 有一个员工表employees简况如下:
CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));

-- 有一个薪水表salaries简况如下:
CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));

-- 请你查找所有员工自入职以来的薪水涨幅情况，
-- 给出员工编号emp_no以及其对应的薪水涨幅growth，
-- 并按照growth进行升序
-- 注:可能有employees表和salaries表里存在记录的员工，有对应的员工编号和涨薪记录，但是已经离职了，
-- 离职的员工salaries表的最新的to_date!='9999-01-01'，这样的数据不显示在查找结果里面
INSERT INTO employees VALUES(10001,'1953-09-02','Georgi','Facello','M','1986-06-26');
INSERT INTO employees VALUES(10002,'1964-06-02','Bezalel','Simmel','F','1985-11-21');
INSERT INTO employees VALUES(10003,'1959-12-03','Parto','Bamford','M','1986-08-28');
INSERT INTO employees VALUES(10004,'1954-05-01','Chirstian','Koblick','M','1986-12-01');
INSERT INTO employees VALUES(10005,'1955-01-21','Kyoichi','Maliniak','M','1989-09-12');
INSERT INTO employees VALUES(10007,'1957-05-23','Tzvetan','Zielinski','F','1989-02-10');
INSERT INTO employees VALUES(10008,'1958-02-19','Saniya','Kalloufi','M','1994-09-15');
INSERT INTO employees VALUES(10009,'1952-04-19','Sumant','Peac','F','1985-02-18');
INSERT INTO salaries VALUES(10001,60117,'1986-06-26','1987-06-26');
INSERT INTO salaries VALUES(10001,62102,'1987-06-26','1988-06-25');
INSERT INTO salaries VALUES(10001,66074,'1988-06-25','1989-06-25');
INSERT INTO salaries VALUES(10001,66596,'1989-06-25','1990-06-25');
INSERT INTO salaries VALUES(10001,66961,'1990-06-25','1991-06-25');
INSERT INTO salaries VALUES(10001,71046,'1991-06-25','1992-06-24');
INSERT INTO salaries VALUES(10001,74333,'1992-06-24','1993-06-24');
INSERT INTO salaries VALUES(10001,75286,'1993-06-24','1994-06-24');
INSERT INTO salaries VALUES(10001,75994,'1994-06-24','1995-06-24');
INSERT INTO salaries VALUES(10001,76884,'1995-06-24','1996-06-23');
INSERT INTO salaries VALUES(10001,80013,'1996-06-23','1997-06-23');
INSERT INTO salaries VALUES(10001,81025,'1997-06-23','1998-06-23');
INSERT INTO salaries VALUES(10001,81097,'1998-06-23','1999-06-23');
INSERT INTO salaries VALUES(10001,84917,'1999-06-23','2000-06-22');
INSERT INTO salaries VALUES(10001,85112,'2000-06-22','2001-06-22');
INSERT INTO salaries VALUES(10001,85097,'2001-06-22','2002-06-22');
INSERT INTO salaries VALUES(10001,88958,'2002-06-22','9999-01-01');
INSERT INTO salaries VALUES(10002,72527,'1996-08-03','1997-08-03');
INSERT INTO salaries VALUES(10002,72527,'1997-08-03','1998-08-03');
INSERT INTO salaries VALUES(10002,72527,'1998-08-03','1999-08-03');
INSERT INTO salaries VALUES(10002,72527,'1999-08-03','2000-08-02');
INSERT INTO salaries VALUES(10002,72527,'2000-08-02','2001-08-02');
INSERT INTO salaries VALUES(10002,72527,'2001-08-02','9999-01-01');
INSERT INTO salaries VALUES(10003,40006,'1995-12-03','1996-12-02');
INSERT INTO salaries VALUES(10003,43616,'1996-12-02','1997-12-02');
INSERT INTO salaries VALUES(10003,43466,'1997-12-02','1998-12-02');
INSERT INTO salaries VALUES(10003,43636,'1998-12-02','1999-12-02');
INSERT INTO salaries VALUES(10003,43478,'1999-12-02','2000-12-01');
INSERT INTO salaries VALUES(10003,43699,'2000-12-01','2001-12-01');
INSERT INTO salaries VALUES(10003,43311,'2001-12-01','9999-01-01');
INSERT INTO salaries VALUES(10004,40054,'1986-12-01','1987-12-01');
INSERT INTO salaries VALUES(10004,42283,'1987-12-01','1988-11-30');
INSERT INTO salaries VALUES(10004,42542,'1988-11-30','1989-11-30');
INSERT INTO salaries VALUES(10004,46065,'1989-11-30','1990-11-30');
INSERT INTO salaries VALUES(10004,48271,'1990-11-30','1991-11-30');
INSERT INTO salaries VALUES(10004,50594,'1991-11-30','1992-11-29');
INSERT INTO salaries VALUES(10004,52119,'1992-11-29','1993-11-29');
INSERT INTO salaries VALUES(10004,54693,'1993-11-29','1994-11-29');
INSERT INTO salaries VALUES(10004,58326,'1994-11-29','1995-11-29');
INSERT INTO salaries VALUES(10004,60770,'1995-11-29','1996-11-28');
INSERT INTO salaries VALUES(10004,62566,'1996-11-28','1997-11-28');
INSERT INTO salaries VALUES(10004,64340,'1997-11-28','1998-11-28');
INSERT INTO salaries VALUES(10004,67096,'1998-11-28','1999-11-28');
INSERT INTO salaries VALUES(10004,69722,'1999-11-28','2000-11-27');
INSERT INTO salaries VALUES(10004,70698,'2000-11-27','2001-11-27');
INSERT INTO salaries VALUES(10004,74057,'2001-11-27','9999-01-01');
INSERT INTO salaries VALUES(10005,78228,'1989-09-12','1990-09-12');
INSERT INTO salaries VALUES(10005,82621,'1990-09-12','1991-09-12');
INSERT INTO salaries VALUES(10005,83735,'1991-09-12','1992-09-11');
INSERT INTO salaries VALUES(10005,85572,'1992-09-11','1993-09-11');
INSERT INTO salaries VALUES(10005,85076,'1993-09-11','1994-09-11');
INSERT INTO salaries VALUES(10005,86050,'1994-09-11','1995-09-11');
INSERT INTO salaries VALUES(10005,88448,'1995-09-11','1996-09-10');
INSERT INTO salaries VALUES(10005,88063,'1996-09-10','1997-09-10');
INSERT INTO salaries VALUES(10005,89724,'1997-09-10','1998-09-10');
INSERT INTO salaries VALUES(10005,90392,'1998-09-10','1999-09-10');
INSERT INTO salaries VALUES(10005,90531,'1999-09-10','2000-09-09');
INSERT INTO salaries VALUES(10005,91453,'2000-09-09','2001-09-09');
INSERT INTO salaries VALUES(10005,94692,'2001-09-09','9999-01-01');
INSERT INTO salaries VALUES(10007,56724,'1989-02-10','1990-02-10');
INSERT INTO salaries VALUES(10007,60740,'1990-02-10','1991-02-10');
INSERT INTO salaries VALUES(10007,62745,'1991-02-10','1992-02-10');
INSERT INTO salaries VALUES(10007,63475,'1992-02-10','1993-02-09');
INSERT INTO salaries VALUES(10007,63208,'1993-02-09','1994-02-09');
INSERT INTO salaries VALUES(10007,64563,'1994-02-09','1995-02-09');
INSERT INTO salaries VALUES(10007,68833,'1995-02-09','1996-02-09');
INSERT INTO salaries VALUES(10007,70220,'1996-02-09','1997-02-08');
INSERT INTO salaries VALUES(10007,73362,'1997-02-08','1998-02-08');
INSERT INTO salaries VALUES(10007,75582,'1998-02-08','1999-02-08');
INSERT INTO salaries VALUES(10007,79513,'1999-02-08','2000-02-08');
INSERT INTO salaries VALUES(10007,80083,'2000-02-08','2001-02-07');
INSERT INTO salaries VALUES(10007,84456,'2001-02-07','2002-02-07');
INSERT INTO salaries VALUES(10007,88070,'2002-02-07','9999-01-01');
INSERT INTO salaries VALUES(10008,46671,'1998-03-11','1999-03-11');
INSERT INTO salaries VALUES(10008,48584,'1999-03-11','2000-03-10');
INSERT INTO salaries VALUES(10008,52668,'2000-03-10','2000-07-31');
INSERT INTO salaries VALUES(10009,60929,'1985-02-18','1986-02-18');
INSERT INTO salaries VALUES(10009,64604,'1986-02-18','1987-02-18');
INSERT INTO salaries VALUES(10009,64780,'1987-02-18','1988-02-18');
INSERT INTO salaries VALUES(10009,66302,'1988-02-18','1989-02-17');
INSERT INTO salaries VALUES(10009,69042,'1989-02-17','1990-02-17');
INSERT INTO salaries VALUES(10009,70889,'1990-02-17','1991-02-17');
INSERT INTO salaries VALUES(10009,71434,'1991-02-17','1992-02-17');
INSERT INTO salaries VALUES(10009,74612,'1992-02-17','1993-02-16');
INSERT INTO salaries VALUES(10009,76518,'1993-02-16','1994-02-16');
INSERT INTO salaries VALUES(10009,78335,'1994-02-16','1995-02-16');
INSERT INTO salaries VALUES(10009,80944,'1995-02-16','1996-02-16');
INSERT INTO salaries VALUES(10009,82507,'1996-02-16','1997-02-15');
INSERT INTO salaries VALUES(10009,85875,'1997-02-15','1998-02-15');
INSERT INTO salaries VALUES(10009,89324,'1998-02-15','1999-02-15');
INSERT INTO salaries VALUES(10009,90668,'1999-02-15','2000-02-15');
INSERT INTO salaries VALUES(10009,93507,'2000-02-15','2001-02-14');
INSERT INTO salaries VALUES(10009,94443,'2001-02-14','2002-02-14');
INSERT INTO salaries VALUES(10009,95409,'2002-02-14','9999-01-01');
INSERT INTO salaries VALUES(10002,72527,'1985-11-21','1996-08-03');
INSERT INTO salaries VALUES(10003,15828,'1986-08-28','1995-12-03');
INSERT INTO salaries VALUES(10008,25828,'1994-09-15','1998-03-11');
```

- Solution
- select subquery 与动态查询
- 作为大表 select 中的临时表，可以用大表 e, s 有关元素与本表 sa 元素关联，甚至做加减法

```MySQL
select 
    e.emp_no,
    (select s.salary-sa.salary from salaries sa
    where sa.emp_no = e.emp_no and sa.from_date = e.hire_date
    ) as growth -- 作为大表 select 中的临时表，可以用大表 e, s 有关元素与本表 sa 元素关联，甚至做加减法
from employees e left join salaries s -- 大表
on e.emp_no = s.emp_no
where to_date = '9999-01-01'
order by growth;
```  

### SQL22 动态查询参考2
```MySQL

drop table if exists  `departments` ; 
drop table if exists  `dept_emp` ; 
drop table if exists  `salaries` ; 

-- 有一个部门表departments简况如下: 
CREATE TABLE `departments` (
`dept_no` char(4) NOT NULL,
`dept_name` varchar(40) NOT NULL,
PRIMARY KEY (`dept_no`));

-- 有一个部门员工关系表dept_emp简况如下
CREATE TABLE `dept_emp` (
`emp_no` int(11) NOT NULL,
`dept_no` char(4) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));

--有一个薪水表salaries简况如下:
CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));

--请你统计各个部门的工资记录数，给出部门编码dept_no、部门名称dept_name
--以及部门在salaries表里面有多少条记录sum，按照dept_no升序排序
INSERT INTO departments VALUES('d001','Marketing');
INSERT INTO departments VALUES('d002','Finance');
INSERT INTO departments VALUES('d003','Human Resources');
INSERT INTO departments VALUES('d004','Production');
INSERT INTO departments VALUES('d005','Development');
INSERT INTO departments VALUES('d006','Quality Management');
INSERT INTO dept_emp VALUES(10001,'d001','1986-06-26','9999-01-01');
INSERT INTO dept_emp VALUES(10002,'d001','1996-08-03','9999-01-01');
INSERT INTO dept_emp VALUES(10003,'d004','1995-12-03','9999-01-01');
INSERT INTO dept_emp VALUES(10004,'d004','1986-12-01','9999-01-01');
INSERT INTO dept_emp VALUES(10005,'d003','1989-09-12','9999-01-01');
INSERT INTO dept_emp VALUES(10006,'d002','1990-08-05','9999-01-01');
INSERT INTO dept_emp VALUES(10007,'d005','1989-02-10','9999-01-01');
INSERT INTO dept_emp VALUES(10008,'d005','1998-03-11','2000-07-31');
INSERT INTO dept_emp VALUES(10009,'d006','1985-02-18','9999-01-01');
INSERT INTO dept_emp VALUES(10010,'d005','1996-11-24','2000-06-26');
INSERT INTO dept_emp VALUES(10010,'d006','2000-06-26','9999-01-01');
INSERT INTO salaries VALUES(10001,60117,'1986-06-26','1987-06-26');
INSERT INTO salaries VALUES(10001,62102,'1987-06-26','1988-06-25');
INSERT INTO salaries VALUES(10001,66074,'1988-06-25','1989-06-25');
INSERT INTO salaries VALUES(10001,66596,'1989-06-25','1990-06-25');
INSERT INTO salaries VALUES(10001,66961,'1990-06-25','1991-06-25');
INSERT INTO salaries VALUES(10001,71046,'1991-06-25','1992-06-24');
INSERT INTO salaries VALUES(10001,74333,'1992-06-24','1993-06-24');
INSERT INTO salaries VALUES(10001,75286,'1993-06-24','1994-06-24');
INSERT INTO salaries VALUES(10001,75994,'1994-06-24','1995-06-24');
INSERT INTO salaries VALUES(10001,76884,'1995-06-24','1996-06-23');
INSERT INTO salaries VALUES(10001,80013,'1996-06-23','1997-06-23');
INSERT INTO salaries VALUES(10001,81025,'1997-06-23','1998-06-23');
INSERT INTO salaries VALUES(10001,81097,'1998-06-23','1999-06-23');
INSERT INTO salaries VALUES(10001,84917,'1999-06-23','2000-06-22');
INSERT INTO salaries VALUES(10001,85112,'2000-06-22','2001-06-22');
INSERT INTO salaries VALUES(10001,85097,'2001-06-22','2002-06-22');
INSERT INTO salaries VALUES(10001,88958,'2002-06-22','9999-01-01');
INSERT INTO salaries VALUES(10002,72527,'1996-08-03','1997-08-03');
INSERT INTO salaries VALUES(10002,72527,'1997-08-03','1998-08-03');
INSERT INTO salaries VALUES(10002,72527,'1998-08-03','1999-08-03');
INSERT INTO salaries VALUES(10002,72527,'1999-08-03','2000-08-02');
INSERT INTO salaries VALUES(10002,72527,'2000-08-02','2001-08-02');
INSERT INTO salaries VALUES(10002,72527,'2001-08-02','9999-01-01');
INSERT INTO salaries VALUES(10003,40006,'1995-12-03','1996-12-02');
INSERT INTO salaries VALUES(10003,43616,'1996-12-02','1997-12-02');
INSERT INTO salaries VALUES(10003,43466,'1997-12-02','1998-12-02');
INSERT INTO salaries VALUES(10003,43636,'1998-12-02','1999-12-02');
INSERT INTO salaries VALUES(10003,43478,'1999-12-02','2000-12-01');
INSERT INTO salaries VALUES(10003,43699,'2000-12-01','2001-12-01');
INSERT INTO salaries VALUES(10003,43311,'2001-12-01','9999-01-01');
INSERT INTO salaries VALUES(10004,40054,'1986-12-01','1987-12-01');
INSERT INTO salaries VALUES(10004,42283,'1987-12-01','1988-11-30');
INSERT INTO salaries VALUES(10004,42542,'1988-11-30','1989-11-30');
INSERT INTO salaries VALUES(10004,46065,'1989-11-30','1990-11-30');
INSERT INTO salaries VALUES(10004,48271,'1990-11-30','1991-11-30');
INSERT INTO salaries VALUES(10004,50594,'1991-11-30','1992-11-29');
INSERT INTO salaries VALUES(10004,52119,'1992-11-29','1993-11-29');
INSERT INTO salaries VALUES(10004,54693,'1993-11-29','1994-11-29');
INSERT INTO salaries VALUES(10004,58326,'1994-11-29','1995-11-29');
INSERT INTO salaries VALUES(10004,60770,'1995-11-29','1996-11-28');
INSERT INTO salaries VALUES(10004,62566,'1996-11-28','1997-11-28');
INSERT INTO salaries VALUES(10004,64340,'1997-11-28','1998-11-28');
INSERT INTO salaries VALUES(10004,67096,'1998-11-28','1999-11-28');
INSERT INTO salaries VALUES(10004,69722,'1999-11-28','2000-11-27');
INSERT INTO salaries VALUES(10004,70698,'2000-11-27','2001-11-27');
INSERT INTO salaries VALUES(10004,74057,'2001-11-27','9999-01-01');
INSERT INTO salaries VALUES(10005,78228,'1989-09-12','1990-09-12');
INSERT INTO salaries VALUES(10005,82621,'1990-09-12','1991-09-12');
INSERT INTO salaries VALUES(10005,83735,'1991-09-12','1992-09-11');
INSERT INTO salaries VALUES(10005,85572,'1992-09-11','1993-09-11');
INSERT INTO salaries VALUES(10005,85076,'1993-09-11','1994-09-11');
INSERT INTO salaries VALUES(10005,86050,'1994-09-11','1995-09-11');
INSERT INTO salaries VALUES(10005,88448,'1995-09-11','1996-09-10');
INSERT INTO salaries VALUES(10005,88063,'1996-09-10','1997-09-10');
INSERT INTO salaries VALUES(10005,89724,'1997-09-10','1998-09-10');
INSERT INTO salaries VALUES(10005,90392,'1998-09-10','1999-09-10');
INSERT INTO salaries VALUES(10005,90531,'1999-09-10','2000-09-09');
INSERT INTO salaries VALUES(10005,91453,'2000-09-09','2001-09-09');
INSERT INTO salaries VALUES(10005,94692,'2001-09-09','9999-01-01');
INSERT INTO salaries VALUES(10006,43311,'1990-08-05','1991-08-05');
INSERT INTO salaries VALUES(10006,43311,'1991-08-05','1992-08-04');
INSERT INTO salaries VALUES(10006,43311,'1992-08-04','1993-08-04');
INSERT INTO salaries VALUES(10006,43311,'1993-08-04','1994-08-04');
INSERT INTO salaries VALUES(10006,43311,'1994-08-04','1995-08-04');
INSERT INTO salaries VALUES(10006,43311,'1995-08-04','1996-08-03');
INSERT INTO salaries VALUES(10006,43311,'1996-08-03','1997-08-03');
INSERT INTO salaries VALUES(10006,43311,'1997-08-03','1998-08-03');
INSERT INTO salaries VALUES(10006,43311,'1998-08-03','1999-08-03');
INSERT INTO salaries VALUES(10006,43311,'1999-08-03','2000-08-02');
INSERT INTO salaries VALUES(10006,43311,'2000-08-02','2001-08-02');
INSERT INTO salaries VALUES(10006,43311,'2001-08-02','9999-01-01');
INSERT INTO salaries VALUES(10007,56724,'1989-02-10','1990-02-10');
INSERT INTO salaries VALUES(10007,60740,'1990-02-10','1991-02-10');
INSERT INTO salaries VALUES(10007,62745,'1991-02-10','1992-02-10');
INSERT INTO salaries VALUES(10007,63475,'1992-02-10','1993-02-09');
INSERT INTO salaries VALUES(10007,63208,'1993-02-09','1994-02-09');
INSERT INTO salaries VALUES(10007,64563,'1994-02-09','1995-02-09');
INSERT INTO salaries VALUES(10007,68833,'1995-02-09','1996-02-09');
INSERT INTO salaries VALUES(10007,70220,'1996-02-09','1997-02-08');
INSERT INTO salaries VALUES(10007,73362,'1997-02-08','1998-02-08');
INSERT INTO salaries VALUES(10007,75582,'1998-02-08','1999-02-08');
INSERT INTO salaries VALUES(10007,79513,'1999-02-08','2000-02-08');
INSERT INTO salaries VALUES(10007,80083,'2000-02-08','2001-02-07');
INSERT INTO salaries VALUES(10007,84456,'2001-02-07','2002-02-07');
INSERT INTO salaries VALUES(10007,88070,'2002-02-07','9999-01-01');
INSERT INTO salaries VALUES(10008,46671,'1998-03-11','1999-03-11');
INSERT INTO salaries VALUES(10008,48584,'1999-03-11','2000-03-10');
INSERT INTO salaries VALUES(10008,52668,'2000-03-10','2000-07-31');
INSERT INTO salaries VALUES(10009,60929,'1985-02-18','1986-02-18');
INSERT INTO salaries VALUES(10009,64604,'1986-02-18','1987-02-18');
INSERT INTO salaries VALUES(10009,64780,'1987-02-18','1988-02-18');
INSERT INTO salaries VALUES(10009,66302,'1988-02-18','1989-02-17');
INSERT INTO salaries VALUES(10009,69042,'1989-02-17','1990-02-17');
INSERT INTO salaries VALUES(10009,70889,'1990-02-17','1991-02-17');
INSERT INTO salaries VALUES(10009,71434,'1991-02-17','1992-02-17');
INSERT INTO salaries VALUES(10009,74612,'1992-02-17','1993-02-16');
INSERT INTO salaries VALUES(10009,76518,'1993-02-16','1994-02-16');
INSERT INTO salaries VALUES(10009,78335,'1994-02-16','1995-02-16');
INSERT INTO salaries VALUES(10009,80944,'1995-02-16','1996-02-16');
INSERT INTO salaries VALUES(10009,82507,'1996-02-16','1997-02-15');
INSERT INTO salaries VALUES(10009,85875,'1997-02-15','1998-02-15');
INSERT INTO salaries VALUES(10009,89324,'1998-02-15','1999-02-15');
INSERT INTO salaries VALUES(10009,90668,'1999-02-15','2000-02-15');
INSERT INTO salaries VALUES(10009,93507,'2000-02-15','2001-02-14');
INSERT INTO salaries VALUES(10009,94443,'2001-02-14','2002-02-14');
INSERT INTO salaries VALUES(10009,95409,'2002-02-14','9999-01-01');
INSERT INTO salaries VALUES(10010,94409,'1996-11-24','1997-11-24');
INSERT INTO salaries VALUES(10010,94409,'1997-11-24','1998-11-24');
INSERT INTO salaries VALUES(10010,94409,'1998-11-24','1999-11-24');
INSERT INTO salaries VALUES(10010,94409,'1999-11-24','2000-11-23');
INSERT INTO salaries VALUES(10010,94409,'2000-11-23','2001-11-23');
INSERT INTO salaries VALUES(10010,94409,'2001-11-23','9999-01-01');
INSERT INTO salaries VALUES(10002,72527,'1985-11-21','1996-08-03');
INSERT INTO salaries VALUES(10003,15828,'1986-08-28','1995-12-03');
INSERT INTO salaries VALUES(10006,43311,'1989-06-02','1990-08-05');
INSERT INTO salaries VALUES(10006,43311,'1994-09-15','1998-03-11');
INSERT INTO salaries VALUES(10010,94409,'1989-08-24','1996-11-24');
INSERT INTO salaries VALUES(10008,25828,'1994-09-15','1998-03-11');
INSERT INTO salaries VALUES(10011,25828,'1990-01-22','9999-01-01');
```
- Solution
```MySQL
-- select 中设置子查询 动态查询
select de.dept_no, 
(select dept_name from departments where dept_no=de.dept_no) dept_name,
count(*)
from dept_emp de

left join salaries sa
on de.emp_no = sa.emp_no
group by de.dept_no
order by de.dept_no;
```

### SQL23 窗口函数
```MySQL
drop table if exists  `salaries` ; 

-- 有一个薪水表salaries简况如下:
CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));

-- 对所有员工的薪水按照salary进行按照1-N的排名，
-- 相同salary并列且按照emp_no升序排列(column:emp_no, salary, t_rank)：
INSERT INTO salaries VALUES(10001,88958,'2002-06-22','9999-01-01');
INSERT INTO salaries VALUES(10002,72527,'2001-08-02','9999-01-01');
INSERT INTO salaries VALUES(10003,43311,'2001-12-01','9999-01-01');
INSERT INTO salaries VALUES(10004,72527,'2001-12-01','9999-01-01');
```
- Solution
  - MySQL四大排名函数 row_number、rank、dense_rank、ntile
  - reference: https://zhuanlan.zhihu.com/p/92654574
```MySQL
select emp_no, salary,

dense_rank() over(order by salary desc) as t_rank 
from salaries 
order by salary desc, emp_no;
```
- 窗口函数
```MySQL
-- 用户变量，在客户端链接到数据库实例整个过程中用户变量都是有效的。
-- MySQL中用户变量不用事前申明，在用的时候直接用“@变量名”使用就可以了。
-- 第一种用法：set @num=1; 或set @num:=1; //这里要使用set语句创建并初始化变量，直接使用@num变量
-- 第二种用法：select @num:=1; 或 select @num:=字段名 from 表名 where ……，
-- select语句一般用来输出用户变量，比如select @变量名，用于输出数据源不是表格的数据。
-- 注意上面两种赋值符号，使用set时可以用“=”或“:=”，但是使用select时必须用“:=赋值”

-- 实现cumsum（累加）的功能
SET @csum := 0;
SELECT 日期, 净利润, (@csum := @csum + 净利润) AS 累计利润
FROM daily_pnl_view;

-- 窗口函数
-- https://zhuanlan.zhihu.com/p/92654574
-- <窗口函数> over (partition by <用于分组的列名>
--                     order by <用于排序的列名>)
SELECT emp_no,salary,
SUM(salary) OVER (ORDER BY emp_no) AS running_total
FROM salaries
WHERE to_date = '9999-01-01';


-- reference:
-- 把所有小于等于当前编号的表s1和当前编号表s2联立起来，
-- 然后按照当前编号分组，计算出所有小于等于
-- 当前标号的工资总数
SELECT s2.emp_no,s2.salary,SUM(s1.salary) AS running_total
FROM salaries AS s1 INNER JOIN salaries AS s2 
ON s1.emp_no <= s2.emp_no
WHERE 
s1.to_date = "9999-01-01"
AND s2.to_date = "9999-01-01"
GROUP BY s2.emp_no

```
### SQL71 按日期统计同一用户累计刷题数
- 窗口函数解法
- 笛卡儿积筛法（refer to above SQL18-SOL23)
  - t1.user_id = t2.user_id 同一用户
  - and t1.date >= t2.date 截至当前某用户累计天数
```MySQL 8.0

CREATE TABLE `login` (
`id` int(4) NOT NULL,
`user_id` int(4) NOT NULL,
`client_id` int(4) NOT NULL,
`date` date NOT NULL,
PRIMARY KEY (`id`));

CREATE TABLE `passing_number` (
`id` int(4) NOT NULL,
`user_id` int(4) NOT NULL,
`number` int(4) NOT NULL,
`date` date NOT NULL,
PRIMARY KEY (`id`));

CREATE TABLE `user` (
`id` int(4) NOT NULL,
`name` varchar(32) NOT NULL,
PRIMARY KEY (`id`));

CREATE TABLE `client` (
`id` int(4) NOT NULL,
`name` varchar(32) NOT NULL,
PRIMARY KEY (`id`));


-- 请你写出一个sql语句查询刷题信息，包括: 
-- 用户的名字，以及截止到某天，累计总共通过了多少题，
-- 并且查询结果先按照日期升序排序，再按照姓名升序排序，
-- 有登录却没有刷题的哪一天的数据不需要输出
INSERT INTO login VALUES
(1,2,1,'2020-10-12'),
(2,3,2,'2020-10-12'),
(3,2,2,'2020-10-13'),
(4,3,2,'2020-10-13');

INSERT INTO passing_number VALUES
(1,2,4,'2020-10-12'),
(2,3,1,'2020-10-12'),
(3,2,0,'2020-10-13'),
(4,3,2,'2020-10-13');

INSERT INTO user VALUES
(1,'tm'),
(2,'fh'),
(3,'wc');

INSERT INTO client VALUES
(1,'pc'),
(2,'ios'),
(3,'anroid'),
(4,'h5');
```
- Solution
```MySQL 8.0
-- 窗口函数汇总累计和
select user.name, tmp.date, tmp.sumn
from (
    -- sum() over(partition by ...) 分组后每行显示每组总和
    -- sum() over(partition by ... order by ...) 分组后每行显示截至当前行累计和
    select user_id, sum(number) over(partition by user_id order by date) sumn, date
    from passing_number
) tmp join user on user.id = tmp.user_id
order by tmp.date, user.name;

-- “截至到某一天，某一用户累计通过多少道题”算法原理
# select pn1.user_id,pn1.date,sum(pn2.number) 
# from passing_number pn1,passing_number pn2 
--  笛卡儿积筛法
  -- t1.user_id = t2.user_id 同一用户
  -- and t1.date >= t2.date 截至当前某用户累计天数
# where pn1.user_id = pn2.user_id and pn1.date>=pn2.date 
# group by pn1.user_id,pn1.date;
```

### SOL18
- 请你查找薪水排名第二多的员工编号emp_no、薪水salary、last_name以及first_name，不能使用order by完成
  - order by 排序实现方法之一
  - 题目
```MySQL
drop table if exists  `employees` ; 
drop table if exists  `salaries` ; 

CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));

CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));

INSERT INTO employees VALUES(10001,'1953-09-02','Georgi','Facello','M','1986-06-26');
INSERT INTO employees VALUES(10002,'1964-06-02','Bezalel','Simmel','F','1985-11-21');
INSERT INTO employees VALUES(10003,'1959-12-03','Parto','Bamford','M','1986-08-28');
INSERT INTO employees VALUES(10004,'1954-05-01','Chirstian','Koblick','M','1986-12-01');

INSERT INTO salaries VALUES(10001,88958,'2002-06-22','9999-01-01');
INSERT INTO salaries VALUES(10002,72527,'2001-08-02','9999-01-01');
INSERT INTO salaries VALUES(10003,43311,'2001-12-01','9999-01-01');
INSERT INTO salaries VALUES(10004,74057,'2001-11-27','9999-01-01');
```
- 题解1：
```MySQL
select s.emp_no, s.salary, e.last_name, e.first_name
from salaries s join employees e
on s.emp_no = e.emp_no
where s.salary =
    (select s1.salary
    from salaries s1 join salaries s2 -- 自链，一对一拼接自身表格
    on s1.salary <= s2.salary --  s1.salary 中每一职工对应多个 s2.salary 中 s1 >= s1 的薪水项·· 
    group by s1.salary 
    having count(distinct s2.salary) = 2); -- 去除s2中重复的salary
``` 
> 自链salary，一对一拼接自身表格
```
s1 	s2
100     100
98 	98
98 	98
95 	95
```
> s1.salary 中每一职工对应多个 s2.salary 中 s1 >= s1 的薪水项

```
s1 	 s2
100      100
98       100
	  98
	  98
95 	 100
	  98
	  98
	  95
```

- 题解2：
```MySQL
select e.emp_no, s.salary, e.last_name, e.first_name
from employees e
left join salaries s
on e.emp_no = s.emp_no
where s.salary = (
    select max(salary) from salaries where
    salary > (select max(salary) from salaries));
```


### SQL61 窗口函数
```MySQl
-- 对于employees表中，输出first_name排名(按first_name升序排序)为奇数的first_name

CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));

如，输入为：
INSERT INTO employees VALUES(10001,'1953-09-02','Georgi','Facello','M','1986-06-26');
INSERT INTO employees VALUES(10002,'1964-06-02','Bezalel','Simmel','F','1985-11-21');
INSERT INTO employees VALUES(10005,'1955-01-21','Kyoichi','Maliniak','M','1989-09-12');
INSERT INTO employees VALUES(10006,'1953-04-20','Anneke','Preusig','F','1989-06-02');
```
- Solution # 窗口函数产生的临时表作为from 表源 #
```MySQL
select first_name
from(select emp_no, first_name,
    dense_rank() over (order by first_name) as odd
    from employees ) tmp --派生表必须加别名 tmp
where odd % 2 = 1
order by emp_no;
```



### SQL32
```MySQL
-- 将employees表的所有员工的last_name和first_name拼接起来作为Name，中间以一个空格区分
-- (注：sqllite,字符串拼接为 || 符号，不支持concat函数，mysql支持concat函数)
CREATE TABLE `employees` ( `emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));
```
- Solution
```MySQL
-- 拼接字符：conact(a, b)
select concat(last_name, ' ', first_name)
from employees;
```


### SQL35
- Solution
```MySQL
-- mysql中常用的三种插入数据的语句:
-- insert into表示插入数据，数据库会检查主键，如果出现重复会报错；
-- replace into表示插入替换数据，需求表中有PrimaryKey，
-- 或者unique索引，如果数据库已经存在数据，则用新数据替换，如果没有数据效果则和insert into一样；
-- insert ignore表示，如果中已经存在相同的记录，则忽略当前新数据

drop table if exists actor;

CREATE TABLE actor (
   actor_id  smallint(5)  NOT NULL PRIMARY KEY,
   first_name  varchar(45) NOT NULL,
   last_name  varchar(45) NOT NULL,
   last_update  DATETIME NOT NULL);
insert ignore into actor values("3","ED","CHASE","2006-02-15 12:34:33");
```

### SQL36 Index
```MySQL
-- 请你创建一个actor_name表(columns:first_name, last_name)
-- 并且将actor表中的所有first_name以及last_name导入该表

-- 根据 actor 表直接创建 actor_name 表，导入数据
create table actor_name
select first_name, last_name from actor;
```
### SQL37 Index
```MySQL
-- 添加主键，索引值必须是唯一的，且不能为NULL
ALTER TABLE tbl_name ADD PRIMARY KEY (col_list);

-- 唯一索引，这条语句创建索引的值必须是唯一的
alter table actor add unique uniq_idx_firstname(first_name);

-- 普通索引，索引值可出现多次
alter table actor add index idx_lastname(last_name);

-- 全文索引
ALTER TABLE tbl_name ADD FULLTEXT index_name (col_list);

-- 删除索引
DROP INDEX index_name ON tbl_name; 
ALTER TABLE tbl_name DROP INDEX index_name；
ALTER TABLE tbl_name DROP PRIMARY KEY;
```

### SQL39 强制索引
```MySQL
CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));

create index idx_emp_no on salaries(emp_no);

-- 强制索引
select * 
from salaries force index(idx_emp_no)
where emp_no='10005';
```

### SQL38 View
```MySQL
-- view 
create view actor_name_view as
select first_name as first_name_v, last_name last_name_v
from actor;
-- or 
CREATE VIEW actor_name_view(first_name_v,last_name_v)
AS SELECT first_name,last_name FROM actor;
-- drop
drop view view_name;
```

### SQL40 在后面增加列并且设置默认值
```MySQL
alter table actor 
add 
create_date datetime not null 
default '2020-10-01 00:00:00'
after last_update;
```

### SQL41 Trigger

### SQL44 2Replace  <br>

```MySQL
-- 1- replace(x, y, z) 函数
update titles_test 
-- emp_no-column 10005-replace-10001
set emp_no = replace(emp_no, 10001, 10005) 
where id = 5;

-- 2- MySQL replace into
replace into titles_test
values ('5', '10005', 'Senior Engineer',
        '1986-06-26',
        '9999-01-01');
```
-2- MySQL replace into 用法（insert into 的增强版）<br>
在向表中插入数据的时候，经常遇到这样的情况：<br>
1. 首先判断数据是否存在
2. 如果不存在，则插入
3. 如果存在，则更新
在 SQL Server 中可以这样处理：
```MySQL
if not exists (select 1 from t where id = 1)
      insert into t(id, update_time) values(1, getdate())
   else
      update t set update_time = getdate() where id = 1
-- ————————————————
-- 版权声明：本文为CSDN博主「risingsun001」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
-- 原文链接：https://blog.csdn.net/risingsun001/article/details/38977797
```
MySQL 中有更简单的方法： replace into
```MySQL
replace into t(id, update_time) values(1, now());
replace into t(id, update_time) select 1, now();
```
replace into 跟 insert 功能类似<br>
不同点在于：replace into 首先尝试插入数据到表中<br>
1. 如果发现表中已经有此行数据（根据主键或者唯一索引判断）则先删除此行数据，然后插入新的数据
2. 否则，直接插入新数据

要注意的是：插入数据的表必须有主键或者是唯一索引！<br>
否则的话，replace into 会直接插入数据，这将导致表中出现重复的数据。<br>
MySQL replace into 有三种形式：
```MySQL
1. replace into tbl_name(col_name, ...) values(...)
2. replace into tbl_name(col_name, ...) select ...
3. replace into tbl_name set col_name=value, ...
-- ————————————————
-- 其中 “into” 关键字可以省略
-- 对于那些没有给予值的列，MySQL 将自动为这些列赋上默认值
```

### SQL45 Alter table...
```MySQL
alter table titles_test rename to titles_2017;
-- ALTER TABLE 表名 ADD 列名/索引/主键/外键等；
-- ALTER TABLE 表名 DROP 列名/索引/主键/外键等；
-- ALTER TABLE 表名 ALTER 仅用来改变某列的默认值；
-- ALTER TABLE 表名 RENAME 列名/索引名 TO 新的列名/新索引名；
-- ALTER TABLE 表名 RENAME TO/AS 新表名;
-- https://dev.mysql.com/doc/refman/8.0/en/alter-table.html
```

### SQL48 Update + Subquery
```MySQL
-- 请你写出更新语句，将所有获取奖金的员工当前的(salaries.to_date='9999-01-01')薪水增加10%
-- (emp_bonus里面的emp_no都是当前获奖的所有员工)
create table emp_bonus(
emp_no int not null,
btype smallint not null);

CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL, PRIMARY KEY (`emp_no`,`from_date`));

INSERT INTO emp_bonus VALUES (10001,1);
INSERT INTO salaries VALUES(10001,85097,'2001-06-22','2002-06-22');
INSERT INTO salaries VALUES(10001,88958,'2002-06-22','9999-01-01'); 
```
- Solution
```MySQL
update salaries set salary=salary+salary*0.1
where to_date = '9999-01-01'
and salary in (
-- both [select * from] and [tmp_salary] lost
-- SQL_ERROR_INFO: "You can't specify target table 'salaries' for update in FROM clause"
    select * from (  
        select sa.salary as sasa from salaries sa
        left join emp_bonus eb 
	-- sigle [tmp_salary] lost 
	-- SQL_ERROR_INFO: 'Every derived table must have its own alias'
        on sa.emp_no = eb.emp_no) tmp_salary);
```


### SQL51

统计字符串长度：<br>
`char_length('string')/char_length(column_name)`<br>
1.返回值为字符串string或者对应字段长度，长度的单位为字符，一个多字节字符（例如，汉字）算作一个单字符<br>
2.不管汉字还是数字或者是字母都算是一个字符<br>
3.任何编码下，多字节字符都算是一个字符<br>

`length('string')/length(column_name)`<br>
1.utf8字符集编码下,一个汉字是算三个字符,一个数字或字母算一个字符<br>
2.其他编码下,一个汉字算两个字符, 一个数字或字母算一个字符<br>

字符串替换：<br>
`REPLACE(s,s1,s2)` 将字符串 s2 替代字符串 s 中的字符串 s1
```MySQL
-- 查找字符串'10,A,B' 中逗号','出现的次数cnt
select (length('10,A,B')-length(replace('10,A,B',',',''))) cnt;
```


### SQL52 截取字符串
  - left/right(string, length)
  - substr(sting, pos, length)
```MySQL
select first_name from employees 
order by right(first_name, 2); -- 从右开始向左，取2个字符

select first_name from employees -- 等价上面语句
order by substr(first_name, -2, 2);

select first_name from employees
order by substr(first_name, -1, 2); -- 正向截取，在最后一个位置，向后取2个字符，但此处只有一个字符，第二个为空
```


### SQL53 group_concat()---group by
```MySQL
-- group_concat([distinct] 要连接的字段 [order by 排序字段 asc/desc] [separator '分隔符'])
-- 分隔符默认“,”
-- group_concat(emp_no ，',') 则会每个字符串后面多一个','---> 10001,,10002,
-- group_concat(emp_no separator',') ---> 10001,10002
select dept_no, group_concat(emp_no)as employees
from dept_emp group by dept_no;
```
### SQL53 avg = (sum()-max()-min())/(count(1)-2)
```MySQL
select (sum(salary)-max(salary)-min(salary))/
(count(1)-2) avg_salary
from salaries where to_date= '9999-01-01';
```

### SQL57 exists
```MySQL
-- 使用含有关键字exists查找未分配具体部门的员工的所有信息

CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));

CREATE TABLE `dept_emp` (
`emp_no` int(11) NOT NULL,
`dept_no` char(4) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));
```
- Solution
```MySQL
select * from employees em
where not exists(
select de.dept_no from dept_emp de
where de.emp_no = em.emp_no);
```


### SQL65 异常邮件概率=(case...when\sum(type="complete"))/count(*)
```MySQL
-- 现在有一个需求，让你统计正常用户发送给正常用户邮件失败的概率:
-- 有一个邮件(email)表，id为主键， type是枚举类型，
-- 枚举成员为(completed，no_completed)，completed代表邮件发送是成功的，
-- no_completed代表邮件是发送失败的。
-- 第1行表示为id为2的用户在2020-01-11成功发送了一封邮件给了id为3的用户
-- 简况如下:
drop table if exists email;
drop table if exists user;
CREATE TABLE `email` (
`id` int(4) NOT NULL,
`send_id` int(4) NOT NULL,
`receive_id` int(4) NOT NULL,
`type` varchar(32) NOT NULL,
`date` date NOT NULL,
PRIMARY KEY (`id`));

-- 下面是一个用户(user)表，id为主键，
-- is_blacklist为0代表为正常用户，
-- is_blacklist为1代表为黑名单用户，
-- 简况如下:
CREATE TABLE `user` (
`id` int(4) NOT NULL,
`is_blacklist` int(4) NOT NULL,
PRIMARY KEY (`id`));

INSERT INTO email VALUES
(1,3,2,'completed','2020-01-11'),
(2,1,3,'completed','2020-01-11'),
(3,1,4,'no_completed','2020-01-11'),
(4,3,1,'completed','2020-01-12'),
(5,3,4,'completed','2020-01-12'),
(6,4,1,'no_completed','2020-01-12'),
(7,1,4,'no_completed','2020-01-11');

-- 第1行表示id为1的是正常用户;
-- 第2行表示id为2的不是正常用户，是黑名单用户，
-- 如果发送大量邮件或者出现各种情况就会容易发送邮件失败的用户.
INSERT INTO user VALUES
(1,0),
(2,1),
(3,0),
(4,0);
```
- Solution
  - 预警 STUPID！
```MySQL 8.0
-- this is STUPID---failure
select tmp1.edate , round(tmp2.nocomp/tmp1.tsum, 3) p
from 
(select email.date edate,
count(email.type) as tsum
from email left join user on email.send_id = user.id
 -- Failure: no user.is_blacklist != email.receive_id
where user.is_blacklist != 1 group by email.date) tmp1
inner join
(select email.date edate2,
 count(email.type) as nocomp
from email left join user on email.send_id = user.id
 --  -- Failure: no user.is_blacklist != email.receive_id
where user.is_blacklist != 1 and email.type = 'no_completed' group by email.date
) tmp2
on tmp1.edate = tmp2.edate2;
```
```MySQL
-- Sucess one: join...on...and...
select e.date, round(
    sum(case when e.type = 'no_completed' then 1 else 0 end)/count(e.type), 3) p
from email e -- 2/3=.67 email.date='2020-01-11'
-- A left join B ... on ...and ... > Failure 2/4=.5 email.date='2020-01-11'
-- bcause of: A 主表，连表A表里面有的项都显示，若B中没有关联项，A中显示NULL 
-- 虽然筛掉了不合条件的项，但是表格中依旧存在此行，只是显示值为 NULL ，所以计算概率时多了一行
-- 此时应该去掉 and，把相关筛选条件放到 where 则成功
-- reference: https://blog.csdn.net/didiliu_1111/article/details/80430957
join user u1 on (e.send_id = u1.id and u1.is_blacklist != 1)
join user u2 on (e.receive_id = u2.id and u2.is_blacklist != 1)
group by e.date order by e.date;

-- left join...on...where
select e.date, round(
    sum(case when e.type = 'no_completed' then 1 else 0 end)/count(e.type), 3) p
from email e
left join user u1 on (e.send_id = u1.id )
left join user u2 on (e.receive_id = u2.id)
where u1.is_blacklist != 1 and u2.is_blacklist != 1
group by e.date order by e.date;

-- Another way
select e.date, round(
    sum(e.type='no_completed')/count(*), 3) p
from email e
join user u1 on (e.send_id = u1.id and u1.is_blacklist != 1)
join user u2 on (e.receive_id = u2.id and u2.is_blacklist != 1)
group by e.date order by e.date;
```

### SQL67 Group by 求聚合函数与条目不匹配问题
```MySQL8.0
drop table if exists login;
drop table if exists user;
drop table if exists client;

-- 登录(login)记录表，简况如下:
CREATE TABLE `login` (
`id` int(4) NOT NULL,
`user_id` int(4) NOT NULL,
`client_id` int(4) NOT NULL,
`date` date NOT NULL,
PRIMARY KEY (`id`));

-- 用户(user)表，简况如下:
CREATE TABLE `user` (
`id` int(4) NOT NULL,
`name` varchar(32) NOT NULL,
PRIMARY KEY (`id`));

-- 客户端(client)表，简况如下:
CREATE TABLE `client` (
`id` int(4) NOT NULL,
`name` varchar(32) NOT NULL,
PRIMARY KEY (`id`));

-- 请你写出一个sql语句查询每个用户最近一天登录的日子，
-- 用户的名字，以及用户用的设备的名字，
-- 并且查询结果按照user的name升序排序

-- select*, max(date) from login group by user_id;
-- result, 分组求max，返回结果max列不与其他列匹配
-- 1 1|2|1|2020-10-12|2020-10-13
-- 2 2|3|2|2020-10-12|2020-10-13
INSERT INTO login VALUES
(1,2,1,'2020-10-12'),
(2,3,2,'2020-10-12'),
(3,2,2,'2020-10-13'),
(4,3,2,'2020-10-13');

INSERT INTO user VALUES
(1,'tm'),
(2,'fh'),
(3,'wangchao');

INSERT INTO client VALUES
(1,'pc'),
(2,'ios'),
(3,'anroid'),
(4,'h5');
```
- Solution
```MySQL
-- 误区
-- max(date)：包括12 13号，最终用where...in...筛选时就出错，不能体现出最大日期
-- 所以where...in...里条件应是：(user_id, max(date))
-- 1 	fh|pc|2020-10-12		
-- 2 	fh|ios|2020-10-13	 1 	fh|ios|2020-10-13
-- 3 	tm|anroid|2020-10-12	 2 	tm|anroid|2020-10-12
-- 4 	wangchao|ios|2020-10-12		
-- 5 	wangchao|ios|2020-10-13	 3 	wangchao|ios|2020-10-13
select  u.name u_n, c.name c_n, date 
from login l 
left join user u on l.user_id = u.id
left join client c on l.client_id = c.id
where date in(
select max(date) from login group by user_id)
order by u.name;


select  u.name u_n, c.name c_n, date 
from login l 
left join user u on l.user_id = u.id
left join client c on l.client_id = c.id
where (user_id, date) in(
select user_id, max(date) from login group by user_id)
order by u.name;
```


### SQL69

- Hard
```MySQL 8.0
-- 牛客每天有很多人登录，请你统计一下牛客每个日期登录新用户个数
-- 并且查询结果按照日期升序排序

select tmp2.date, count(tmp.user_id) new from (
    select user_id, min(date) mdate from login group by user_id) tmp
right join 
    (select distinct date from login) tmp2
on tmp.mdate = tmp2.date
group by tmp2.date;


-- Not undersande
select distinct date,
    sum(case when (user_id, date) in 
       (select user_id, min(date) from login group by user_id)
       then 1 else 0 end)
from login  order by date;
```


## Review
- None

## SQL73 根据表单条件求均值?
```MySQL 8.0
drop table if exists grade;
CREATE TABLE  grade(
`id` int(4) NOT NULL,
`job` varchar(32) NOT NULL,
`score` int(10) NOT NULL,
PRIMARY KEY (`id`));

-- 写一个sql语句查询用户分数大于
-- 其所在工作(job)分数的平均分的所有grade的属性，
-- 并且以id的升序排序
INSERT INTO grade VALUES
(1,'C++',11001),
(2,'C++',10000),
(3,'C++',9000),
(4,'Java',12000),
(5,'Java',13000),
(6,'JS',12000),
(7,'JS',11000),
(8,'JS',9999),
(9,'Java',12500);
```
- Solution 
- 根据表单条件求均值, 不用 join 连接
```MySQL 8.0
select id, job, score from grade a
where score > ( 
    select round(avg(score), 3) from grade b
    where a.job = b.job)
order by id;
```


### SQL75 寻找中位数算法-简洁优雅
```MySQL 8.0
-- 牛客每次考试完，都会有一个成绩表(grade)，如下:
drop table if exists grade;
CREATE TABLE  grade(
`id` int(4) NOT NULL,
`job` varchar(32) NOT NULL,
`score` int(10) NOT NULL,
PRIMARY KEY (`id`));

-- 写一个sql语句查询各个岗位分数升序排列之后的中位数位置的范围，
-- 并且按job升序排序
INSERT INTO grade VALUES
(1,'C++',11001),
(2,'C++',10000),
(3,'C++',9000),
(4,'Java',12000),
(5,'Java',13000),
(6,'B',12000),
(7,'B',11000),
(8,'B',9999);
```
- Solution
```MySQL 8.0
select job,
    floor((count(*) + 1)/ 2) as 'start',
    floor((count(*) + 2)/ 2) as 'end'
from grade
group by job
order by job;
```
- SQL76 奇妙解法
- 写一个sql语句查询各个岗位分数的中位数位置上的所有grade信息，
- 并且按id升序排序(题干同上）
  - 无论奇偶:`[中位数的位置]-[(每类job总个数+1)/2] < 1`
```MySQL 8.0
select id, job, score, r_rank
from (select *,
     (row_number() over (partition by job order by score desc)) r_rank,
     (count(score) over (partition by job)) num
     from grade ) t1
where abs(t1.r_rank-(t1.num+1)/2) < 1
order by id;
```
### SQL88 分组后寻找中位数
```MySQL 8.0
drop table if exists class_grade;
CREATE TABLE class_grade (
grade varchar(32) NOT NULL,
number int(4) NOT NULL
);

INSERT INTO class_grade VALUES
('A',2),
('C',4),
('B',4),
('D',2);

```
- Solution 
- 正序/2 和 逆序/2 取交集得到中位数

``` MySQL 8.0
select grade from(
select grade, 
    (select sum(number)/2 from class_grade) t,
    sum(number) over(order by grade) a, 
    sum(number) over(order by grade desc) b
    from class_grade ) tmp
where a >= t and b >= t
order by grade;
```

## 排序专题
### SQL91 if函数转换负数，窗口函数定名次
```MySQL 8.0
drop table if exists user;
drop table if exists grade_info;

CREATE TABLE user (
id  int(4) NOT NULL,
name varchar(32) NOT NULL
);

CREATE TABLE grade_info (
user_id  int(4) NOT NULL,
grade_num int(4) NOT NULL,
type varchar(32) NOT NULL
);

INSERT INTO user VALUES
(1,'tm'),
(2,'wwy'),
(3,'zk'),
(4,'qq'),
(5,'lm');

INSERT INTO grade_info VALUES
(1,3,'add'),
(2,3,'add'),
(1,1,'reduce'),
(3,3,'add'),
(4,3,'add'),
(5,3,'add'),
(3,1,'reduce');

-- 请你写一个SQL查找积分最高的用户的id，
-- 名字，以及他的总积分是多少(可能有多个)，
-- 查询结果按照id升序排序
-- result
2|wwy|3
4|qq|3
5|lm|3
```
- Solution 
```MySQL 8.0
select t2.tid, u.name, t2.tsum
from (select
      user_id tid, 
      sumg tsum, 
      dense_rank() over(order by t.sumg desc) grade_sum
      from (select user_id, 
            sum(if(type='reduce', -grade_num, grade_num)) sumg
            from grade_info group by user_id
           ) t
     ) t2
join user u on u.id = t2.tid
where t2.grade_sum = 1;
```

### SQL81 订单分析之找一二次购买时间
- 排序问题：寻找第一，第二，第三...通用算法
- 寻找中位数 SQL75
```MySQL 8.0

drop table if exists order_info;

-- 有很多同学在牛客购买课程来学习，
-- 购买会产生订单存到数据库里。
-- 有一个订单信息表(order_info):
CREATE TABLE order_info (
id int(4) NOT NULL,
user_id int(11) NOT NULL,
product_name varchar(256) NOT NULL,
status varchar(32) NOT NULL,
client_id int(4) NOT NULL,
date date NOT NULL,
PRIMARY KEY (id));

-- 请你写出一个sql语句查询在2025-10-15以后，
-- 如果有一个用户下单2个以及2个以上状态为购买成功的C++课程或Java课程或Python课程，
-- 那么输出这个用户的user_id，
-- 以及满足前面条件的第一次购买成功的C++课程或Java课程或Python课程的日期first_buy_date，
-- 以及满足前面条件的第二次购买成功的C++课程或Java课程或Python课程的日期second_buy_date，
-- 以及购买成功的C++课程或Java课程或Python课程的次数cnt，并且输出结果按照user_id升序排序
INSERT INTO order_info VALUES
(1,557336,'C++','no_completed',1,'2025-10-10'),
(2,230173543,'Python','completed',2,'2025-10-12'),
(3,57,'JS','completed',3,'2025-10-23'),
(4,57,'C++','completed',3,'2025-10-23'),
(5,557336,'Java','completed',1,'2025-10-23'),
(6,57,'Java','completed',1,'2025-10-24'),
(7,557336,'C++','completed',1,'2025-10-25'),
(8,557336,'Python','completed',1,'2025-10-26');

-- 输出：
57|2025-10-23|2025-10-24|2
557336|2025-10-23|2025-10-25|3
```
- Solution
```MySQL 8.0
select user_id,
-- 
max(case when tmp.r=1 then tmp.date else 0 end) first_buy_date,
max(case when tmp.r=2 then tmp.date else 0 end) second_buy_date,
tmp.cnt
from(select
    user_id,
    date,
    row_number() over(partition by user_id order by date) as r,
    -- 
    count(*) over(partition by user_id) as cnt
    from order_info
    where date>'2025-10-15'
    and status='completed'
    and product_name in ('C++','Python','Java')) tmp 
--
where tmp.cnt>=2
group by tmp.user_id, tmp.cnt
order by tmp.user_id;
```

### SQL78
- usage: where group having 
```MySQL 8.0
select user_id 
from order_info
where date>'2025-10-15'
and product_name in ('C++', 'Python', 'Java')
and status="completed"

group by user_id
having count(product_name)>1
order by user_id;
```

### SQL83

```MySQL 8.0
drop table if exists order_info;
drop table if exists client;
CREATE TABLE order_info (
id int(4) NOT NULL,
user_id int(11) NOT NULL,
product_name varchar(256) NOT NULL,
status varchar(32) NOT NULL,
client_id int(4) NOT NULL,
date date NOT NULL,
is_group_buy varchar(32) NOT NULL,
PRIMARY KEY (id));

CREATE TABLE client(
id int(4) NOT NULL,
name varchar(32) NOT NULL,
PRIMARY KEY (id)
);

-- 请你写出一个sql语句查询在2025-10-15以后，
-- 同一个用户下单2个以及2个以上状态为购买成功的C++课程或Java课程或Python课程的来源信息，
-- 第一列是显示的是客户端名字，如果是拼团订单则显示GroupBuy，
-- 第二列显示这个客户端(或者是拼团订单)有多少订单，
-- 最后结果按照第一列(source)升序排序
INSERT INTO order_info VALUES
(1,557336,'C++','no_completed',1,'2025-10-10','No'),
(2,230173543,'Python','completed',2,'2025-10-12','No'),
(3,57,'JS','completed',0,'2025-10-23','Yes'),
(4,57,'C++','completed',3,'2025-10-23','No'),
(5,557336,'Java','completed',0,'2025-10-23','Yes'),
(6,57,'Java','completed',1,'2025-10-24','No'),
(7,557336,'C++','completed',0,'2025-10-25','Yes');

INSERT INTO client VALUES
(1,'PC'),
(2,'Android'),
(3,'IOS'),
(4,'H5');
```
- Solution 
- case when 匹配操作: join连表后, 可以赋值， 也可以用其他表的列名匹配相关项
  - `case when o.is_group_buy='Yes' then 'GroupBuy' else c.name end`

```MySQL 8.0
select tmp.source, count(*) cn 
from(select count(*) over(partition by o.user_id) as r,
     -- case when 与 join 连表操作
     case when o.is_group_buy='Yes' then 'GroupBuy' else c.name end source
     from order_info o
     left join client c 
     on c.id = o.client_id
     where o.date > '2025-10-15'
     and o.status = 'completed'
     and o.product_name in('C++', 'Python', 'Java')
) tmp 
where tmp.r >=2
group by tmp.source order by tmp.source;
```

### SQL85 筛选日期
- 年-月
```MySQL 8.0
left('2025-02-23', 7);
date_format('2025-02-23', '%Y-%m');
caoncat(year(date), '-', month(date));
```
- 某年
```MySQL 8.0
date like '2025%';
date between '2025-01-01' and '2025-12-31';
```
- 年/月/日+1
`UPDATE table SET date = DATE_ADD(date, INTERVAL 1 YEAR/month/day)`


## Review 2 OldBoy

> 可能是全网最好的MySQL入门合集 <br>
> https://www.bilibili.com/video/BV1fx411Z7CV <br>

### 3
`select student_id, avg(number) from score group by student_id having avg(number) > 60;`
### 5
`select count(tname) from teacher where tname like '李%';`
### 6
`select sid, sname from student where sid not in (select student_id from score where corse_id in (select cid from teacher left join course on teacher.tid = course.teacher_id where tname = '波多'));`
### 8
`select student.sid, sname from student where student.sid in (select student_id from score where corse_id in (select cid from teacher left join course on tid = course.teacher_id where tname = '波多'));`
### 4
`select student.sid, sname, count(corse_id), sum(number) from student left join score on student.sid = score.student_id group by student_id;`

### 7 学过课程001且也学过002课程的同学学号、姓名
-- 且条件不能用 and ，可用 or 筛选
```MySql
select sid, sname from student where sid in (
    select student_id from score where course_id = 1 or course_id = 2
        group by student_id having count(1) > 1);
```

### 9 查询课程编号002的成绩比001低的所有同学学号、姓名
### 2 查询生物课比物理课成绩高的所有同学的学号
 
```MySql
--9
select sid, sname from student where sid in (
    select s1_id from
        (select student_id s1_id, number n1 from score where course_id  = 1) as a
        inner join
        (select student_id s2_id, number n2 from score where course_id = 2) as b
        on s1_id = s2_id where n2 < n1);
-- 2
-- Every derived table must have its own alias---生物行 
select s1_id from
    (select student_id s1_id, number n1 from score
        where course_id in (select cid from course where cname = "生物")) as a
    inner join
    (select student_id s2_id, number n2 from score
        where course_id in (select cid from course where cname = "物理")) as b
    on s1_id = s2_id where n1 > n2;
```

### 10 查询有课程成绩小于60分的同学学号、姓名
```MySql
select sid, sname from student where sid in(
    select student_id from score where number < 60 group by student_id);
```

### 11 查询没有学全所有课程的同学学号、姓名
```MySql
-- 如果学生一门课都没选呢？ 用or筛选
-- count(1) or count(primary key) 效率高 (p32 练习讲解4)
select sid, sname from student where sid
    in ( select student_id from score group by student_id
        having (select count(1) from course) > count(1))
    or sid not in (select student_id from score);
```

### 12 查询至少有一门课与学号为001的同学所学相同的同学的学号、姓名
### 14 查询和 002 号同学学习的课程完全相同的同学学号、姓名
### 13 查询至少学过学号为 001 同学所选课程中任意一门课的其他同学学号、姓名
```MySql
-- 不会 
--12
select sid, sname from student where sid in
    (select student_id from score where course_id in(
        select course_id from score where student_id = 1)
        and student_id != 1
        group by student_id);
--14
select sid, sname from student where sid in(
select student_id from score where course_id in(
select course_id from score where student_id = 2
) and student_id != 2 group by student_id having count(1) = (
select count(1) from score where student_id =2
));
--13 =12
select sid, sname from  student where sid in(
select student_id from score where course_id in(
select course_id from score where student_id = 1
)and student_id !=1);

```

### 15 删除学过 小叶 老师score表纪录
```MySql
delete from score where course_id in(
select cid from course where teacher_id in(
select tid from teacher where tname = '小叶'
));
```

### 16 向sc表中插入一些记录，这些记录符合以下条件：没有上过 002 课程的同学学号；插入 002 号课程的平均成绩
```MySql
-- 插入值：insert into table_name(c1, c2, c3) values(v1, v2, v3);
-- 赋值表：insert into table_name(column_list) select column_list from table_name;
insert into score(student_id, course_id, number)
select sid, 2, (
select avg(number) from score where course_id = 2
) as avgcor from student where sid not in(
select student_id from score where course_id = 2
);
```

### 17 按平均成绩从低到高显示所有学生的 语文、数学、英语课的成绩，形式：学生ID，语数英，有效课程数，有效平均分
```MySql
-- 有效指的是 语数英三门课，所以筛选需要限定的这三门课里: count(), avg()---where 语句限定
-- where 在 group by 后则 error：course_id 找不到 
select
    student_id '学生ID',
    (select number from score s2 where s1.student_id = s2.student_id and
        s2.course_id in (select cid from course where cname = '生物')) as '生物',
    (select number from score s2 where s1.student_id = s2.student_id and 
        s2.course_id in (select cid from course where cname = '体育')) as '体育',
    (select number from score s2 where s1.student_id = s2.student_id and 
        s2.course_id in (select cid from course where cname = '物理')) as '物理',
    count(s1.course_id) '有效课程数',
    avg(number) '有效平均分'
from
    score s1
where
    course_id in(select cid from course where cname in('生物', '体育', '物理'))
group by
    student_id
order by
    '有效平均分';
```

### 19 各科按平均成绩从低到高 和 及格率从高到低排序
### 35 查询每门课程成绩最好的前2名(小于第三的即为前2名 同19)
### 21 查询各科成绩前3名的纪录（考虑并列）--p36 excises8(like 35)
```MySql
--21
select student_id, course_id, number
from(
    select student_id, course_id, number, (
    select number from score s2 where s1.course_id = s2.course_id group by number order by number desc limit 3, 1
    ) forth from score s1
) s3 
where number > if(isnull(s3.forth), 0, s3.forth)
order by course_id, number desc;


--35
---version 1 ERROR 1054 (42S22): Unknown column 'third' in 'where clause'
select
    student_id,
    course_id,
    number,
    (select number from score s2 where s1.course_id = 
    s2.course_id group by number order by number limit 2, 1
    ) as third
from
    score s1
where
    number > if(isnull(third),0, third) 
order by
    number desc;

---verson 3 
select student_id, course_id, number from(
    select student_id, course_id, number, (
    select number from score s2 where s1.course_id = 
    s2.course_id group by number order by number desc limit 2, 1
    ) as third from score s1
) s3
where
    s3.number > if(isnull(s3.third), 0, s3.third)
order by
    course_id, number desc;


-- 19
-- case when then else
select
    course_id,
    avg(number) avgsco,
    sum(case when number >= 60 then 1 else 0 end)/sum(1) passrate
from
    score
group by
    course_id
order by
    avgsco asc, passrate desc;
```

### 20 课程平均分从高到低表示，显示任课老师
```MySql
-- IF(ISNULL(X),expr1,expr2)
select
    course_id,
    cname,
    avg(if (isnull(number), 0, number)) avgsco,
    (select tname from teacher where tid in(
        select teacher_id from course where cid = course_id)) teacher
from 
    score
left join 
    course on course_id = course.cid
group by
    course_id
order by
    avgsco desc;
```

### 25 查询小姓学生名单
```MySql
select sid, sname from student where sname like '小%';
```
### 26 查询同名同姓学生名单，并且统计人数
```MySql
select sname, count(1) from student group by sname having count(1)>0;
```

### 28 查询平均成绩大于85的所有学生姓名、学号和平均成绩
```MySql
-- avg(number) 要有别名，否则第一个 select 语句 error
-- Every derived table must have its own alias
-- And 引用 内容需要用格式：derives_table_alias.内容别名 like s1.average
select student_id,sname, s1.average from(
    select student_id, avg(number) average from score group by student_id
    having avg(number) > 85
) s1 left join student on student_id = sid;
```

### 31 查询选了课程的人数
```MySql
select count(s1.student_id) from(
select student_id from score group by student_id
) s1;
```

### 32 查询选修"苍空"老师所授课程的学生中，成绩最高的学生
```MySql
select * from (
    select *, dense_rank() over(order by number desc) crank from teacher 
    left join course on tid = teacher_id 
    left join score on cid = course_id where tname = '苍空'
) A where a.crank = 1;
```

### 33 查询各个课程及其选修人数
```MySql
-- 不能 count(1),否则计数没有人选修的课程为1
select cid, cname, count(student_id) from course
left join score on cid = course_id
group by cid;
```

### 34 查询不同课程但成绩相同的学生的学号、课程号、学生成绩
```MySql
select s1.student_id, s1.course_id, number
from score s1, score s2
where
    s1.student_id != s2.student_id and
    s1.course_id != s2.course_id and
    s1.number = s2.number;
```

### 36 检索至少选修两门课的学生学号
```MySql
select student_id from score
group by student_id 
having count(course_id) > 1;
--37 查询全部学生都选修的课程课程号和课程名
--39 查询两门以上不及格课程的童鞋及其平均成绩
```

### P44_below: SQL refer to pymysql view 触发器 函数 存储过程……
```python
-- pymysql
pip3 install pymysql # -i 国内源

import pymysql
course_id = input('courseId: ')
conn = pymysql.connect(host='localhost',
                       user='root',
                       password='542643364',
                       database='db2')
cursor = conn.cursor()
sql = 'select * from score where course_id=%s'
cursor.execute(sql, course_id)
ret = cursor.fetchone() # 查
# 增删改 cursor.commit()
cursor.close()
conn.close()

if ret: print('成', ret)
else: print('败')
```

### p49 Function
```MySql
--build in F
date_format('2021-10-12 11:11', '%y-%m')
select sum(x, y); -- 执行函数
select date_format(now(), '%y-%m-%d %H:%i');
select date_format('2021-10-12 16:32:02', '%y-%m %H:%i');
--def F
delimiter \\
create function f(
    a1 int,
    a2 int)
returns int
BEGIN
    declare num int default 0;
    set num = a1 + a2;
    return (num);
END \\
delimiter;

select f(1, 100);
```

### p47 view
```MySql
create view v5 as select * from where number > 60;
select * from v5;
```

### p51(50-53...) procedure + transaction
#### 1 检查插入值执行状态 
  - sucess 0
  - error 1
  - warning 2
  
```MySql
delimiter //
create procedure p6(
	out n int
)
BEGIN
	declare exit handler for sqlexception
	begin
		--Error
		set n = 1;
		rollback;
	end;
	
	declare exit handler for sqlwarning
	begin
		set n = 2;
		rollback;
	end;
	
	start transaction;
		delete from score where id = 1;
		insert into student(sname, gender, class_id) values('大头', '男', 2);
	commit;
	
	--success
	set n = 0;
END //
delimiter ;

--out 位置需要传入一个变量
--@a session 级变量
set @a = 9;
call p6(@a);
select @a;

-- another way
set @_p6_0 = 9;
call p6(@_p6_0);
select @_p6_0;
```
```python3
-- pymysql 执行上面语句
import pymysql
course_id = input('courseId: ')
conn = pymysql.connect(host='localhost',
                       user='root',
                       password='542643364',
                       database='db2')
cursor = conn.cursor()

cursor.callproc('p6', (90,))
# conn.commit()
cursor.execute('select @_p6_0')
ret = cursor.fetchall()
print(ret)

# conn.commit()
cursor.close()
conn.close()

# if ret: print('成', ret)
# else: print('败')

```
#### 2 存储过程+动态SQL+防注入
```MySql
delimiter //
create procedure p3(
    in tpl varchar(255),
    in args int
)
BEGIN
    set @a = args;
    set @t = tpl;
    prepare xxoo from @t; -- 用变量
    execute xxoo using @a; -- 用变量
    deallocate prepare xxoo;
END //
delimiter ;

call p3('select * from student where sid > ?', 2)
```

> pymysql 实现：
```python3
import pymysql
course_id = input('courseId: ')
conn = pymysql.connect(host='localhost',
                       user='root',
                       password='542643364',
                       database='db2')
cursor = conn.cursor()

cursor.callproc('p3', ('select * from student where sid > ?', 2))
ret = cursor.fetchall() # 查询用 fetchall()
print(ret)

# conn.commit() # 增删改data有变动需要 commit()
cursor.close()
conn.close()
```

#### 3 p52 cursor-LOOP



> **Python 执行 mysql 存储过程** <br>
> refer to https://www.cnblogs.com/klvchen/p/10119006.html <br>

> 1 创建临时表
```MySql
create database test;
use test;

DROP TABLE IF EXISTS `tmp`;
CREATE TABLE `tmp`  (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(60) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NULL DEFAULT NULL,
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 12 CHARACTER SET = latin1 COLLATE = latin1_swedish_ci ROW_FORMAT = Dynamic;

INSERT INTO `tmp` VALUES (1, 'klvchen');
INSERT INTO `tmp` VALUES (2, 'lily');
INSERT INTO `tmp` VALUES (3, 'lucy');
INSERT INTO `tmp` VALUES (4, 'james');
INSERT INTO `tmp` VALUES (5, 'jim');
```

> 2 创建 存储过程
```MySql
delimiter $$        # 自定义 mysql 的分隔符
CREATE  PROCEDURE p1(
    in i1 int,      # 仅用于传入参数用
    in i2 int,
    inout i3 int,   # 既可以传入又可以当作返回值
    out r1 int      # 仅用于返回值用，外部传进来的值无用
)
BEGIN
    DECLARE temp1 int;
    DECLARE temp2 int default 0;
    
    set temp1 = 1;

    set r1 = i1 + i2 + temp1 + temp2;
    
    set i3 = i3 + 100;
		
		SELECT * FROM tmp;

end $$
delimiter ;
```

> 3 调用 pymql
```
import pymysql

PY_MYSQL_CONN_DICT = {
    "host" : '192.168.0.214',
    "port" : 3306,
    "user" : 'root',
    "passwd" : '123456',
    "db" : 'tmpdb'
}

conn = pymysql.connect(**PY_MYSQL_CONN_DICT)
cusor = conn.cursor(cursor=pymysql.cursors.DictCursor)

# 调用 p1 存储过程，传入4个参数
cusor.callproc('p1', args=(1, 2, 3, 4))

# 返回获得的集合，即存储函数中的 SELECT * FROM tmp; 结果
res1 = cusor.fetchall()
print(res1)

# 以 python 固定格式获取返回的值：@_存储过程名_0, 第一个返回值
cusor.execute("select @_p1_0, @_p1_1, @_p1_2, @_p1_3")
res2 = cusor.fetchall()
print(res2)

conn.commit()
cusor.close()
conn.close()
```

- 'mysql> drop procedure p6;'
- 参数
  - IN 输入参数：表示调用者向过程传入值（传入值可以是字面量或变量）
  - OUT 输出参数：表示过程向调用者传出值(可以返回多个值)（传出值只能是变量）
  - INOUT 输入输出参数：既表示调用者向过程传入值，又表示过程向调用者传出值（值只能是变量）

    

