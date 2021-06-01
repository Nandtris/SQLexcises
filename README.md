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

### SQL25 
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

## SQL关键字顺序
```MySQL 8.0
SELECT [DISTINCT|DISINCTROW|ALL] select_expression,... -- 查询结果
[FROM table_references -- 指定查询的表
[WHERE where_definition] -- where子句，查询数据的过滤条件
[GROUP BY col_name,...] -- 对[匹配where子句的]查询结果进行分组
[HAVING where_definition] -- 对分组后的结果进行条件限制
[ORDER BY{unsigned_integer | col_name | formula} [ASC | DESC],...] -- 对查询结果进行排序
[LIMIT [offset,] rows] -- 对查询的显示结果进行条数限制
[PROCEDURE procedure_name] --查询存储过程返回的结果集数据
]
-- 原文链接：https://blog.csdn.net/bestforxu/article/details/51131234
```
