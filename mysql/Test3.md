```txt
=====================表的创建=======================================================================

建表语句的语法格式：
	create table 表名(
		字段1 数据类型 [comment '注释'],
		字段2 数据类型 [comment '注释'],
		字段3 数据类型 [comment '注释'],
		.....
	) [comment '注释'];

关于MySQL当中字段的数据类型？（常见的）
	tinyint			byte型（1b）
	smallint		short型（2b）
	mediumint		3个字节（3b）
	int/integer		int型（4b）
	bigint			long型（8b）
	float			单精度浮点型（4b）
	double			双精度浮点型（8b）
	decimal(x,y)	定点型小数，x是共几个位数，y是小数位数，不带小数点（9b~16b）

	char(x)			定长字符串(String)，x范围[0-255]，性能较高，具体大小看使用的编码
	varchar(x)		变长字符串(StringBuffer/StringBuilder)，x范围[0-16383]，性能较低；默认不指定x是varchar(255)
	（上面两个存文本不够用了，才会用下面这些，通常都是text或longtext）
	tinytext		不超过255个字符，不管底层采用了什么编码（1b个字符）
	text			不超过65535个字符（2b个字符）
	mediumtext		不超过16777215个字符（3b个字符）
	longtext		不超过4294967295个字符（4b个字符）
	（ENUM和SET可以帮助限制字段值的范围）
	enum('值1','值2',..)	只能插入预定义的值
	set('值1','值2',..)		和enum类似，不过它可以取预定义列表中的，一个元素或者多个元素的组合。取多个元素时，不同元素之间用逗号隔开；

	tinyblob		不超过255个字节的二进制数据，对应Java中的Object类型（1b）
	blob			不超过65535个字节（2b）
	mediumblob		不超过16777215个字节（3b）
	longblob		不超过4294967295个字节（4b）

	date			日期类型，只描述日期（3b）
	time			时间类型，只描述时间（3b）
	year			年份类型，只描述4位年份（1b）
	datetime		日期加时间类型（8b）
	timestamp		日期加时间类型，是时间戳，最大到2038年（4b）（该类型插入数据时可以只写长数字：20220912121212）
	（mysql大部分代码是由c++写的，所以这些类型在c++中都有对应的实现）

表名在mysql数据库中一般建议以： t_ 或者 tbl_ 开始

	*案例：创建一个学生表：
		学生信息包括：学号、姓名、性别、班级编号、生日
			学号：bigint
			姓名：varchar
			性别：char
			班级编号：int
			生日：char

		create table t_student(
			no bigint,
			name varchar(255),
			sex char(1), //这里后面如果有：default 1 这样插入数据没有赋值就会用默认值
			classno varchar(255),
			birth char(10)
		);

-----------删除表-----------------------------------------------------------------------------------------------
drop table [if exists] 表名;	//这个操作直接给表干没了
-----------查看建表语句------------------------------------------------------------------------------------------
show create table 表名;
-----------修改表结构-----------------------------------------------------------------------------------------------
	修改表名：
		alter table 旧表名 rename 新表名;
	添加字段：
		alter table 表名 add 新字段 数据类型 [约束条件] [firse|after 已经存在的字段名];
		最后的参数用于，first将新添加的字段设置为表的第一个字段，或after将新字段添加到指定字段的后面，默认将新字段追加到表尾；
	修改字段名和数据类型：
		alter table 表名 change 旧字段 新字段 字段的数据类型;
	修改字段的数据类型：
		alter table 表名 modify 字段名 新数据类型;
	删除字段：
		alter table 表名 drop 字段名;

	为表添加或删除主键：
		添加：alter table 表名 modify 字段名 数据类型 primary key;
		删除：alter table 表名 drop primary key;

=====================表的复制=======================================================================

# 复制表结构和数据，语法：create table 新表 as select语句;（表结构相同，约束相同）
	例如：create table dept1 as select * from dept;

# 只复制表结构：create table 新表 like 表名;

=====================DML语句=======================================================================
----------------
insert语句插入数据：
	#语法：insert into 表名(字段名1,字段名2,字段名3....) values(值1,值2,值3...);
		（注意：字段的数量和值的数量相同，并且数据类型要对应相同）

		insert into t_student(no,name,sex,classno,birth) values(1,'zhangsan','1','gaosan1ban','1950-10-12');
		+------+----------+------+------------+------------+
		| no   | name     | sex  | classno    | birth      |
		+------+----------+------+------------+------------+
		|    1 | zhangsan | 1    | gaosan1ban | 1950-10-12 |
		+------+----------+------+------------+------------+

	#insert语句只要执行成功，表格当中必然会多出一条记录，其他的字段自动为null；如果想要更改这条记录，只能用update语句；

	#字段也可以省略，但是values的值必须和表字段顺序对上：insert into t_student values(no值,name值,...);

	#一次插入多行数据：insert into t_student() values (3,'rose','gaosi2ban','1994-12-31'),()...;

	#用insert语句，将查询结果集插入到另一张表：
		语法：insert into 表名 select语句

----------------
update语句修改数据：
	#语法：update 表名 set 字段名1=值1,字段名2=值2...where 条件;
	（注意：没有条件整张表都会更新）

	案例：将部门10的LOC修改为SHANGHAI,将部门名修改为RENSHIBU
	update dept1 set loc = 'SHANGHAI', dname = 'RENSHIBU' where deptno = 10;

	select * from dept1;
	+--------+------------+----------+
	| DEPTNO | DNAME      | LOC      |
	+--------+------------+----------+
	|     10 | RENSHIBU   | SHANGHAI |
	|     20 | RESEARCH   | DALLAS   |
	|     30 | SALES      | CHICAGO  |
	|     40 | OPERATIONS | BOSTON   |
	|     10 | RENSHIBU   | SHANGHAI |
	|     20 | RESEARCH   | DALLAS   |
	|     30 | SALES      | CHICAGO  |
	|     40 | OPERATIONS | BOSTON   |
	+--------+------------+----------+

	更新所有记录，不写where即可
		update dept1 set loc = 'x', dname = 'y';
		+--------+-------+------+
		| DEPTNO | DNAME | LOC  |
		+--------+-------+------+
		|     10 | y     | x    |
		|     20 | y     | x    |
		|     30 | y     | x    |
		|     40 | y     | x    |
		|     10 | y     | x    |
		|     20 | y     | x    |
		|     30 | y     | x    |
		|     40 | y     | x    |
		+--------+-------+------+

----------------
delete语句删除数据：（表占用内存不释放，数据还可以回滚）
	#语法：delete from 表名 where 条件;
	（注意：没有条件数据会全部删除）
		例如：
		删除10部门数据？
			delete from dept1 where deptno=10;
		删除所有记录？
			delete from dept1;

	#彻底截断表中的数据？（不可回滚，永久丢失）
		语法：truncate table 表名;	//不属于DML语句，是DDL，DML操作都支持事务的
		（与DELETE语句相比，TRUNCATE更快，因为它不记录每条删除的记录，而只是简单地删除整个表的内容；
			并且，TRUNCATE不会触发任何ON DELETE触发器，要注意）
----------------------------------------------------------------------------------------------------
以上对表数据增删改查统一称作“CRUD”，是指，Create（增） Retrieve（查，检索/瑞退v） Update（修改） Delete（删除）
```

