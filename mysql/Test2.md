```txt
我们讲了单表的简单查询，现在我们看看多张表之间的复杂查询：

========================连接查询===============================================================================
连接查询：在实际开发中，都不是从单表中查询数据，一般都是从多张表联合查询取出最终的结果。
一般一个业务都会对应多张表，比如：学生和班级，两张表。

连接查询分类----------------------------------------------------------------------------------------------

	根据语法出现的年代来划分的话，包括：
		SQL92(一些老的DBA可能还在使用这种语法。DBA：DataBase Administrator，数据库管理员)
		SQL99(比较新的语法)

	根据表的连接方式来划分，包括：
		内连接：
			等值连接
			非等值连接
			自连接
		外连接：一张表是主表，一张是副表
			左外连接（左连接）
			右外连接（右链接）
		全连接full join（目前mysql中不支持全连接）:两张表全是主表，a b两张表数据全部查出来，不漏任何数据
==============================================================================================
在表的连接查询方面有一种现象被称为：笛卡尔积现象（也叫cross join交叉连接）

案例：找出每一个员工的部门名称，要求显示员工名和部门名。

	员工表emp
	+-------+--------+-----------+------+------------+---------+---------+--------+
	| EMPNO | ENAME  | JOB       | MGR  | HIREDATE   | SAL     | COMM    | DEPTNO |
	+-------+--------+-----------+------+------------+---------+---------+--------+
	|  7369 | SMITH  | CLERK     | 7902 | 1980-12-17 |  800.00 |    NULL |     20 |
	|  7499 | ALLEN  | SALESMAN  | 7698 | 1981-02-20 | 1600.00 |  300.00 |     30 |
	|  7521 | WARD   | SALESMAN  | 7698 | 1981-02-22 | 1250.00 |  500.00 |     30 |
	|  7566 | JONES  | MANAGER   | 7839 | 1981-04-02 | 2975.00 |    NULL |     20 |
	|  7654 | MARTIN | SALESMAN  | 7698 | 1981-09-28 | 1250.00 | 1400.00 |     30 |
	|  7698 | BLAKE  | MANAGER   | 7839 | 1981-05-01 | 2850.00 |    NULL |     30 |
	|  7782 | CLARK  | MANAGER   | 7839 | 1981-06-09 | 2450.00 |    NULL |     10 |
	|  7788 | SCOTT  | ANALYST   | 7566 | 1987-04-19 | 3000.00 |    NULL |     20 |
	|  7839 | KING   | PRESIDENT | NULL | 1981-11-17 | 5000.00 |    NULL |     10 |
	|  7844 | TURNER | SALESMAN  | 7698 | 1981-09-08 | 1500.00 |    0.00 |     30 |
	|  7876 | ADAMS  | CLERK     | 7788 | 1987-05-23 | 1100.00 |    NULL |     20 |
	|  7900 | JAMES  | CLERK     | 7698 | 1981-12-03 |  950.00 |    NULL |     30 |
	|  7902 | FORD   | ANALYST   | 7566 | 1981-12-03 | 3000.00 |    NULL |     20 |
	|  7934 | MILLER | CLERK     | 7782 | 1982-01-23 | 1300.00 |    NULL |     10 |
	+-------+--------+-----------+------+------------+---------+---------+--------+
	部门表dept
	+--------+------------+----------+
	| DEPTNO | DNAME      | LOC      |
	+--------+------------+----------+
	|     10 | ACCOUNTING | NEW YORK |
	|     20 | RESEARCH   | DALLAS   |
	|     30 | SALES      | CHICAGO  |
	|     40 | OPERATIONS | BOSTON   |
	+--------+------------+----------+

	select ename,dname from emp,dept;
	+--------+------------+
	| ename  | dname      |
	+--------+------------+
	| SMITH  | ACCOUNTING |
	| SMITH  | RESEARCH   |
	| SMITH  | SALES      |
	| SMITH  | OPERATIONS |
	| ALLEN  | ACCOUNTING |
	| ALLEN  | RESEARCH   |
	| ALLEN  | SALES      |
	| ALLEN  | OPERATIONS |
	| WARD   | ACCOUNTING |
	| WARD   | RESEARCH   |
	| WARD   | SALES      |
	| WARD   | OPERATIONS |
	| JONES  | ACCOUNTING |
	...
	56 rows in set (0.00 sec)

（笛卡尔积现象：当两张表进行连接查询的时候，没有任何条件限制，最终的结果条数是两张表的乘积）

	关于表的别名：
		select e.ename,d.dname from emp e,dept d;
		我们知道给字段起别名，给表加上别名有什么好处？
			第一：可执行效率高
			第二：可读性好
------------------------------------------------------

怎么避免笛卡尔积现象？当然是加条件进行过滤
思考：避免了笛卡尔积现象，会减少记录的匹配次数吗？
	不会，次数还是56次。只不过显示的有效记录变少了，匹配次数没变

案例：找出每一个员工的部门名称，要求显示员工名和部门名
	select
		e.ename,d.dname
	from
		emp e,dept d
	where
		e.deptno=d.deptno;	//SQL92，以后不用，忽略即可
	+--------+------------+
	| ename  | dname      |
	+--------+------------+
	| CLARK  | ACCOUNTING |
	| KING   | ACCOUNTING |
	| MILLER | ACCOUNTING |
	| SMITH  | RESEARCH   |
	| JONES  | RESEARCH   |
	| SCOTT  | RESEARCH   |
	| ADAMS  | RESEARCH   |
	| FORD   | RESEARCH   |
	| ALLEN  | SALES      |
	| WARD   | SALES      |
	| MARTIN | SALES      |
	| BLAKE  | SALES      |
	| TURNER | SALES      |
	| JAMES  | SALES      |
	+--------+------------+

------------内连接之等值连接-------------------------------------------------------------------
等值连接指，条件是等量关系。
	案例：找出每一个员工的部门名称，要求显示员工名和部门名。
	SQL92:
		select
			e.ename,d.dname
		from
			emp e,dept d
		where
			e.deptno=d.deptno;
	SQL99：（最新的，常用的）
		select
			e.ename,d.dname
		from
			emp e
		(inner)join		//可读性更好一些，一看就知道是内连接
			dept d
		on
			e.deptno=d.deptno;//由于条件是等值的，所以叫等值连接

	语法：
	...
		A表
	join
		B表
	on
		连接条件
	where
		条件
（新的SQL99语法结构更清晰一些：表的连接条件和后来的where条件分离了）

------------内连接之非等值连接---------------------------------------------------------
非等值连接指，连接条件中的等量关系是非等值关系。

	案例：找出每个员工的工资等级，要求显示员工名、工资、工资等级。
		select
			e.ename,e.sal,s.grade
		from
			emp e
		(inner) join	//inner关键字可选
			salgrade s
		on
			e.sal between s.losal and s.hisal;
		+--------+---------+-------+
		| ename  | sal     | grade |
		+--------+---------+-------+
		| SMITH  |  800.00 |     1 |
		| ALLEN  | 1600.00 |     3 |
		| WARD   | 1250.00 |     2 |
		| JONES  | 2975.00 |     4 |
		| MARTIN | 1250.00 |     2 |
		| BLAKE  | 2850.00 |     4 |
		| CLARK  | 2450.00 |     4 |
		| SCOTT  | 3000.00 |     4 |
		| KING   | 5000.00 |     5 |
		| TURNER | 1500.00 |     3 |
		| ADAMS  | 1100.00 |     1 |
		| JAMES  |  950.00 |     1 |
		| FORD   | 3000.00 |     4 |
		| MILLER | 1300.00 |     2 |
		+--------+---------+-------+

----------自连接------------------------------------------------------------
自连接：最大的特点是，自己连接自己

	案例：找出每个员工的上级领导，要求显示出对应的员工名和领导名
		select
			a.ename as '员工',b.ename as '领导'
		from
			emp a
		join
			emp b
		on
			a.mgr=b.empno;

			+--------+-------+
			| 员工   | 领导  |
			+--------+-------+
			| SMITH  | FORD  |
			| ALLEN  | BLAKE |
			| WARD   | BLAKE |
			| JONES  | KING  |
			| MARTIN | BLAKE |
			| BLAKE  | KING  |
			| CLARK  | KING  |
			| SCOTT  | JONES |
			| TURNER | BLAKE |
			| ADAMS  | SCOTT |
			| JAMES  | BLAKE |
			| FORD   | JONES |
			| MILLER | CLARK |
			+--------+-------+
```

