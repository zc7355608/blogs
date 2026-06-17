```txt
（压缩包版mysql核心数据库需要进行初始化：(建议直接用安装器安装mysql8)

	# mysql 8.0.32压缩包解压到本地磁盘，然后配置path；
	# 管理员打开command，切到bin下，用命令初始化data(mysql核心数据)：
	# mysqld --initialize-insecure
	# 在安装目录下新建空文件my.ini，将以下内容复制到进去，地址改成安装路径；注意双斜杠；
	# 此时mysql目录的data目录中就有数据了，再执行：mysqld -install
	# 此时管理中多了mysql服务，这个服务就是一个后台运行的程序，负责管理数据库以及编译sql，它就是DBMS；
	# 启动MySQL服务：net start mysql
	# 然后登录MySQL：mysql -u root -p（没有密码双回车直接进入然后更改密码）
	# use mysql;	//切换数据库
	# update user set authentication_string=' ' where user='root';
	# alter user 'root'@'localhost' identified with mysql_native_password by '新密码';

	（默认root不可以远程登陆，可以这样设置（不安全）：
		# 登录mysql
		# 使用mysql核心数据库：use mysql;
		# 输入：update user set host = '%' where user = 'root';
		# 让它立即生效：flush privileges;
	）
）

------------my.ini文件-----------------------------------
[mysqld]
# 设置3306端口
port=3306
# 设置mysql的安装目录
basedir=D:\\software\\mysql-8.0.36-winx64
# 切记此处一定要用双斜杠\\，单斜杠这里会出错。
# 设置mysql数据库的数据的存放目录
datadir=D:\\software\\mysql-8.0.36-winx64\\data
# 此处同上
# 允许最大连接数
max_connections=200
# 允许连接失败的次数。这是为了防止有人从该主机试图攻击数据库系统
max_connect_errors=10
# 服务端使用的字符集默认为UTF8(在mysql5.7中一定要改)
character-set-server=utf8
# 服务端使用的比较规则(在mysql5.7中一定要改)
collation-server=utf8_general_ci
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
# 默认使用“mysql_native_password”插件认证
default_authentication_plugin=mysql_native_password
[mysql]
# 设置mysql客户端默认字符集(注意这个在mysql5.7中一定要设置)
default-character-set=utf8
[client]
# 设置mysql客户端连接服务端时默认使用的端口
port=3306
default-character-set=utf8
---------------------------------------------------------

（mysql8卸载：
	# mysql服务停掉
	# 卸载mysql服务：mysqld --remove mysql
	# 删除mysql目录
）
（mysql5.5卸载：
	# 运行安装工具，选择remove
	# 删除mysql安装目录
	# c盘program data目录下的mysql目录也删掉
）

=============================================start===============================================================

SQL语句分类：
	DQL（数据查询语言）：查询语句，凡是select语句都是DQL。
	DML（数据操作语言）：insert delete uptate，对表中的数据进行增删改
	DDL（数据定义语言）：create drop alter truncate rename，对表结构的增删改。
	TCL（事务控制语言）：commit提交事务、rollback回滚事务、savepoint等。（TCL中的T是Transaction）
	DCL（数据控制语言）：grant授权、revoke撤销权限等。
	CCL（指针控制语言）：declare cursor、fetch into、udpate where current都是指针控制语言。

MySQL常用命令：
	·查询当前使用的数据库：select database();
	·查询mysql版本号：select version();	这个database()和version()实际上是mysql的函数
	·清空当前输入：\c
	·退出mysql：exit、\q、quit、Ctrl+c
-------------------------

#导入数据（学习期间就用这个脚本文件的数据了:bjpowernode.sql）
	第一步：登录mysql数据库管理系统(DBMS)
		dos命令窗口：
			mysql -h hostname -u root -pa123456789
		（-h后面跟的是主机地址，输入localhost或者127.0.0.1都可以，如果是本地登录可以省略该参数；-P是端口号）

	第二步：查看有哪些数据库（不要忘记分号；）
		show databases;	//这个不是SQL语句，是MySQL的命令，还可以查看库中有多少表show tables [from 库名];
		+--------------------+
		| Database           |
		+--------------------+
		| information_schema |信息数据库：存储着MySQL服务器所维护的所有其他数据库的信息，如表名，数据库名，数据库权限等。
								在这个数据库中，有几个只读表，它们实际上是视图，而不是基本表，因此用户无法看到与之相关的任何文件。
		| mysql              |MySQL的核心数据库：主要负责存储数据库运行时的输出文件夹，字符集、数据库用户、权限设置、关键字等
								控制和管理信息。不可删除，否则MySQL将不能够正常运行，也不要轻易修改。
		| performance_schema |收集性能参数库：用于收集数据库服务器性能参数，用于监控数据库的性能指标。该数据库中所有表的存储引擎
								均为 ‘performance_schema’ ，而用户是不能创建存储引擎为此引擎的表的。
		| sys                |system数据库：同样是给开发人员监控数据库性能的。
		+--------------------+

	第三步：创建属于我们自己的数据库
		create database if not exists test [default character set 字符集] [collate 排序规则];
		drop database if exists test;(这俩也属于MySQL的命令)

		数据库创建之后，编码方式就确定了。修改数据库的编码方式，可以使用alter语句
		alter database 数据库名称 default character set 编码方式 collate 编码方式_bin;

	第四步：使用bjpowernode数据库
		use bjpowernode;(属于MySQL的命令)，如果没有先使用数据库，那么执行sql时需要指定哪个数据库中的表test.emp

	第五步：查看当前使用的数据库（mysql）中有哪些表？
		show tables;(属于MySQL的命令)
		show tables from bjpowernode;查看其他数据库中的表
		+---------------------------+
		| Tables_in_mysql           |
		+---------------------------+
		| columns_priv              |
		| db                        |
		| event                     |
		| func                      |
		| general_log               |
		| help_category             |
		| help_keyword              |
		| help_relation             |
		| help_topic                |
		| host                      |
		| ndb_binlog_index          |
		| plugin                    |
		| proc                      |
		| procs_priv                |
		| proxies_priv              |
		| servers                   |
		| slow_log                  |
		| tables_priv               |
		| time_zone                 |
		| time_zone_leap_second     |
		| time_zone_name            |
		| time_zone_transition      |
		| time_zone_transition_type |
		| user                      |
		+---------------------------+

	第六步：初始化数据
		mysql> source D:\studyMysql\course\bjpowernode.sql（注意：source不能加分号，同样也是mysql的命令，这个只能在cmd中）
		mysql> show tables;
		+-----------------------+
		| Tables_in_bjpowernode |
		+-----------------------+
		| dept                  |	（部门表）
		| emp                   |	（员工表）
		| salgrade              |	（工资等级表）
		+-----------------------+
	（以.sql结尾的文件并且该文件中编写了大量的sql语句，被叫做“sql脚本”）
	使用source可以执行sql脚本，当一个sql脚本文件太大的时候，记事本打不开
	该文件，请使用source命令完成初始化

==================================================================
1、查看表的结构：describe 表名; 还可以简写为desc
	describe dept;
				部门表
	+--------+-------------+------+-----+---------+-------+
	| Field  | Type        | Null | Key | Default | Extra |
	+--------+-------------+------+-----+---------+-------+
	| DEPTNO | int(2)      | NO   | PRI | NULL    |       |部门编号
	| DNAME  | varchar(14) | YES  |     | NULL    |       |部门名称
	| LOC    | varchar(13) | YES  |     | NULL    |       |部门位置
	+--------+-------------+------+-----+---------+-------+
	desc emp;
				员工表
	+----------+-------------+------+-----+---------+-------+
	| Field    | Type        | Null | Key | Default | Extra |
	+----------+-------------+------+-----+---------+-------+
	| EMPNO    | int(4)      | NO   | PRI | NULL    |       |员工编号
	| ENAME    | varchar(10) | YES  |     | NULL    |       |员工姓名
	| JOB      | varchar(9)  | YES  |     | NULL    |       |工作岗位
	| MGR      | int(4)      | YES  |     | NULL    |       |上级领导编号
	| HIREDATE | date        | YES  |     | NULL    |       |入职日期
	| SAL      | double(7,2) | YES  |     | NULL    |       |月薪
	| COMM     | double(7,2) | YES  |     | NULL    |       |不住/津贴
	| DEPTNO   | int(2)      | YES  |     | NULL    |       |部门编号
	+----------+-------------+------+-----+---------+-------+
	desc salgrade;
				工资等级表
	+-------+---------+------+-----+---------+-------+
	| Field | Type    | Null | Key | Default | Extra |
	+-------+---------+------+-----+---------+-------+
	| GRADE | int(11) | YES  |     | NULL    |       |等级
	| LOSAL | int(11) | YES  |     | NULL    |       |最低薪资
	| HISAL | int(11) | YES  |     | NULL    |       |最高薪资
	+-------+---------+------+-----+---------+-------+

2、查看建表语句和建库语句：show create table 表名; show create database 库名;
	+-------+----------------------------------+
	| Table | Create Table                     |
	+-------+----------------------------------+
	| dept  | CREATE TABLE `dept` (
		`DEPTNO` int(2) NOT NULL,
		`DNAME` varchar(14) DEFAULT NULL,
		`LOC` varchar(13) DEFAULT NULL,
		PRIMARY KEY (`DEPTNO`)
	) ENGINE=InnoDB DEFAULT CHARSET=utf8 	   |
	+-------+----------------------------------+

3、表中的数据？
	select * from emp;
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

	select * from dept;
	+--------+------------+----------+
	| DEPTNO | DNAME      | LOC      |
	+--------+------------+----------+
	|     10 | ACCOUNTING | NEW YORK |
	|     20 | RESEARCH   | DALLAS   |
	|     30 | SALES      | CHICAGO  |
	|     40 | OPERATIONS | BOSTON   |
	+--------+------------+----------+

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
```