```txt
约束（Constraint康斯醇特）：

什么是约束？常见的约束有哪些？
	在创建表的时候，可以给表的字段添加相应的约束，添加约束的目的是为了保证表中数据的
	合法性、有效型、完整性
	常见的约束有哪些？
		非空约束（not null）：约束的字段不能为NULL
		唯一约束（unique）：约束的字段不能重复
		主键约束（primary key）：约束的字段既不能为NULL，也不能重复，简称（PK）
		外键约束（foreign key）：...（简称FK）
		（mysql没有）检查约束（check）：注意Oracle和SQL Server有check约束，mysql低版本不支持该约束
		默认约束（default）：给字段赋一个默认值

==================================================================================================
1、非空约束：not null
	drop table if exists t_user;
	create table t_user(
		id int,
		username varchar(255) not null,
		password varchar(255)//这里的255：在mysql 5.0.3之前表示n个字节，之后表示n个字符，按照实际使用的数据库字符编码集，占用不同字节数量
	);
	insert into t_user(id,password) values(1,'123');
	这个出问题了：ERROR 1364 (HY000): Field 'username' doesn't have a default value
	mysql> desc t_user;
	+----------+--------------+------+-----+---------+-------+
	| Field    | Type         | Null | Key | Default | Extra |
	+----------+--------------+------+-----+---------+-------+
	| id       | int(11)      | YES  |     | NULL    |       |
	| username | varchar(255) | NO   |     | NULL    |       |
	| password | varchar(255) | YES  |     | NULL    |       |
	+----------+--------------+------+-----+---------+-------+
注意：not null只有列级约束，没有表级约束，也就是不能多个联合非空

删除非空/默认/自增:
alter table 表名 modify 字段名 数据类型;
=============================================================================================
2、唯一性约束：（unique）

	唯一性约束修饰的字段具有唯一性，不能重复

	案例：
		给某一列添加unique
		drop table if exists t_user；
		create table t_user(
			id int,
			username varchar(255) unique	//列级约束
		);

	案例：给两个列或者多个列添加unique
	drop table if exists t_user;
	create table t_user(
		id int,
		usercode varchar(255),
		username varchar(255),
		unique(usercode,username)	//表级约束：多个字段联合起来唯一就行
	);

删除唯一约束：
	alter table 表名 drop index 字段名;
（注意：唯一性约束的实现方式有很多种。在MySQL中，唯一性约束的本质上是一个唯一索引）
=========================================================================================
3、主键约束：primary key（牢记：一张表的主键约束只能有一个）

	怎么给一张表添加主键约束？
		drop table if exists t_user;
		create table t_user(
			id int primary key,		//主键约束有列级和表级约束两种
			username varchar(255),
			email varchar(255)
		);

	添加了主键约束的字段，unique且not null

主键的作用：
	主键的设计三范式要求，第一个范式就要求一个表必须要有主键

主键的分类？
	根据主键字段的字段数量来划分：
		单一主键	（推荐的，常用的）
		复合主键	（多个字段联合起来添加一个主键约束，用表级约束的方式添加主键）不建议用，违背了三范式。
	根据主键性质来划分：
		自然主键：主键值最好就是一个和业务没有关系的自然数（推荐）
		业务主键：（不推荐用）主键值和系统的业务挂钩，例如：拿着身份证号码作为身份信息表的主键、拿着银行卡号码作为主键

mysql提供主键值自增：
	drop table if exists t_user;
	create table t_user(
		id int primary key auto_increment,	//只需要在后面加上auto_increment
		username varchar(255)
	);

Oracle中也提供了一个自增机制，叫做：序列（sequence）
```