```txt
--------------外连接-------------------------------------------------------------------------------

什么是外连接？和内连接有什么区别？
	内连接：
		假设A表和B表进行连接，使用内连接的话，凡是A表和B表能够匹配上的记录查询出来，这就是内连接
		A、B两张表没有主副表之分，都是平等的。这种连接可能会缺失数据，因为匹配不上的数据就缺失了

	外连接：
		假设A表和B表进行连接，使用外连接的话，AB两张表有一张表是主，一张是副，
		主要查询主表中的数据，捎带着查询副表。当副表中的数据没有和主表中的数据匹配上，
		副表自动模拟出null与之匹配，这种连接方式实际开发中用的比较多

外连接分为‘左外连接’和‘右外连接’，也叫左连接和右连接
	左外连接：表示左边的这张表是主表
	右外连接：表示右边的这张表是主表

	左连接有右连接的写法，右连接也会有对应的左连接的写法

	案例：找出每一个员工的上级领导？（所有员工都要查出来）
	select
		a.ename as '员工',b.ename as '领导'
	from
		emp a
	left (outer) join	//inner和outer一样，都可以省略的
		emp b
	on
		a.mgr=b.empno;

		+--------+-------+
		| 员工   | 领导  |
		+--------+-------+
		| SMITH  | FORD  |
		| ALLEN  | BLAKE |
		| WARD   | BLAKE |
		| JONES  | KING  |
		| MARTIN | BLAKE |
		| BLAKE  | KING  |
		| CLARK  | KING  |
		| SCOTT  | JONES |
		| KING   | NULL  |	KING的上级领导是null
		| TURNER | BLAKE |
		| ADAMS  | SCOTT |
		| JAMES  | BLAKE |
		| FORD   | JONES |
		| MILLER | CLARK |
		+--------+-------+
--------------------------------------------
（重要案例）：找出哪个部门没有员工
	select
		d.*
	from
		emp e
	right join
		dept d
	on
		e.deptno=d.deptno
	where
		e.empno is null;

	+--------+------------+--------+
	| DEPTNO | DNAME      | LOC    |
	+--------+------------+--------+
	|     40 | OPERATIONS | BOSTON |
	+--------+------------+--------+

----------------------------------------------------------------------------------------------------------------------
三张表怎么建立连接？（多表链接就是，2张表的链接结果，当作新表继续和下一张表进行链接）

	案例：找出每个员工的工资等级以及部门名称
	select				（内连接）
		e.ename,s.grade,d.dname
	from
		emp e
	join
		dept d
	on
		e.deptno=d.deptno
	join
		salgrade s
	on
		e.sal between s.losal and s.hisal;

	+--------+-------+------------+
	| ename  | grade | dname      |
	+--------+-------+------------+
	| SMITH  |     1 | RESEARCH   |
	| ALLEN  |     3 | SALES      |
	| WARD   |     2 | SALES      |
	| JONES  |     4 | RESEARCH   |
	| MARTIN |     2 | SALES      |
	| BLAKE  |     4 | SALES      |
	| CLARK  |     4 | ACCOUNTING |
	| SCOTT  |     4 | RESEARCH   |
	| KING   |     5 | ACCOUNTING |
	| TURNER |     3 | SALES      |
	| ADAMS  |     1 | RESEARCH   |
	| JAMES  |     1 | SALES      |
	| FORD   |     4 | RESEARCH   |
	| MILLER |     2 | ACCOUNTING |
	+--------+-------+------------+

	案例升级：找出每一个员工的部门名称、工资等级、以及上级领导
	select
		e.ename,s.grade,d.dname,e1.ename as '上级领导'
	from
		emp e
	join
		dept d
	on
		e.deptno=d.deptno
	join
		salgrade s
	on
		e.sal between s.losal and s.hisal
	left join
		emp e1
	on
		e.mgr=e1.empno;

	+--------+-------+------------+----------+
	| ename  | grade | dname      | 上级领导 |
	+--------+-------+------------+----------+
	| SMITH  |     1 | RESEARCH   | FORD     |
	| ALLEN  |     3 | SALES      | BLAKE    |
	| WARD   |     2 | SALES      | BLAKE    |
	| JONES  |     4 | RESEARCH   | KING     |
	| MARTIN |     2 | SALES      | BLAKE    |
	| BLAKE  |     4 | SALES      | KING     |
	| CLARK  |     4 | ACCOUNTING | KING     |
	| SCOTT  |     4 | RESEARCH   | JONES    |
	| KING   |     5 | ACCOUNTING | NULL     |
	| TURNER |     3 | SALES      | BLAKE    |
	| ADAMS  |     1 | RESEARCH   | SCOTT    |
	| JAMES  |     1 | SALES      | BLAKE    |
	| FORD   |     4 | RESEARCH   | JONES    |
	| MILLER |     2 | ACCOUNTING | CLARK    |
	+--------+-------+------------+----------+
（超过3张表一般不建议连接查询，2张表链接查询也建议用索引，因为连接查询相当于嵌套for很降低性能）
```