```txt
从现在开始，学习的是通用的SQL语句==========================================================================

注意：
	1、mysql任何一个SQL语句以分号;结尾（其他数据库中可能不是必须的）
	2、SQL语句关键字不区分大小写，推荐大写；
	3、数据库中NULL不是一个值，代表什么也没有，为空，空不是一个值，不能用等号来衡量必须使用is null 或者 is not null；
		NULL和任何东西做运算结果还是NULL；
	4、and和or：and优先级较高，or优先级较低;如果运算符优先级不确定加小括号就行，不用死记
	5、in等同于or：in(,)/not in(,)
	6、转义字符：\ ，\_ 表示普通下划线
	7、字符串量都要用单引号'括起来（可以但不推荐""）
	8、标准sql中的注释：--单行注释，/*多行注释*/（#也是单行注释，是mysql中特有的）
	9、反引号``是mysql中的着重号，如果你的字段或表名和关键字冲突了，请用它括起来；

==========DQL============================================================================
简单查询：
	select 字段名1,字段名2,字段名3,字段名4..... from 表名;

			select ENAME,SAL*24 from emp;(查询的字段可以参与数学运算)
			+--------+-----------+
			| ENAME  | SAL*24    |
			+--------+-----------+
			| SMITH  |  19200.00 |
			| ALLEN  |  38400.00 |
			| WARD   |  30000.00 |
			| JONES  |  71400.00 |
			| MARTIN |  30000.00 |
			| BLAKE  |  68400.00 |
			| CLARK  |  58800.00 |
			| SCOTT  |  72000.00 |
			| KING   | 120000.00 |
			| TURNER |  36000.00 |
			| ADAMS  |  26400.00 |
			| JAMES  |  22800.00 |
			| FORD   |  72000.00 |
			| MILLER |  31200.00 |
			+--------+-----------+
	给查询结果的字段重新命名:select ENAME,SAL*24 as '年薪' FROM EMP;
	（注意：在标准SQL语句中，字符串以单引号'括起来）
			as关键字也可以省略，换成空格就行
			+--------+-----------+
			| ENAME  | 年薪      |
			+--------+-----------+
			| SMITH  |  19200.00 |
			| ALLEN  |  38400.00 |
			| WARD   |  30000.00 |
			| JONES  |  71400.00 |
			| MARTIN |  30000.00 |
			| BLAKE  |  68400.00 |
			| CLARK  |  58800.00 |
			| SCOTT  |  72000.00 |
			| KING   | 120000.00 |
			| TURNER |  36000.00 |
			| ADAMS  |  26400.00 |
			| JAMES  |  22800.00 |
			| FORD   |  72000.00 |
			| MILLER |  31200.00 |
			+--------+-----------+

	查询所有字段：select * from emp;
	（注意：这种方式实际开发中效率较低，java程序中不建议使用）
======================================================================
查询常量：
	select语句可以对常量进行查询展示，就是在结果集中新增一列固定的常量，例如：
		select 1 as sex, '张三' as '姓名', ename from emp;
		结果：
		+-------+-------+---------+
		| sex   | 姓名   | ename   |
		+-------+-------+---------+
		| 1     |  张三  |  WARD   |
		| 1     |  张三  |  ALLEN  |
		| 1     |  张三  |  SMITH  |
		| 1     |  张三  |   TOM   |
		+-------+--------+--------+
======================================================================
条件查询：
	select 字段1，字段2... from 表名 where 条件;
	执行顺序：先from，后where，最后select

	查询工资等于5000的员工姓名：select ENAME from emp where SAL=5000;
					+-------+
					| ENAME |
					+-------+
					| KING  |
					+-------+
	查询SMITH的工资：select SAL from emp where ENAME='SMITH';
					+--------+
					| SAL    |
					+--------+
					| 800.00 |
					+--------+
	找出工资大于3000的员工：select ENAME from emp where SAL>3000;
					+-------+
					| ENAME |
					+-------+
					| KING  |
					+-------+
	找出工资不等于3000的：select ENAME from emp where SAL<>3000;
				或者：select ENAME from emp where SAL!=3000;
					+--------+
					| ENAME  |
					+--------+
					| SMITH  |
					| ALLEN  |
					| WARD   |
					| JONES  |
					| MARTIN |
					| BLAKE  |
					| CLARK  |
					| KING   |
					| TURNER |
					| ADAMS  |
					| JAMES  |
					| MILLER |
					+--------+
	找出工资在1100到3000的之间的员工，包括1100和3000：select ENAME,SAL from emp where SAL>=1100 and SAL<=3000;
								或者：select ENAME,SAL from emp where SAL between 1100 and 3000;//注意，闭区间，且前面的必须小
				+--------+---------+
				| ENAME  | SAL     |
				+--------+---------+
				| ALLEN  | 1600.00 |
				| WARD   | 1250.00 |
				| JONES  | 2975.00 |
				| MARTIN | 1250.00 |
				| BLAKE  | 2850.00 |
				| CLARK  | 2450.00 |
				| SCOTT  | 3000.00 |
				| TURNER | 1500.00 |
				| ADAMS  | 1100.00 |
				| FORD   | 3000.00 |
				| MILLER | 1300.00 |
				+--------+---------+
	
	*between and 除了使用在数字方面，也可以使用在字符串方面：左闭右开（了解一下，不用掌握）
		select ename from emp where ename between 'A' and 'C';
			+-------+
			| ename |
			+-------+
			| ALLEN |
			| BLAKE |
			| ADAMS |
			+-------+

	找出哪些人没有津贴：select ENAME, COMM from emp where COMM is null or COMM=0;
				+--------+------+
				| ENAME  | COMM |
				+--------+------+
				| SMITH  | NULL |
				| JONES  | NULL |
				| BLAKE  | NULL |
				| CLARK  | NULL |
				| SCOTT  | NULL |
				| KING   | NULL |
				| TURNER | 0.00 |
				| ADAMS  | NULL |
				| JAMES  | NULL |
				| FORD   | NULL |
				| MILLER | NULL |
				+--------+------+

	in(,):的用法，等同于or
	select ENAME, SAL from emp where SAL in(800,1100);
				+-------+---------+
				| ENAME | SAL     |
				+-------+---------+
				| SMITH |  800.00 |
				| ADAMS | 1100.00 |
				+-------+---------+

	和in相反的：not in(,);

=======================================================================================
模糊查询：like % _ [] ^
			like		写在字段和模糊查询串之间，出现like表示你要用模糊查询了（标准SQL）
			%			表示0到多个任意字符（标准SQL）
			_			表示一个任意字符（标准SQL）
			[]			表示一个这个范围内的字符，[]写一个范围，例如：[^579]表示不要5或7或9
			^			写在[]内部的开头，表示不使用这个范围内的任何字符
模糊查询中，必须掌握两个符号：%和_（%代表任意多个字符，_代表任意一个字符）
模糊查询也可以用在非字符串字段上面

	找出名字中带有O的：select * from emp where ename like '%O%';
	+-------+-------+---------+------+------------+---------+------+--------+
	| EMPNO | ENAME | JOB     | MGR  | HIREDATE   | SAL     | COMM | DEPTNO |
	+-------+-------+---------+------+------------+---------+------+--------+
	|  7566 | JONES | MANAGER | 7839 | 1981-04-02 | 2975.00 | NULL |     20 |
	|  7788 | SCOTT | ANALYST | 7566 | 1987-04-19 | 3000.00 | NULL |     20 |
	|  7902 | FORD  | ANALYST | 7566 | 1981-12-03 | 3000.00 | NULL |     20 |
	+-------+-------+---------+------+------------+---------+------+--------+

	找出第二个字符是A的：select * from emp where ENAME like '_A%';
	+-------+--------+----------+------+------------+---------+---------+--------+
	| EMPNO | ENAME  | JOB      | MGR  | HIREDATE   | SAL     | COMM    | DEPTNO |
	+-------+--------+----------+------+------------+---------+---------+--------+
	|  7521 | WARD   | SALESMAN | 7698 | 1981-02-22 | 1250.00 |  500.00 |     30 |
	|  7654 | MARTIN | SALESMAN | 7698 | 1981-09-28 | 1250.00 | 1400.00 |     30 |
	|  7900 | JAMES  | CLERK    | 7698 | 1981-12-03 |  950.00 |    NULL |     30 |
	+-------+--------+----------+------+------------+---------+---------+--------+

--------------------------------------------
排序：order by（升序asc、降序desc）
	对结果集进行排序，默认是升序asc，降序是desc（注意，和查询表结构的关键字一样）

		找出员工名和薪资，按照员工工资排序：select ENAME as '员工姓名', SAL as '薪资' from emp order by SAL asc;
		+----------+---------+
		| 员工姓名 | 薪资    |
		+----------+---------+
		| SMITH    |  800.00 |
		| JAMES    |  950.00 |
		| ADAMS    | 1100.00 |
		| WARD     | 1250.00 |
		| MARTIN   | 1250.00 |
		| MILLER   | 1300.00 |
		| TURNER   | 1500.00 |
		| ALLEN    | 1600.00 |
		| CLARK    | 2450.00 |
		| BLAKE    | 2850.00 |
		| JONES    | 2975.00 |
		| FORD     | 3000.00 |
		| SCOTT    | 3000.00 |
		| KING     | 5000.00 |
		+----------+---------+
		按照工资的降序排，如果工资一样，再按照名字的升序排：select ENAME as '员工姓名', SAL as '薪资' from emp order by SAL desc, ENAME asc;
		注意：越靠前的字段越能起到主导作用。后面的字段可能压根用不上
		+----------+---------+
		| 员工姓名 | 薪资    |
		+----------+---------+
		| KING     | 5000.00 |
		| FORD     | 3000.00 |
		| SCOTT    | 3000.00 |
		| JONES    | 2975.00 |
		| BLAKE    | 2850.00 |
		| CLARK    | 2450.00 |
		| ALLEN    | 1600.00 |（也可以这样写：select ENAME as '员工姓名', SAL as '薪资' from emp order by 2; 2是表的第二个字段SAL，但是不建议这样用）
		| TURNER   | 1500.00 |
		| MILLER   | 1300.00 |
		| MARTIN   | 1250.00 |
		| WARD     | 1250.00 |
		| ADAMS    | 1100.00 |
		| JAMES    |  950.00 |
		| SMITH    |  800.00 |
		+----------+---------+


		select			执行顺序：
			字段			3
		from
			表名			1
		where
			条件			2
		order by
			排序条件;		4
=================================运算符========================================================================================
# 逻辑运算符：and或&&、or或||、not或!、XOR（异或）

# 算数运算符：类似于Java语言，sql中的运算符同样有+-*/%，但是+没有拼接的功能，而且做运算时如果是非数值型，那么会自动进行类型转换，转换失败当作0；

# 比较运算符：=等于、<=>安全等于、<>或!=不等于、<、<=、>、>=；（安全等于<=>和等于=类似，无非是出现null的时候也能完成判断，而=只要出现null结果就是null）

# 关键字运算符：is null、is not null、between and、in(a,b,..)、not in()、like、regexp/rlike（用法相同，'aab' regexp '^a'，返回布尔值）

# （了解）位运算符：| & ^ ~ >> <<
```