```txt
外键约束：

	关于外键约束的相关术语：
		外键约束：foreign key
		外键字段：添加有外键约束的字段
		外键值：  外键字段中的每一个值

案例：创建表，维护学生和班级信息
		t_class 班级表
		cno(pk)		cname
		---------------------------------------------------------------------
		101			河南省周口市西华县洼村3小五年级101班
		102			河南省周口市西华县洼村3小五年级102班


		t_student 学生表
		sno(pk)		sname		classno(该字段添加外键约束fk)
		-------------------------------------------------------------------
		1			zs1			101
		2			zs2			102
		3			zs3			101
		4			zs4			101
		5			zs5			102

将以上表的建表语句写出来：
	t_student中的classno字段引用t_class中的cno字段，此时t_student叫做子表，t_class表为父表
	顺序要求：
		删除数据的时候，先删除子，再删除父
		添加数据的时候，先添加父，再添加子
		创建表的时候，先创建父表，再创建子表
		删除表的时候，先删除子表，再删除父表

	drop table if exists t_student;
	drop table if exists t_class;

	create table t_class(
		cno int,
		cname varchar(255),
		primary key(cno)
	);
	create table t_student(
		sno int,
		sname varchar(255),
		classno int,
		foreign key(classno) references t_class(cno)
	);

[CONSTRAINT <外键名>] FOREIGN KEY (字段名,...) REFERENCES <主表名> (字段1,...)
添加外键约束时“CONSTRAINT [约束名]”可以省略，如果没有用CONSTRAINT设置约束名， 系统会自动生成一个约束名
查看建表语句可以看到自动的外键名：show create table 表名;

	insert into t_class values(101,'河南省周口市西华县洼村3小五年级101班');
	insert into t_class values(102,'河南省周口市西华县洼村3小五年级102班');

	insert into t_student values(1,'zs1',101);
	insert into t_student values(2,'zs2',102);
	insert into t_student values(3,'zs3',101);
	insert into t_student values(4,'zs4',102);
	insert into t_student values(5,'zs5',101);
	然后尝试插入数据：
	insert into t_student values(6,'lisi',103);
	报错：ERROR 1452 (23000): Cannot add or update a child row: a
		foreign key constraint fails (`bjpowernode`.`
		t_student`, CONSTRAINT `t_student_ibfk_1` FOREIGN
		KEY (`classno`) REFERENCES `t_class` (`cno`))

注意：
	1、外键值是可以为NULL的
	2、外键references引用的字段不一定非得是主键，但是被引用的字段至少具有唯一性（unique）约束

删除外键：alter table 表名 drop foreign key 外键名;
```