```txt
什么是子查询？子查询都可以出现在哪里？
	select语句当中嵌套select语句，被嵌套的select语句是子查询。
	子查询可以出现在哪里？
		select
			..(select)..
		from
			..(select)..
		where
			..(select)..

=======where后使用子查询======================================================================

案例：找出高于平均薪资的员工信息
	select * from emp where sal > (select avg(sal) from emp);

	+-------+-------+-----------+------+------------+---------+------+--------+
	| EMPNO | ENAME | JOB       | MGR  | HIREDATE   | SAL     | COMM | DEPTNO |
	+-------+-------+-----------+------+------------+---------+------+--------+
	|  7566 | JONES | MANAGER   | 7839 | 1981-04-02 | 2975.00 | NULL |     20 |
	|  7698 | BLAKE | MANAGER   | 7839 | 1981-05-01 | 2850.00 | NULL |     30 |
	|  7782 | CLARK | MANAGER   | 7839 | 1981-06-09 | 2450.00 | NULL |     10 |
	|  7788 | SCOTT | ANALYST   | 7566 | 1987-04-19 | 3000.00 | NULL |     20 |
	|  7839 | KING  | PRESIDENT | NULL | 1981-11-17 | 5000.00 | NULL |     10 |
	|  7902 | FORD  | ANALYST   | 7566 | 1981-12-03 | 3000.00 | NULL |     20 |
	+-------+-------+-----------+------+------------+---------+------+--------+

	#where子查询之:exists和not exists：外部查询的每条记录，都会根据exists(子查询)中子查询是否有结果，来决定该行记录是否包含在结果集中；
		简单说就是，exists(有记录)就返回true，此时where true成立，前面的select查到的这条记录就包含在结果集中；not exists就是取反；
	#它和in的区别：in会根据指定列表内的值是否相等来判断，exists只看有没有记录，尤其在结果集很多时，它效率远远高于in；

======from后嵌套子查询==========================================================================
（from后面的子查询可以看作临时表）

案例1：找出每个部门平均薪水的薪资等级
	第一步：找出各部门平均薪水（按照部门编号分组，求sal的平均值）
	select deptno,avg(sal) as '平均薪资' from emp group by deptno;
	+--------+-------------+
	| deptno | 平均薪资    |
	+--------+-------------+
	|     10 | 2916.666667 |
	|     20 | 2175.000000 |
	|     30 | 1566.666667 |
	+--------+-------------+
	第二步：给平均薪资分等级
		查看薪资等级表
		select * from salgrade;
		+-------+-------+-------+
		| GRADE | LOSAL | HISAL |
		+-------+-------+-------+
		|     1 |   700 |  1200 |
		|     2 |  1201 |  1400 |
		|     3 |  1401 |  2000 |
		|     4 |  2001 |  3000 |
		|     5 |  3001 |  9999 |
		+-------+-------+-------+
		将第一步查询到的结果当作p表，与第二步的薪资等级表左连接
		select
			p.*,s.grade
		from
			(select deptno,avg(sal) as avgsal from emp group by deptno) p
		left join
			salgrade s
		on
			p.avgsal between losal and hisal;

		+--------+-------------+-------+
		| deptno | avgsal      | grade |
		+--------+-------------+-------+
		|     10 | 2916.666667 |     4 |
		|     20 | 2175.000000 |     4 |
		|     30 | 1566.666667 |     3 |
		+--------+-------------+-------+


案例2：找出每个部门平均的薪资等级
	第一步，找出每个员工的薪资等级
	select
		e.ename,e.sal,e.deptno,s.grade
	from
		emp e
	left join
		salgrade s
	on
		e.sal between s.losal and s.hisal;
	+--------+---------+--------+-------+
	| ename  | sal     | deptno | grade |
	+--------+---------+--------+-------+
	| SMITH  |  800.00 |     20 |     1 |
	| ALLEN  | 1600.00 |     30 |     3 |
	| WARD   | 1250.00 |     30 |     2 |
	| JONES  | 2975.00 |     20 |     4 |
	| MARTIN | 1250.00 |     30 |     2 |
	| BLAKE  | 2850.00 |     30 |     4 |
	| CLARK  | 2450.00 |     10 |     4 |
	| SCOTT  | 3000.00 |     20 |     4 |
	| KING   | 5000.00 |     10 |     5 |
	| TURNER | 1500.00 |     30 |     3 |
	| ADAMS  | 1100.00 |     20 |     1 |
	| JAMES  |  950.00 |     30 |     1 |
	| FORD   | 3000.00 |     20 |     4 |
	| MILLER | 1300.00 |     10 |     2 |
	+--------+---------+--------+-------+

	第二步，基于以上结果，按照deptno分组，求grade的平均值
	select 
		e.deptno,avg(s.grade) 
	from
		emp e
	left join
		salgrade s
	on
		e.sal between s.losal and s.hisal
	group by
		e.deptno;
	+--------+--------------+
	| deptno | avg(s.grade) |
	+--------+--------------+
	|     10 |       3.6667 |
	|     20 |       2.8000 |
	|     30 |       2.5000 |
	+--------+--------------+
	下面这种也可以，但是麻烦多了：select p.deptno,avg(p.grade) as '平均薪资等级' from
	(select e.ename,e.deptno,s.grade from emp e left join salgrade s on e.sal between s.losal and s.hisal) p group by p.deptno;

==========select后面嵌套子查询=====================================================================

案例：找出每个员工所在的部门名称，要求显示员工名和部门名
	select e.ename,d.dname from emp e join dept.d on e.deptno=d.deptno;

	上面这种方式不太行，能不连接查就不连接查
	select e.ename,(select d.dname from dept d where e.deptno=d.deptno) as dname from emp e;
	+--------+------------+
	| ename  | dname      |
	+--------+------------+
	| SMITH  | RESEARCH   |
	| ALLEN  | SALES      |
	| WARD   | SALES      |
	| JONES  | RESEARCH   |
	| MARTIN | SALES      |
	| BLAKE  | SALES      |
	| CLARK  | ACCOUNTING |
	| SCOTT  | RESEARCH   |
	| KING   | ACCOUNTING |
	| TURNER | SALES      |
	| ADAMS  | RESEARCH   |
	| JAMES  | SALES      |
	| FORD   | RESEARCH   |
	| MILLER | ACCOUNTING |
	+--------+------------+
```

