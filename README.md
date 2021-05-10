# SQLexcises

> https://www.nowcoder.com/activity/oj 

### SQL10
```MySQL
drop table if exists  `dept_emp` ; 
drop table if exists  `dept_manager` ; 

--有一个员工表dept_emp简况如下
--第一行表示为员工编号为10001的部门是d001部门。
CREATE TABLE `dept_emp` (
`emp_no` int(11) NOT NULL,
`dept_no` char(4) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));

--有一个部门经理表dept_manager简况如下: 
--第一行表示为d001部门的经理是编号为10002的员工
CREATE TABLE `dept_manager` (
`dept_no` char(4) NOT NULL,
`emp_no` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));

--求：获取所有的员工和员工对应的经理，如果员工本身是经理的话则不显示
INSERT INTO dept_emp VALUES(10001,'d001','1986-06-26','9999-01-01');
INSERT INTO dept_emp VALUES(10002,'d001','1996-08-03','9999-01-01');
INSERT INTO dept_emp VALUES(10003,'d002','1995-12-03','9999-01-01');
INSERT INTO dept_manager VALUES('d001',10002,'1996-08-03','9999-01-01');
INSERT INTO dept_manager VALUES('d002',10003,'1990-08-05','9999-01-01');
```

- 题解：
```MySQL
SELECT e.emp_no, m.emp_no AS manager_no
FROM dept_emp AS e
INNER JOIN dept_manager AS m
ON e.dept_no=m.dept_no
-- key:!= 过滤经理
WHERE e.emp_no!=m.emp_no
AND e.to_date='9999-01-01'
AND m.to_date='9999-01-01';
```

### SQL24
此题对比 SQL10，都需要过滤经理
```MySQL
drop table if exists  `dept_emp` ; 
drop table if exists  `dept_manager` ; 
drop table if exists  `employees` ; 
drop table if exists  `salaries` ; 

--有一个，部门员工关系表dept_emp简况如下: 
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

-- 获取所有非manager员工薪水情况，给出dept_no、emp_no以及salary，以上例子输出:
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
INSERT INTO employees VALUES(10001,'1953-09-02','Georgi','Facello','M','1986-06-26');
INSERT INTO employees VALUES(10002,'1964-06-02','Bezalel','Simmel','F','1985-11-21');
INSERT INTO employees VALUES(10003,'1959-12-03','Parto','Bamford','M','1986-08-28');
INSERT INTO employees VALUES(10004,'1954-05-01','Chirstian','Koblick','M','1986-12-01');
INSERT INTO employees VALUES(10005,'1955-01-21','Kyoichi','Maliniak','M','1989-09-12');
INSERT INTO employees VALUES(10006,'1953-04-20','Anneke','Preusig','F','1989-06-02');
INSERT INTO employees VALUES(10007,'1957-05-23','Tzvetan','Zielinski','F','1989-02-10');
INSERT INTO employees VALUES(10008,'1958-02-19','Saniya','Kalloufi','M','1994-09-15');
INSERT INTO employees VALUES(10009,'1952-04-19','Sumant','Peac','F','1985-02-18');
INSERT INTO employees VALUES(10010,'1963-06-01','Duangkaew','Piveteau','F','1989-08-24');
INSERT INTO employees VALUES(10011,'1953-11-07','Mary','Sluis','F','1990-01-22');
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
```MySQL
select de.dept_no, de.emp_no, sa.salary
from dept_emp de
-- key: dept_no 关联
-- 用 left 连接，考虑到诸如某个当前员工薪水缺失的问题
-- inner 会漏掉过滤掉当前缺失薪水纪录的员工
left join dept_manager dm on de.dept_no = dm.dept_no
left join salaries sa on de.emp_no = sa.emp_no
where de.emp_no != dm.emp_no
and sa.to_date = '9999-01-01';
```

SQL25 
考点同24，10，更复杂
```MySQL
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
```MySQL
select de.emp_no, dm.emp_no as manager_no,
sa.salary emp_salary, sa2.salary manager_salary
from dept_emp de
left join dept_manager dm on de.dept_no = dm.dept_no -- where 
left join salaries sa on de.emp_no = sa.emp_no -- emp_salary
left join salaries sa2 on dm.emp_no = sa2.emp_no -- manager_sa
where de.emp_no != dm.emp_no -- 筛出经理-经理行，形成职工-经理行
and sa.salary > sa2.salary
and sa.to_date = '9999-01-01' -- 保证是当前职工
and sa2.to_date = '9999-01-01';
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


### SQL21
```MySQL
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

-- 请你查找所有员工自入职以来的薪水涨幅情况，给出员工编号emp_no以及其对应的薪水涨幅growth，并按照growth进行升序
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
- 题解如下：
```MySQL
select e.emp_no,
(select s.salary-sa.salary from salaries sa
    where sa.emp_no = e.emp_no and sa.from_date = e.hire_date
) as growth -- 作为大表 select 中的临时表，可以用大表 e, s 有关元素与本表 sa 元素关联，甚至做加减法
from employees e left join salaries s
on e.emp_no = s.emp_no
where to_date = '9999-01-01'
order by growth;
```


### SQL22
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

--请你统计各个部门的工资记录数，给出部门编码dept_no、部门名称dept_name <br>
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
-- way1 select 中设置子查询
select de.dept_no, 
(select dept_name from departments where dept_no=de.dept_no) dept_name,
count(*)
from dept_emp de

left join salaries sa
on de.emp_no = sa.emp_no
group by de.dept_no
order by de.dept_no;

-- way2 left join 三连,注意分组、排序条件放最后
select de.dept_no,
d.dept_name,
count(*)
from dept_emp de
  
left join salaries sa
on de.emp_no = sa.emp_no
 
left join departments d
on d.dept_no = de.dept_no
 
group by de.dept_no
order by de.dept_no;
```

### SQL23
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
  - reference: https://blog.csdn.net/niuchenliang524/article/details/107690207
```MySQL
select emp_no, salary,

dense_rank() over(order by salary desc) as t_rank 
from salaries 
order by salary desc, emp_no;
```
- other
```
用户变量，在客户端链接到数据库实例整个过程中用户变量都是有效的。
MySQL中用户变量不用事前申明，在用的时候直接用“@变量名”使用就可以了。
第一种用法：set @num=1; 或set @num:=1; //这里要使用set语句创建并初始化变量，直接使用@num变量
第二种用法：select @num:=1; 或 select @num:=字段名 from 表名 where ……，
select语句一般用来输出用户变量，比如select @变量名，用于输出数据源不是表格的数据。
注意上面两种赋值符号，使用set时可以用“=”或“:=”，但是使用select时必须用“:=赋值”
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