```txt
存储引擎：
	建表的时候可以指定存储引擎，也可以指定字符集
	完整的建表语句：

	CREATE TABLE `t_x` (
	`id` int(11) DEFAULT NULL
	) ENGINE=InnoDB DEFAULT CHARSET=utf8;

	mysql默认使用的存储引擎是InnoDB方式，默认采用的字符集是UTF-8

	注意：在MySQL中，凡是“标识符”是可以使用飘号括起来的（`）
	最好别用，不通用，其他数据库不行

修改表的存储引擎：alter table 表名 ENGINE = MyISAM;
====================================================================================================================
修改mysql客户端字符编码命令:		set character_set_client = gbk;
	（注意：这种方式只可以在本命令窗口有效，如果要永久有效，需要修改my.ini配置文件，路径为：C:\Program Files\MySQL\MySQL Server 5.5\my.ini）
\s 或 statud 可以查看MySQL的状态信息
=========================================================================================================================

什么是存储引擎呢？
	存储引擎这个名字只有在mysql中存在。（Oracle中也有对应的机制，但是不叫这个名字，就叫表的存储方式）
	每一个存储引擎都有自己的优缺点，需要在合适的实际选择合适的存储引擎

查看当前mysql支持的存储引擎：(5.5.62版本有9个存储引擎)
	show engines \G（可省略\g）

	用一下：
	select version();
	+-----------+
	| version() |
	+-----------+
	| 5.5.62    |
	+-----------+


show engines \G
*************************** 1. row ***************************
      Engine: FEDERATED
     Support: NO
     Comment: Federated MySQL storage engine
Transactions: NULL
          XA: NULL
  Savepoints: NULL