```txt
union：拼接查询结果集；注意，unin会自动去除重复的记录，如果不想去除重复记录用unin all
-------------------------------------------------------------------------------------
	select ename,job from emp where job = 'MANAGER'
	union
	select ename,job from emp where job = 'SALESMAN';
	+--------+----------+
	| ename  | job      |
	+--------+----------+
	| JONES  | MANAGER  |
	| BLAKE  | MANAGER  |
	| CLARK  | MANAGER  |
	| ALLEN  | SALESMAN |
	| WARD   | SALESMAN |
	| MARTIN | SALESMAN |
	| TURNER | SALESMAN |
	+--------+----------+

	两张不相干的表拼到一块：
	select ename from emp
	union
	select dname from dept;
	+------------+
	| ename      |
	+------------+
	| SMITH      |
	| ALLEN      |
	| WARD       |
	| JONES      |
	| MARTIN     |
	| BLAKE      |
	| CLARK      |
	| SCOTT      |
	| KING       |
	| TURNER     |
	| ADAMS      |
	| JAMES      |
	| FORD       |
	| MILLER     |
	| ACCOUNTING |
	| RESEARCH   |
	| SALES      |
	| OPERATIONS |
	+------------+
（注意：union的上下两个select语句中的显示字段数量要相同，并且以上面的字段为主）

======================================================================================
（重中之重，mysql特有的，oracle有相同的机制，rownum，sqlserver是top n）limit：取结果集的部分数据
	语法机制：
		limit startIndex,length
			startIndex表示起始的位置，从0开始，0表示第一条数据
			length表示取几个

	limit是sql语句最后执行的一个环节：
		select		5
			...
		from		1
			...
		where		2
			...
		group by	3
			...
		having		4
			...
		order by	6
			...
		limit		7
			...;
案例：取出工资前五名的员工（思路降序取前五个）
	select ename,sal from emp order by sal desc limit 0,5;	//这里也可以直接写成limit 5;默认从第一个开始

案例：找出工资排名在第四到第9名的员工？
	select ename,sal from emp order by sal desc limit 3,6;

通用的标准分页SQL？

	每页显示3条记录：
	第1页：limit 0,3;
	第2页：limit 3,3;
	第3页：limit 6,3;
	第4页：limit 9,3;
	第5页：limit 12,3;
	第6页：limit 15,3;

	每页显示n条记录
	第x页：limit (x-1)*n,n;
```