```txt
mysql的数据处理函数===================================================================================================

# 字符串相关：
	upper('abc')和ucase('abc')转大写
	lower('ABC')和lcase('ABC')转小写
	concat('a','b','c')字符串拼接
	substr('abcdef',index,length)截取字符串
	length('str')字符串占几个字节
	char_length('str')几个字符
	trim(' name ')去除前后空白；trim(leading '0' from '0012')去掉前置0，和trim(trailing '0' from '1200')去除后置0，
		和trim(both '0' from '001200')去除前后0

# 数字相关：
	rand()生成[0-1)的随机数，参数可以传进去一个整数作为随机种子；
	round(3.1415)四舍五入，round(3.14,1)保留一位小数
	truncate(3.14,1)保留1位小数剩下的舍去
	ceil(3.1)和floor(3.1)向上取整和向下取整
	least(a,b,..)取最小值
	greatest(a,b,..)取最大值

# 空处理函数：ifnull(x,0)如果x为null当作0处理；isnull(x)判断该值是不是null，返回布尔值

# 日期相关的函数和属性：
	获取当前日期和时间：now()获取当前sql语句开始执行的时刻，sysdate()获取执行到sysdate函数的时刻
	只获取当前日期：curdate()\current_date()\current_date
	只获取当前时间：curtime()\current_time()\current_time

	获取单独的指定的日期时间：年year(now())、月month(now())、日day(now())、时hour(now())、分minute(now())、秒second(now())，注意参数是date型的数据；
	加日期：date_add(日期,interval 3 day)，日期加3天，单位可以是month、year等，也可以复合('2021-02-11',interval '3,2' day_hour)
	判断日期是第几天：dayofweek(date型) / dayofmonth(date型) / dayofyear(date型)
	获取日期所在月的最后一天的日期：last_day(date型)
	datediff(date型1,date型2)计算两个日期差的天数
	timediff(date型1,date型2)两个时间差xx:xx:xx

# 日期和字符串转换：
	str_to_date('字符串','日期格式')：将字符串根据日期格式转成date型数据并返回；通常用于新增日期型数据；

	date_format(日期型,'日期格式')：将date型数据格式化成字符串返回；日期格式：
							%Y	4位年
							%y	2位年
							%m	月
							%d	日
							%h	时
							%i	分
							%s	秒（mysql默认的日期格式是'%Y-%m-%d %h:%i:%s'）
		（控制台上都是字符串，其实data型输出到控制台上都有自动类型转换，都调用了该函数）

# 流程控制函数：
	# if(布尔表达式,'yes','no')数据中新增一列，如果true就输出yes，否则输出no，可以嵌套；
	# case when用法1：case when 条件1 then 结果1 when 条件2 then 结果2 ..[else 结果n] end
	# case when用法2：case 表达式 when 常量1 then 结果1 when 常量2 then 结果2 .. [else 结果n] end
		（就是新增一列，每一列结果会根据情况选择输出不同结果，用法1类似if else，用法2类似switch）

# case函数：用于做类型转换，cast(值 as 数据类型)，其中数据类型可以是：date/datetime/time/signed有符号int/char定长字符串/decimal浮点型；

# 加密函数：
	md5(str)：用于md5加密，md5('sfad')可以将给定字符串，用md5算法加密成32位定长的字符串，通常无法解密；参数为null则返回null；
	sha(str)：通常是不可逆的，它比md5更加安全，mysql8.0的root就是用了这种加密方式；参数为null则返回null；

# mysql信息函数：可以看数据库的一些信息，其中一些函数我们刚开始用过
	version()返回mysql版本号
	schema()/database()返回当前使用的数据库
	connection_id()返回当前链接的id
	user()/current_user()/system_user()/session_user()返回当前用户名，格式为：用户名@链接的ip
	charset('任意字符串')当前数据库使用的字符集
	collation('任意字符串')当前数据库的比较规则，它是和字符集一一对应的

================================================================================================================
分组函数：（也叫：多行处理函数，显示的查询结果是一条数据）
	count:	计数
	sum:	求和
	avg：	平均值
	max:	最大值
	min:	最小值
注意：所有的分组函数都是对“某一组”数据进行操作的
	一共5个:
		找出工资总和：select sum(SAL) from emp;
		找出最高工资：select max(SAL) from emp;
		找出最低工资：select min(SAL) from emp;
		找出平均工资：select avg(SAL) from emp;
		找出总人数：select count(*) from emp;
			或者select count(ENAME) from emp;

（重点：）		以上两种找出总人数的方式有什么区别？
			count(*)，和字段没关系，所有的数据条数；count(字段)，字段中不为null的数据条数

注意：	
	1、分组函数自动忽略null
	2、分组函数不能够直接出现在where子句中，why？
	因为group by是在where执行之后才执行的。分组函数是在分完组之后才执行的
			select			5
				..
			from			1
				..
			where			2
				..
			group by		3
				..
			having			4
				..
			order by		6
				..

	3、分组函数也能够组合起来使用：
				select count(*),avg(sal),min(sal),max(sal),sum(sal) from emp;
				+----------+-------------+----------+----------+----------+
				| count(*) | avg(sal)    | min(sal) | max(sal) | sum(sal) |
				+----------+-------------+----------+----------+----------+
				|       14 | 2073.214286 |   800.00 |  5000.00 | 29025.00 |
				+----------+-------------+----------+----------+----------+
------------------------------------------------------------------------------------

group by 和 having：

	group by：按照某个字段或者某些字段进行分组
	having：having是对分组之后的数据不满意的话，进行再次过滤（能用where过滤的话就用where先过滤掉）
	例如：查找每个部门的最高薪资，且只显示高于2900的
	select max(sal),deptno from emp group by deptno having max(sal)>2900;
	+----------+--------+
	| max(sal) | deptno |
	+----------+--------+
	|  5000.00 |     10 |
	|  3000.00 |     20 |
	+----------+--------+
	2 rows in set (0.00 sec)

注意：分组函数一般都会和group by联合使用，这也是它为什么被称为
	分组函数的原因，并且分组函数都是在group by执行之后才执行的，
	当一条sql语句没有group by的话，整张表的数据会自成一组。默认有个group by，此时select后面只能出现分组函数

	案例1：找出每个工作岗位的最高薪资
		select max(sal),job from emp group by job;
			+----------+-----------+
			| max(sal) | job       |
			+----------+-----------+
			|  3000.00 | ANALYST   |
			|  1300.00 | CLERK     |
			|  2975.00 | MANAGER   |
			|  5000.00 | PRESIDENT |
			|  1600.00 | SALESMAN  |
			+----------+-----------+

	案例2：找出工资高于平均工资的员工
		select ename,sal from emp where sal > (select avg(sal) from emp);
			+-------+---------+
			| ename | sal     |
			+-------+---------+
			| JONES | 2975.00 |
			| BLAKE | 2850.00 |
			| CLARK | 2450.00 |
			| SCOTT | 3000.00 |
			| KING  | 5000.00 |
			| FORD  | 3000.00 |
			+-------+---------+

====================================================================================
(重点)：当sql中有group by的时候，select后面只能跟“分组函数”和“参与分组的字段”
（或者在分组函数后面加as over()，这时分组结果会平铺到每条记录中去）
====================================================================================

多个字段能不能联合起来一块分组？可以

	案例：找出每个部门不同工作岗位的最高薪资。
		select deptno,job,max(sal) from emp group by deptno,job;
		它会默认认为deptno和job两个字段连在一起，都一样的话是一组
			+--------+-----------+----------+
			| deptno | job       | max(sal) |
			+--------+-----------+----------+
			|     10 | CLERK     |  1300.00 |
			|     10 | MANAGER   |  2450.00 |
			|     10 | PRESIDENT |  5000.00 |
			|     20 | ANALYST   |  3000.00 |
			|     20 | CLERK     |  1100.00 |
			|     20 | MANAGER   |  2975.00 |
			|     30 | CLERK     |   950.00 |
			|     30 | MANAGER   |  2850.00 |
			|     30 | SALESMAN  |  1600.00 |
			+--------+-----------+----------+

-----------------------------------------------------------------------------------
总结：一个完整的SQL语句应该怎么写？


	select			5
		...
	from			1
		...
	where			2
		...
	group by		3
		...
	having			4
		...
	order by		6
		...


==================================================================================
去重：

关于查询结果集的去重：distinct
	select distinct job from emp;
（distinct只能出现在所有字段的最前面）

	select distinct deptno,job from emp order by deptno;表示deptno和job联合起来去重
	+--------+-----------+
	| deptno | job       |
	+--------+-----------+
	|     10 | CLERK     |
	|     10 | MANAGER   |
	|     10 | PRESIDENT |
	|     20 | ANALYST   |
	|     20 | CLERK     |
	|     20 | MANAGER   |
	|     30 | CLERK     |
	|     30 | MANAGER   |
	|     30 | SALESMAN  |
	+--------+-----------+
	案例：统计工作岗位的数量：select count(distinct job) from emp;

关于mysql的字符集：
在mysql8之前，5.7的时候，默认的字符集还是latin1，如果创建数据库没有指定字符集，是存不了中文数据的；
```