*************************** 2. row ***************************
      Engine: MRG_MYISAM
     Support: YES
     Comment: Collection of identical MyISAM tables
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 3. row ***************************
      Engine: MyISAM
     Support: YES
     Comment: MyISAM storage engine
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 4. row ***************************
      Engine: BLACKHOLE
     Support: YES
     Comment: /dev/null storage engine (anything you write to it disappears)
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 5. row ***************************
      Engine: CSV
     Support: YES
     Comment: CSV storage engine
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 6. row ***************************
      Engine: MEMORY
     Support: YES
     Comment: Hash based, stored in memory, useful for temporary tables
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 7. row ***************************
      Engine: ARCHIVE
     Support: YES
     Comment: Archive storage engine
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 8. row ***************************
      Engine: InnoDB
     Support: DEFAULT
     Comment: Supports transactions, row-level locking, and foreign keys
Transactions: YES
          XA: YES
  Savepoints: YES
*************************** 9. row ***************************
      Engine: PERFORMANCE_SCHEMA
     Support: YES
     Comment: Performance Schema
Transactions: NO
          XA: NO
  Savepoints: NO


=================================================================================
常见的存储引擎：以下这三个只有InnoDB支持XA（分布式事务）、TX（事务）、Savepoints（事务的部分回滚）
--------------------------------------------------
	Engine: MyISAM
	Support: YES
	Comment: MyISAM storage engine
	Transactions: NO
	XA: NO
	Savepoints: NO

	MyISAM这种存储引擎是MySQL最常用的，但是不支持事务，不是默认的

		它管理的表具有一下特征：
			-使用三个文件表示每个表：
				格式文件-存储表结构的定义（xxx.frm）
				数据文件-存储表行的内容（xxx.MYD）
				索引文件-存储表上索引（xxx.MYI）
			-灵活的AUTO_INCREMENT字段处理
			-可被压缩、节省存储空间。并且可以被转换为只读表，提高检索效率
			缺点：不支持事务
-------------------------------------------------------
	Engine: InnoDB（事务、安全、重量级）
	Support: DEFAULT
	Comment: Supports transactions, row-level locking, and foreign keys
	Transactions: YES
	XA: YES
	Savepoints: YES

		优点：支持事务、行级锁、外键等。数据的安全得到了保障
		缺点：处理速度不是最快的，而且不能够被压缩，无法转换成只读文件

		表的结构存储在xxx.frm文件中
		数据存储在tablespace这样的表空间中（逻辑概念，不是文件）
这种InnoDB存储引擎在mysql服务器崩溃后提供自动恢复机制。（一般开发都用这个）

（开发中很少用这项功能，很危险）
支持级联删除和级联更新
	级联删除：指当外键中的父表中数据被删除后，与它有关联子表中的数据也被删除
	级联更新：当外键中的父表中数据更新后，与它有关联子表中的数据也被更新

--------------------------------------------------------------
	Engine: MEMORY	（以前叫作HEPA引擎）
	Support: YES
	Comment: Hash based, stored in memory, useful for temporary tables
	Transactions: NO
	XA: NO
	Savepoints: NO

	缺点：不支持事务，数据容易丢失，因为所有数据和索引都是存储在内存当中，
	支持表级锁机制，表中不能包含CLOB和BLOB格式
	优点：查询速度最快
