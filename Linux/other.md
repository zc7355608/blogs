```tex
linux：
----------------------------------------------------------------------------------------------------------------------------------
关于linux的目录：
	home就相当于windows系统c盘“用户”文件夹，开始菜单在上面
		系统工具-设置-displays可以调整分辨率
	computer就代表“我的电脑”
	bin、lib64、sbin：都是一个快捷方式，它都对应某一个路径，bin、sbin下放的都是linux的命令以及可执行文件
	dev目录：设备文件
	etc目录：安装软件的配置文件（etcetera=诸如此类）
	media目录：媒体目录
	mnt目录：挂载移动设备，如：u盘、硬盘等移动设备（mount=挂载）
	opt目录：放置大型软件的目录
	proc目录：程序目录
	root目录：root用户的目录，如果登陆的不是root用户，那么是没有权限访问该目录的
	srv、sys目录：系统目录，暂时了解
	temp目录：放置临时资源的
	usr目录：它下面有一个常用的目录：local目录：用来放置开发人员开发的软件的，相当于windows的program files
	var目录：放置日志文件，或者持续增长的文件
---------------------
	linux系统也是类unix的一种，linux系统开发中一般作为服务器使用，而服务器一般在机房中，所以要想在服务器部署项目，只能通过远程访问的方式部署
这里介绍几种远程工具：XShell、SecureCRT、putty
其中XShell有可以免费用的的个人版，SecureCRT是收费的，putty是完全免费的开源的，但是功能偏弱
这些远程工具是用来执行linux远程命令来完成远程控制的
	还有一些用于文件上传和下载的工具，是基于ftp协议的：Xftp、WinSCP
----------------------
这里使用XShell(局域网)：远程链接服务器工具
	1、新建一个会话，名字随意，一般我们使用:服务器功能名_ip
		那怎么查看linux的ip呢？打开终端，输入ifconfig，找到对应网卡的inet后面的数字就是ipv4地址
		协议就用ssh、端口号都不用动，主机就用ip地址
	2、用户身份验证：该选项是为了登录linux系统保存用户名和密码，以后每次远程链接就不需要重新输入用户名和密码了
		但是这样就不太安全了，如果不希望保存密码，每次登录都输入密码的话，就别填这项就行
	3、终端：模拟用户来访问服务器，一般我们模拟linux来访问linux服务器，所以用linux
	4、外观是设置本地shell窗口的颜色图案之类的样式的，在这个shell中执行命令和在linux系统上打开命令窗口执行命令是一样的
----------------------------
使用Xftp(局域网)：链接服务器进行上传和下载文件工具
	使用方式和上方类似
=================================================================================================================================
关于linux命令：
	*首先，linux是没有c盘d盘这个概念的，它所有的资源都是以目录或文件的形式存在的，/就代表本地的计算机也就是Computer，它就代表所有目录的根目录
	*其次，在linux中，图标上带有箭头的表示快捷方式，它指向了一个路径。如：bin -> user/bin
	*root用户在命令行中是#，其他用户都是$
	*命令的参数没有顺序要求
--------------------
1、磁盘管理命令：
		pwd：查看当前所处位置的绝对路径（Print Working Directory）
		ll：查看当前目录下所有的内容，开头是“-”表示是文件，“d”表示是目录
		ls：也是查看目录下内容，但是只列出它们的名字，没有详细信息
		（ll和ls后面也可以跟路径，表示查看某个路径的内容）
		cd：切换目录，后面可以跟相对路径可以跟绝对路径。“cd /”表示切换到根目录，类似于windows的我的电脑
			例子：cd /user/local/  是绝对路径的方式		|	cd a/b/c/  是相对路径的方式
		clear：清屏
--------------------
2、文件管理命令（以下路径都可以用绝对和相对）：
		mkdir 目录名：创建目录。表示在当前目录下创建一个目录（已多重目录的形式创建加-p参数）

		rm 文件路径：删除文件，回车后会提示是否删除，y删除，n取消
		rm -f 文件路径：强制删除文件，无需确认
		rm -rf 文件路径：递归的，强制的，删除一个目录或文件，它会将目录以及目录下所有的资源全删掉最后再删除掉该目录

		cp 被复制的文件 新文件：复制文件（只复制文件，非目录）
		cp -rf 被复制的目录 新目录：递归复制一个目录下所有的资源

		cat 文件路径：将该文件中所有的内容都显示在屏幕上，无论文件多大。该命令查看大文件会相当麻烦
		more 文件路径：查看文件内容，方便查看大文件。空格键：显示一页内容；回车键：显示一行内容
		head 文件路径：查看文件头10行，如果要查看更多行，加参数-n：head -n 12 文件，表示查看文件头12行内容
		tail 文件路径：查看文件末尾10行，如果要查看更多行，加参数-n：tail -n 12 文件，表示查看文件末尾12行内容

		grep 搜索的内容 源文件1 文件2...：将文件中匹配到的内容以行的方式打印在控制台，支持正则表达式（用双引号），区分大小写
			参数i表示忽略大小写：grep -i xxxoo /home/newfile.txt
			参数w表示以独立的单词来搜索，不能是其他单词里面带有该内容：grep -w xxx /home/newfile.txt
			除了这些，grep还可以和“管道”操作一块用，这样就不需要写后面的源文件了，后面会详细介绍管道
---------------------
3、系统命令（和路径无关，在哪个路径下执行都行）：
		date：显示当前时间
		su 用户名：切换当前用户（如果切换到权限高的用户需要输入密码，输入密码时光标是不动的）
		clear：清屏
		reboot：重启
		shutdown -h now：关机。h是参数，设置关机时间，now是立即关机
		ps -ef：查看系统中当前正在运行的进程。e和f都是参数，e表示显示所有的进程，f表示列出所有的详情列，类似于windows的任务管理器
						uid：该进程是哪个用户启动的
						pid：进程号
						ppid：该进程的父进程号
						c：占用cpu资源的百分比
						stime：启动时间
						tty：终端。由哪一个客户端启动这个程序的
						time：这个程序运行了多长时间了
						cmd：具体的程序名称
						一般如果进程比较多的话会配合管道符“|”进行过滤
		kill uid：相当于任务管理器中的结束进程（如果进程还没有结束，可以跟一个参数-9，表示强制结束一个进程）
------------------------
4、压缩和解压命令tar：
	压缩：
	tar -zcvf 压缩后的新文件 源文件：z表示压缩，压缩后的文件后缀名为xxx.tar.gz；c创建压缩文档；v显示被压缩文件的文件名；f被压缩文件的文件名
			其中参数：v可以省略；源文件可以有多个，中间用空格分开

	解压：
	tar -zxvf 当前目录的压缩文件：默认解压到当前目录下。其中参数x表示解压缩

	解压到指定目录：
	tar -zxvf 压缩文件 -C 指定位置：将压缩文件解压到指定位置
			其中参数：C表示解压到指定目录，大写C

	查看压缩文件内容：
	tar -tf 压缩文件：查看压缩文件的内容。
			其中参数：t表示查看压缩文件的内容
【常用命令】
	压缩：
	tar -zcvf test.tar.gz *.txt (使用gzip进行压缩)
	tar -jcvf test.tar.bz2 *.txt (使用bzip2进行压缩)

	解压：
	tar -zxvf test.tar.gz (使用gzip进行解压缩)
	tar -jxvf test.tar.bz2 (使用bzip2进行解压缩)

---------------------------------
5、网络通讯：
	ifconfig：查看ip配置信息(inet后面的数字就是ipv4地址,inet6后面的数字就是ipv6地址)
	ping：测试网连通性的
	curl url地址：模拟用户访问
	wget url地址：下载资源的（如果下载失败就给该命令加参数：wget --no-check-certificate url地址，即可）默认下载到当前位置
--------------------------------
6、权限管理：
	linux的文件权限机制采用UGO模式，其中U表示所属用户user，G表示所属组group，O表示其他用户other
	创建文件的就是文件的所有者user，user的所在组就是文件的所属组group，除了创建文件的用户其他用户都是other。root有最高权限
			- RWX R-X R-X
		*linux中：
			l是链接
			d是目录
			c是字符设备文件
			b是块设备
			-是文件
		*后面的前三位是user权限，4-6位是group权限，7-9位是other权限
			R表示：读----4
			W表示：写----2
			X表示：执行--1
			rwx=4+2+1=7
			常见权限有：644、755、777

	#修改文件权限：
		chmod 权限 文件或目录：chmod 644 /user/a.txt
	#修改文件所有者：
		chown 用户名 文件：chown chi /user/a.txt
--------------------------------
7、管道和重定向：
	echo命令是 Linux 中最基本和最常用的命令之一。传递给 echo 的参数被打印到标准输出中；若要打印双引号，请将其包含在单引号内，或用反斜杠字符进行转义。
	#重定向输出覆盖（>）：
		echo "新内容" > 文件a：将文件a的内容替换为“新内容”，文件a不存在会新建（echo是输出内容到控制台）
	#重定向输出追加（>>）：
		echo "追加内容" >> 文件a：将文件a的内容尾部换行追加一个“追加内容”，文件a不存在会新建
	#管道（|）：
		管道的作用就是链接两个命令，将前面命令的输出内容作为后面命令的输入。用于把左边的输出作为右边的输入（可以有多个）
		例如：
				1、echo "helloworld ppp" | grep "hello"
				2、echo "hello linux" | wc
				wc是统计前面这个字符串的内容里，有几行，几个单词，几个字符的（控制字符也算\n,\0,\t）
				输出：1 2 12		表示1行，2单词，12个字符（尾部还有一个'\0'字符串结束标志）
----------------------------------
8、vi和vim编辑器
	*vi相当于windows的记事本
	*vim相当于记事本的升级版
	*它俩的主要区别是：vim打开的东西，里面的一些关键字是可以语法高亮的，一般用来编辑配置文件

	#语法：vi 文件名
	如果该文件存在，那么就打开该文件，如果不存在，保存的时候就会将文件创建出来
	#vi常用操作：
		*vi中的操作分为“命令模式”和“编辑模式”2种
		*命令模式是进行文件的复制粘贴和保存的，编辑模式是进行文件内容的操作的
		当vi打开一个文件时，默认进入的时命令模式，命令模式下无法编写文件内容的

		命令模式：Esc键
		编辑模式：按a或i键，此时底部会出现insert
		命令模式下可以输入:wq然后回车保存退出；输入:q!不保存退出(如果文件不存在，可以【:wq 文件名】这样的方式创建一个文件)

		命令模式中可以对文件进行如下操作：
			dd		删除光标所在行（连续摁2次d键）
			yy		复制光标所在行
			p		粘贴
			gg		光标回到第一行
			GG		光标回到最后一行
			^		光标回到当前行行首
			$		光标回到当前行行尾
			/		搜索字符，搜索到多个可以按n往后找（/关键字【回车】）
-------------------------------
9、安装软件命令（yum命令使用只能是root用户，并且必须联网使用）：
	yum命令：Yellow dog Updater,Modified，是一个在RedHat、Fedora以及CentOS中的一种软件包管理器，能够从指定的服务器自动下载和安装软件包，
	可以自动处理软件包之间的依赖关系，并且一次性安装完所有的依赖包，无需反复地安装下载..（它会在这3家的服务器上自动下载所需软件的依赖）

	yum的使用：
		*搜索软件包：yum search 安装包的部分关键字
			它会联网搜索可以下载的镜像包，然后安装的时候会自动从国内的代理服务器上联网下载和安装依赖与环境。搜索到带有这些关键字的镜像包，
		然后就可以使用另一个子命令来联网安装了，不需要我们很复杂的自己手动安装软件
		*安装软件包：yum install 软件包名字
		*卸载软件包：yum remove 软件包名字
		*列出来你的系统中所有的已经安装的软件包：yum list installed
				由于这个信息非常之多，所以我们一般都会筛选：yum list installed | grep java，表示安装的带java关键字的软件包
				我们发现系统上自带了一个openJDK，它是开源的，不是oracle的jdk17之类的不一样，我们也可以用java -version查看openjdk的版本
		*清除安装包：yum clean all（yum命令下载的包都在/var/cache/yum目录下）安装后的软件，安装包就没用了，该命令可以删除安装包
=========================
关于环境配置：
	1、linux安装jdk8：
		-安装前先查看本机上是否有openJDK，有的话删除掉：
			查看本机的jdk相关软件：rpm -qa | grep java（应该是4个openJDK的包，删除这4个即可）
			用该命令可以卸载：rpm -e --nodeps 包名（.noarch不用管，删不删都行）
			检查是否删除完，输入：rpm -qa | grep java
		-下载jdk8的压缩包，然后解压到/usr/local/目录下就安装上了
		-然后配置jdk环境变量：
			JAVA_HOME=/usr/lib/jvm/jdk1.8.0_361
			PATH=$JAVA_HOME/bin:$PATH
			CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
			export JAVA_HOME PATH CLASSPATH
			在/etc/profile文件末尾追加上配置即可
		-最后让profile生效，编译profile：source profile
		-检查version：java -version

	2、linux安装tomcat（tomcat10.1就不支持jdk8了，10.0还可以用jdk8）：
		直接解压到/usr/local/目录下即可（tomcat的bin目录下的.sh结尾的文件就是shell文件，即windows中的bat文件）
		打开服务器：当前bin目录下，./startup.sh（./代表当前目录，指执行当前目录下的shell文件）
		查看进程是否存在tomcat：ps -ef | grep tomcat

		查看防火墙状态：systemctl status firewalld
		启用防火墙：systemctl start firewalld（永久有效）
		禁用防火墙：systemctl stop firewalld

		让防火墙可用：systemctl enable firewalld（这俩是临时的，只在本次登录linux有效，重启就又打开防火墙了）
		让防火墙不可用：systemctl disable firewalld

	3、linux安装mysql数据库：
		-首先检查是否装有mariadb数据库，mariadb是mysql的分支，是免费开源的。这两个数据库会有冲突，安装mysql前要检查
			检查：yum list installed | grep mariadb
		-卸载mariadb：yum -y remove 软件包名，-y参数表示一路确认的，不再询问的卸载掉
		-安装mysql：将压缩包直接解压到/usr/local/下即可
		-改名字：mv 原名字 新名字（目录原名太长了，不方便，给它改短即可）
		-创建数据文件夹data：data文件夹是mysql用来存放数据库文件的，数据库的表都放在data目录中，默认没有data，需要手动创建
			在mysql的目录中mkdir data创建一个data目录即可
		-添加mysql用户，用来执行mysql的一些命令：useradd mysql（任何位置都可以执行）
			查看是否执行成功：id mysql（id命令用来查看用户信息的）
		-初始化系统数据库：定位到bin目录下，执行：
			./mysqld --initialize --user=mysql --datadir=/usr/local/mysql-5.5.1/data --basedir=/usr/local/mysql-5.5.1
					--initialize参数表示要执行mysql的初始化
					--user=mysql用这个用户身份完成初始化
					--datadir创建好的系统数据库放在哪里
					--basedir表示mysql的位置在哪里
			初始化完成有一条语句很重要：
				[Note] A temporary password is generated for root@localhost: #s5a/tgEatE5
				表示给localhost的root用户生成一个临时的密码：#s5a/tgEatE5
		-启用数据传输加密功能：./mysql_ssl_rsa_setup --datadir=/usr/local/mysql-5.5.1/data
			完成之后data文件夹中就生成了很多pem文件
		-给mysql文件夹设置所属权限信息：chown -R mysql:mysql /usr/local/mysql-5.5.1/
			表示将该目录及其子目录，用递归的方式设置为mysql账号和mysql组，在linux中，每创建一个账号就有对应的同名的组
		-安全启动mysql服务：./mysqld_safe &（&表示后台启动）
			检测下：ps -ef | grep mysql
		-启动服务之后就可以在bin目录下链接mysql了：./mysql -u root -p密码
			第一次链接进去还无法操作，会提示你改密码，因为此时的密码是临时的：alter user '用户名'@'ip地址' identified by '新密码'
				例如：alter user 'root'@'localhost' identified by 'a123456789'
		-授权允许客户端远程访问：（mysql的命令）
			grant all privileges on *.* to root@'%' identified by 'a123456789';
			表示允许所有的ip使用root来远程访问所有数据库的所有表
			第一个*表示数据库名，第二个*表示所有表名
			@后面''里面的百分号%表示所有ip
			更新权限信息：flush privileges;
		-关闭mysql服务：./mysqladmin -uroot -p shutdown，回车输入密码关闭即可


```

