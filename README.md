# SQLexcises

> https://www.nowcoder.com/activity/oj 

### SQL10
```
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
```
SELECT e.emp_no, m.emp_no AS manager_no
FROM dept_emp AS e
INNER JOIN dept_manager AS m
ON e.dept_no=m.dept_no
WHERE e.emp_no!=m.emp_no
AND e.to_date='9999-01-01'
AND m.to_date='9999-01-01'
```

### SOL18
- 请你查找薪水排名第二多的员工编号emp_no、薪水salary、last_name以及first_name，不能使用order by完成
  - order by 排序实现方法之一
  - 题目
```
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
```
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
```
select e.emp_no, s.salary, e.last_name, e.first_name
from employees e
left join salaries s
on e.emp_no = s.emp_no
where s.salary = (
    select max(salary) from salaries where
    salary > (select max(salary) from salaries));
```