```

```txt
事务（Transaction）
	1、什么是事务？
		一个事务是一个完整的业务逻辑单元，不可再分。
		比如：银行账户转账，从A账户向B账户转账10000元，需要执行两条update语句：
			update t_act set balance = balance - 10000 where actno = 'actno-001';
			update t_act set balance = balance + 10000 where actno = 'actno-002';

		以上两条DML语句必须同时成功或者同时失败，不允许一条成功一条失败的情况
		要保证以上两条DML语句同时成功或失败，就要使用数据库的“事务机制”了

	2、和事务相关的语句只有DML语句（insert\delete\update）
		为什么？因为它们三个语句都是和数据库表中“数据”相关的。
		事务的存在就是为了保证数据的完整性，安全性。

	3、假设所有的业务都能够使用1条DML语句搞定，还需要事务机制吗？不需要！
		通常一个“业务”需要多条DML语句来完成。

	4、事务的特性？
		事务包括四大特性：ACID
		A：原子性（atomicity）：事务是最小的工作单元，不可再分
		C：一致性（consistency）：事务必须保证多条DML语句同时成功或失败
		I：隔离性（isolation）：事务A与事务B之间具有隔离
		D：持久性（durability）：持久性说的是最终数据必须持久化到硬盘文件中，事务才算成功的结束

	5、关于事务之间的隔离性
		事务隔离性存在隔离级别，理论上隔离级别（从不安全到安全）包括4个：
			第一级别：读未提交（read uncommitted），一般现在数据库都不用这个级别了
				对方事务还没有提交，我们当前事务可以读取到对方未提交的数据。
				读未提交存在脏读（Dirty Read）现象:表示读到了脏的数据
			第二级别：读已提交（read committed）
				对方事务提交之后的数据我方可以读取到。
				这种隔离级别解决了：脏读现象没有了。
				读已提交存在的问题是：不可重复读。
			第三级别：可重复读（repeatable read）
				这种隔离级别解决了：不可重复读。
				这种隔离级别存在的问题是：读取到的数据是幻象。
			第四级别：序列化读/串行化读（serializable）
				解决了所有问题。
				效率低。需要事务排队。

			Oracle数据库默认的隔离级别是：二档：读已提交
			MySQL数据库默认的隔离级别更牛是：三档：可重复读

-------------------------------------------------------------------------------------------
演示事务：
	mysql事务默认情况下是自动提交的，只要执行任意一条DML语句则提交一次；
		*怎么开启事务：start transaction; 此时commit()或rollback()之后事务结束；后面的sql还是正常的一条sql一个事务；
		*关闭自动提交：set autocommit = 0; 此时在当前会话中，每个sql只有commit()或rollback()之后才会提交，通过它们来区分不同的事务；
	（commit提交、rollback回滚、savepoint 保存点名字、rollback 保存点名字）

设置全局的事务隔离级别为：读未提交
set global(全局)/session(当前会话) transaction isolation level read uncommitted;

查看事务的全局（当前）隔离级别：select @@global.tx_isolation; （select @@session.tx_isolation;）

注意：
MySQL的session和global一般使用在终端，用来对配置进行暂时设置，当数据库服务重启就会失效。session和global体现在新的设置生效的范围

session：当前会话，也就是当前连接立即生效。所以设置的这个级别必须保持打开的窗口一致，每个窗口都要执行以下修改语句

