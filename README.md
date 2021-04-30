# SQLexcises

> https://www.nowcoder.com/activity/oj SOL18
- 请你查找薪水排名第二多的员工编号emp_no、薪水salary、last_name以及first_name，不能使用order by完成
> order by 排序实现方法之一

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
自链salary，一对一拼接自身表格
```
s1 	s2
100 100
98 	98
98 	98
95 	95
```
s1.salary 中每一职工对应多个 s2.salary 中 s1 >= s1 的薪水项

```
s1 	s2
100 100
98  100
	  98
	  98
95 	100
	  98
	  98
	  95
```