global：全局，不包含当前连接，之后新获取的连接都会生效。所以设置的这个级别必须退出当前窗口重新链接mysql
```

```txt
索引：
	1、什么是索引？有什么用？
		索引相当于一本书的目录，通过目录可以快速地找到对应的资源。
		在数据库方面，查询一张表的时候，有两种查询方式：
			第一种：全表扫描
			第二种：根据索引检索（效率极高，几倍不止）

		索引为什么可以提高检索效率呢？
			最根本的原理是缩小了扫描的范围。

		索引虽然可以提高检索效率，但是不能随意的添加索引，因为索引也是数据库中
		的对象，也需要数据库不断地维护。是有维护成本的。比如：表中的数据经常被修改
		这样就不适合添加索引，因为数据一旦修改，索引需要重新排序，进行维护。

		添加索引是给某一个字段，或者说某些字段添加索引；

		select ename,sal from emp where ename = 'SMITH';
		当ename字段上没有添加索引的时候，以上sql语句会进行全表扫描，扫描ename字段中所有的值。
		当ename字段上添加有索引的时候，以上sql语句会根据索引扫描，快速定位。

	2、怎么创建索引对象？怎么删除索引对象？
		创建索引对象：
			create index 索引名 on 表名(字段名);
		删除索引对象：
			drop index 索引名 on 表名;或drop index 表名 索引名;

	3、主键primary key和具有unique约束的字段的字段会自动添加索引
		根据主键查询效率较高，尽量根据主键检索

	4、什么时候考虑给字段添加索引？（满足什么条件）
		* 数据量庞大。（根据客户的需求，根据线上的环境配置）
		* 字段很少DML操作。（因为字段会进行修改操作，所以索引也需要维护）
		* 该字段经常出现在where子句中。（经常根据哪个字段查询）

	5、查看sql语句的执行计划explain：
	mysql> explain select ename,sal from emp where sal = 5000;
	+----+-------------+-------+------+---------------+------+---------+------+------+-------------+
	| id | select_type | table | type | possible_keys | key  | key_len | ref  | rows | Extra       |
	+----+-------------+-------+------+---------------+------+---------+------+------+-------------+
	|  1 | SIMPLE      | emp   | ALL  | NULL          | NULL | NULL    | NULL |   14 | Using where |
	+----+-------------+-------+------+---------------+------+---------+------+------+-------------+
							全表扫描type all


	给薪资sal字段添加索引：
		create index emp_sal_index on emp(sal);
	mysql> explain select ename,sal from emp where sal = 5000;
	+----+-------------+-------+------+---------------+---------------+---------+-------+------+-------------+
	| id | select_type | table | type | possible_keys | key           | key_len | ref   | rows | Extra       |
	+----+-------------+-------+------+---------------+---------------+---------+-------+------+-------------+
	|  1 | SIMPLE      | emp   | ref  | emp_sal_index | emp_sal_index | 9       | const |    1 | Using where |
	+----+-------------+-------+------+---------------+---------------+---------+-------+------+-------------+
						

	6、索引底层采用的数据结构是：B+树

	7、索引的实现原理？
		通过B Tree缩小扫描范围，底层索引进行了排序，分区，索引会携带数据在表中的“物理地址”，
		最终通过索引检索到数据之后，获取到关联的物理地址，通过物理地址定位表中的数据，效率是最高的
			select ename from emp where ename = 'SMITH';
			通过索引转换为：
			select ename from emp where 物理地址 = '0x3';//Oracle中物理地址叫：rowid

	8、索引的分类？
		单一索引：给单个字段添加索引
		复合索引：给多个字段联合起来添加1个索引
		主键索引：主键上会自动添加索引
		唯一索引：有unique约束的字段上会自动添加索引
		...

	9、索引什么时候失效？
		select ename from emp where ename like '%A%';
		模糊查询的时候，第一个通配符使用的是%，这个时候索引是失效的

=======================================================================================================================
视图：（view）
	1、什么是视图？
		站在不同的角度去看到数据。（同一张表的数据，通过不同的角度去看待）
		其实视图就是一系列的select语句

	2、怎么创建视图？怎么删除视图？
			create view 视图名 as select empno,ename from emp;
			drop view 视图名;
		注意：只能通过select语句创建视图

	3、对视图进行增删改查（CRUD），会影响到原表数据（通过视图来影响原表数据的，不是直接操作的原表）

	4、视图的作用？
		视图可以隐藏表的实现细节。保密级别较高的系统，数据库对外只提供相关的视图，java程序员只能对视图对象进行CRUD
		create view 视图名 as select empno as 'id',ename as '名字' from emp;
```

```txt
DBA命令：
	1、将数据库当中的数据导出
		导出指定库：
			在windows的dos命令窗口中执行:
			mysqldump bjpowernode>D:\bjpowernode.sql -uroot -p密码

		导出指定表：	
			在windows的dos命令窗口中执行：
			mysqldump bjpowernode emp>D:\bjpowernode_emp.sql -uroot -p密码

	2、导入数据
		create database bjpowernode;
		use bjpowernode;
		source D:\bjpowernode.sql

======================================================================================
数据库设计三范式：（重点，面试经常问）

	1、什么是设计三范式
		设计表的一句。按照这个三范式设计的表不会出现数据冗余

	2、三范式都是哪些？
		第一范式：任何一张表都应该有“主键”，并且每一个字段“原子性不可再分”

		第二范式：在第一范式的基础上，所有非主键字段“完全依赖”主键，不能产生部分依赖
（口诀）		多对多？三张表，关系表两个外键
			t_student					t_teacher					t_relatation
			------------------------	-------------------------	------------------------
			sno(pk)		sname			tno(pk)		tname			rno(pk)	sno(fk)	tno(fk)
			========================	=========================	========================
			1			张三				1			李老师			1			2		1
			2			李四				2			王老师			2			2		2

		第三范式：在第二范式的基础上，所有非主键字段，“直接依赖”主键，不能产生传递依赖。
（口诀）		一对多？两张表，多的表加外键

	（注意：在实际的开发中，以满足客户的需求为主，有的时候会拿冗余换执行速度）

一对一怎么设计？
	有两种方案：
		1、主键共享
			t_user_login	用户登录表
			id(pk)		username		password
			------------------------------------------------
			1			zs				123
			2			ls				456

			t_user_detail	用户详细信息表
			id(pk+fk)	realname		tel
			--------------------------------------------------
			1			张三				1111111111
			2			李四				2222222222

		2、外键唯一
			t_user_login	用户登录表
			id(pk)		username		password
			------------------------------------------------
			1			zs				123
			2			ls				456

			t_user_detail	用户详细信息表
			id(pk)		realname	tel			userid(fk+unique)
			-------------------------------------------------------------------------
			1			张三		11111111		1
			2			李四		22222222		2
				（之前说过，添加外键的字段至少要有唯一性）

====================================================================================================================
行级锁：（为了保证查询到的数据的真实性），又被称为“悲观锁”
	select ename,job,sal from emp where job='manager' for update;
	+-------+---------+------+
	| ename | job     | sal  |
	+-------+---------+------+
	| JONES | MANAGER | 2975 |
	| BLAKE | MANAGER | 2850 |
	| CLARK | MANAGER | 2450 |
	+-------+---------+------+
	3 rows in set
	在select语句后面加上for update，表示：将查询到的3条记录锁起来，当前事务结束之前，这三条记录谁都不能动。其他事务修改这些记录会卡住

悲观锁：事务必须排队执行，数据锁住了，不允许并发
乐观锁：支持并发，事务不需要排队，只不过需要一个版本号

比如说两个事务，版本号1.1：
事务1先修改了，然后看了版本号是1.1，于是提交数据，将版本号改为1.2
事务2后修改，修改完毕查看版本号为1.2，与它读到的版本1.1不同，修改失败。事务回滚

=========================================================================================================
在实际的开发中，数据库表的设计会使用专业的建模工具，我们这里用PowerDesigner，使用PD工具来进行数据库表结构的设计。当然Navicat也会用，但是两个工具负责的
模块不同，各有各的用处。这个工具是SyBase公司的产品，该公司是做SyBase数据库起家的，后来被德国的SAP公司收购了，SAP是Enterprise Resource Planning
（企业资源计划）的缩写，它是一家物资资源管理（物流）、人力资源管理（人流）、财务资源管理（资金流）、信息资源管理（信息流）集成一体化的企业管理软件公司。
	· 安装完成之后，打开create model创建模型-->模型类型选择:物理数据模型-->选择对应的数据库，输入模型名称，一般为项目名
	· 然后在右边的Toolbox中的Physical Diagram中选择Table，将表拖到主屏幕即可
	· 双击该表即可对它进行设计，General栏中，Name是表名，Code才是真正的数据库表名
	· 选中Columns栏对字段进行设计，Name是列名，Code才是真正的数据库字段名称；DataType是字段类型，length是该类型的长度，mandatory是强制的意思，表示该字段非空not null；
	· 这样sql语句就有了，可以点保存，导出该sql脚本；
	· 做好数据库的设计之后点保存即可保存成一个pdm文件，下次可以直接双击该pdm文件打开pd工具，或者在pd中file-open找到该文件，打开原来的工作区
	· 在工作区中右键find，可以快速定位图中某张表
（后面还会用到该工具，其他的用到了再说）
```

