```txt
字符编码：
================================================================================================
	1、ASCII编码：美国信息交换标准编码，每个字母或符号占1B，并且8bits的最高位是0，
		因此ASCII能编码的字母和符号只有128个；
	2、Latin1编码（又名ISO-8859-1编码）：Latin1是Mysql数据库表的默认编码方式。
		Latin1也是1B编码方式，向上兼容ASCII编码；共支持256种字符，用于表示欧洲语言；
	3、全球通用编码：
（java）# Unicode字符集：该集合中包含全球所有国家的字符和文字，“码点”用16进制表示，它兼容了Latin1字符集；
		它有以下3种编码实现方式：
			# UTF-8编码：它是一种可变长度的字符编码，根据不同的字符采用不同的字节数来表示，
				内部编码方案使其可以区分每个字符占了几个字节；它使用1~4个字节来编码一个字符，
				是目前Web开发中最常用的，英文占1B，汉字占3B；推荐编程人员的源码都用这个；
			# UTF-16编码：同样是基于Unicode编码的可变长度的字符编码，也是占用2/4个字节；
				不同的是英文占2B，汉字大部分占2B，小部分4B；
			# UTF-32编码：固定长度编码方案，所有的unicode字符都用4个字节编码；
	4、简体中文编码：
		# GB2312（小）：中国国家标准的简体中文字符集，是GBK的前身；英文占1B，汉字占2B；
		# GBK（中）：针对中文设计的一个编码方式，能够表示中国内地所有的汉字；英文占1B，
			汉字占2B，目前中文系统用的最多；支持繁体中文；
		# GB18030（大）：全称《信息技术 中文编码字符集》，共收录七万多个汉字和字符，
			它在GBK的基础上增加了中日韩语中的汉字和少数名族的文字，完全兼容GB2312，
			基本兼容GBK，用于取代以上两种编码；
	5、专门的繁体中文编码Big5：台湾地区繁体中文标准字符集，采用双字节编码，英文占1B，汉字占2B；

（
Java中String类提供了2方法对字符文本进行编码：byte[] getBytes(); 和byte[] getBytes(String charsetName);
	前者采用jvm平台默认字符集，utf8进行编码；后者可以自己指定方案；（不知道平台编码方式就Charset.defaultCharset()来获取）
String同样给了2个解码的方法：String(byte[] bytes); 和 String(byte[] bytes, String charsetName);
	可将二进制数据转换为字符串；
）
====================================================================================================

- 0B/0b开头表示二进制的字面量（JDK8），这是c语言（C99）中没有的；

- float4个字节也比long8个字节能表示的数字大；因为float在计算机内存中是按照IEEE754标准来存的，
	其中32位float包含3部分(符号位，指数位，尾数位)，float是指数级的增大；

- 由于计算机存一个浮点数时，内部都是存了一个该数的近似值，所以在实际开发中，
	不建议拿着一个浮点数与另一个数做==比较，这是不准确的，很有可能不会相等；
	（如果非要比较，可以判断两数之间的差，差值小于0.00001时就算两数相等）

- Java中&&会短路，&不会短路，所以一般都用&&，效率较高；&是按位与也是逻辑与，这点和c不同；
	当两边是布尔类型，则&是逻辑运算符，当两边是整数，则是位运算符；

- Java中switch语句中的类型可以是（jdk7）：int/String/enum枚举；

- Java的goto和const是保留字，没用处，如果想让break终止指定的循环的话，可以给循环之前加上一个标号，
	然后break：标号（continue同样可以）；

- 垃圾回收机制：在java中，当一个对象没有任何引用指向它的时候，此时该对象就会成为一个垃圾对象，垃圾回收器gc就会在
	合适的时机将它回收释放掉内存；

- 以.properties结尾的文件是“属性配置文件”，该文件内容格式是“键=值”的格式，用“#”作为注释；
----------------------------------------------------------------------------------------------------

Java中一个类/接口/抽象类/枚举/注解的访问权限只有两种，public和默认（不写）；而方法、属性和内部类的访问权限有4种：

			private  <    默认    <  protected  <  public
			同一个类	同一个包		子类      	所有类
（如果类的访问权限是public，那么文件名必须和类名保持一致）

----------------------------------------------------------------------------------------------------
- Java中类路径是运行java命令的路径，就是classpath环境变量的路径，除了java源代码里的系统类之外，第三方类要
	装入jvm，如果不在当前运行目录下（类路径）的话，就得配置环境变量classpath，使得ClassLoader能找到该类并装在jvm中；

- IDEA的默认路径是工程project的根下，也就是模块的同级目录

- Java分为编译和运行两个过程；javac会将源代码编译生成.class结尾的字节码文件，然后运行时jvm会将
	该字节码文件解释成二进制执行；

- Java采用Unicode字符集，char c = '\u0000';表示c赋值一个空字符''，\u表示是Unicode编码，后面跟上
	该字符对应的2个字节的16进制的码点；不能 c='';

- Java中的可变长参数：类型... 参数名：可以传进去0~n个参数，并且可变长度参数在列表中只能出现在最后一个位置且出现一次，事实上它就是数组；

- Java中，有一个命令可以用来查看字节码文件（class文件）：javap -c XXX.class：用来查看字节码文件；字节码文件中有很多“字节码指令”，我们来看下分别是什么意思：
	# 在java程序执行过程中，会给每一个方法分配独立的空间；在该空间中，有两块空间很重要；
	一个是“操作数栈”，另一个是“局部变量表”；
		· bipush  23：将指令后面跟的字面量23压入操作数栈中
		· istore_1  ：将操作数栈顶的数据弹出，并且将该数据存储在局部变量表的第1个位置上；
			局部变量表0的位置通常存储方法的局部变量参数
		· iload_1   ：将局部变量表中1位置上的数字复制一份，压入操作数栈
		· iinc   1,1：将局部变量表1位置上的数据+1
		· return    ：该指令一致性，方法结束

- Java中的方法重载只和"方法名"以及"参数列表"有关；判断方法是否相同就看这俩；

- java -X：该命令可以设置和查看JVM中的堆栈内存的大小

- Java中定义数组由于用到了new关键字，所以Java中的数组是在堆中的，并且有默认值；
	数组是引用类型，有length属性；数组的初始化有两种方式：
		# int[] a = new int[4];
		# int[] a = new int[]{1,2,3};//为了简便通常写为：int[] a = {1,2,3};
	定义二维数组：int[][] a = new int[3][4];也可以这样：new int[3][]，3个列数不确定的1维数组，这点和c正好相反；
	也可以显示定义：int[][] a = { {123},{1} };

- Java的编译器(javac)默认采用utf8的方式加载源文件(.java)进行编译的；如果要更改：
	javac -encoding GBK 文件名.java 加参数就行了；

- System.out.println();里面的引用类型会自动调用其toString方法，里面的基本类型会自动调用
	String.valueOf()方法转换成字符串，所以该方法输出的东西本质上都是字符串；

- 8个基本类型都有对应的包装类；其中6个数字型包装类的父类是Number抽象类，它们有自动装箱和自动拆箱；

- 	Integer.parseInt("100")可以将字符串转换成int，相应的Double也有对应的方法；
	Integer.valueOf(int/String)可以完成手动装箱，相应的手动拆箱用intValue()；
	Integer.toHexString(321)可以将321数字转换成对应的16进制字符串；
	Integer.MAX_VALUE可以获取int的取值范围，对应的Double类也有类似的方法；

- Java中为了提高程序的执行效率，将[-128~127]之间所有的包装对象都提前创建好了，放在了方法区的“整数型常量池”（其实就是Integer[]）中，用到了直接来拿就行；

- 超过long或精度超过double就得用BigInteger和BigDecimal引用型，父类也是Number；做计算都得用里面的方法，不能用算数运算符了；

- 	System.currentTimeMillis()：获取系统当前的时间戳
	System.gc()：建议启动Java垃圾回收器
	System.exit(0)：正常退出JVM
	System.arraycopy()：数组拷贝
	System.out获取系统的标准输出流PrintStream
	System.setOut(PrintStream)：设置标准输出流的方向
	System.getProperties()：返回一个properties集合，用于获取当前系统的全部属性

- Object类的方法：
	toString()：将对象转成字符串；默认实现是：类名@对象哈希值的16进制字符串；通常都需要重写；
	equals()：判断两个对象是否相等；默认实现是判断内存地址；通常都需要重写；
	hashCode()：返回一个对象内存地址的哈希值，底层c++程序实现了；
	clone()：调用底层c++程序完成对象的浅克隆，注意该方法是protected修饰的，所以只能在子类中重写
			并改为public的，并且只有实现了Cloneable接口(标志性接口)的对象才能被克隆；
		（Java中接口分为两大类，普通接口和标志性接口，标志性接口中没有代码， 是给JVM看的）
		（深克隆是指：被克隆对象中还有对象，克隆该对象时，需要将里面的对象也克隆一份，否则修改新对象会影响原来的里面的对象）
	finalize()：一个对象被垃圾回收器销毁前会执行此方法，可以重写此方法并编写需要被执行的代码（Java 9就过时了）

- Runtime类用于封装JVM进程，通过该类可以获取JVM的运行时状态，每一个JVM都有一个该类的实例，
	可以通过Runtime.getRuntime()来获取；Runtime类有几个方法：
		· Process exec(String command)该方法用于执行一个DOS命令，其执行效果和直接执行
			DOS命令的效果相同，它的返回值是一个Process进程对象，通过该对象可以控制操作系统
			的进程，如果要关闭进程，调用process.destory()方法即可；
		· long freeMemory()：返回JVM中的空闲内存量。单位：字节
	    · long maxMemory()：返回JVM的最大可用内存量。单位：字节
	    · int availableProcessors()：向JVM返回可用处理器的数目
	    · long totalMemory()：返回JVM中的内存总量。单位：字节

- new Random()/可以获取一个Random对象，然后调用nextInt(100)方法获取随机数[0-100)的随机整数，
	Random对象的随机数也可以传进去一个参数作为种子，这样每次运行产生的随机数都是相同的，默认是当前时间戳为种子；

- 在Java中，枚举中的值可以看作一个常量；超过boolean这2种情况，有3、4..多种情况时，
	就可以定义成一个枚举类型；枚举默认继承java.lang.Enum，而不是Object：
		[修饰符列表] enum 枚举名{RED,BLUE,GREEN}

- 和c一样，Java中的""括起来的字符串存放在堆中的，字符串常量池中，不可改；而String引用类型，底层是final的byte数组
	同样不可改；+/+=对变量在做字符串拼接时，底层会new出StringBuilder对象进行拼接，调用该对象的toString方法
	返回了String，所以这个新的String对象在堆中不是常量池中；如果是两个字符串常量做+拼接，那么该字符串常量在编译期
	就确定了，这是编译器的优化；如果需要将频繁使用的String放在字符串常量池中，调用它的intern()方法即可，这样效率较高；
	但是我们没有直接从常量池中删除的常量的入口，只能加；
	new String("str")创建出来的对象，本质上在堆中有一个String类的byte数组，而且在字符串常量池中还有一个"str"对象，
	所以该构造器不建议用；

- 当大量字符串拼接时，一定要用StringBuilder或StringBuffer类，它们是可变长字符串，内部的byte数组没带final修饰，是一个
	byte数组的容器；将字符串放到这个字符串缓冲区内有助于字符的拼接，在其中添加删除字符串时，操作的都是这个容器，
	并不会产生新的字符串对象；初始容量16不够会自动扩容，扩容2倍+2；最好实例化一个长度够用的字符缓冲区，减少拷贝次数；
	它们并没有重写equals方法，且不能用+进行字符串拼接，必须用append方法，但有compareTo方法可以用；

--------------------------------------------------------------------------------------

# 类体中加static关键字的称为“静态成员变量”，它是在类加载时初始化，并且存储在方法区，就一份；
	static不能修饰局部变量；静态方法只能访问“静态成员变量”，所以当一个方法中访问了实例变量，
	那么该方法一定是实例方法；

# 静态代码块也是在类加载时执行，并且只执行一次，多个静态代码块遵循自上而下的顺序依次执行；
	相对应的不加static关键字的是实例代码块，实例块在构造方法执行之前执行，其他的和静态块类似；

--------------------------------------------------------------------------------------

- this和super只能出现在实例方法中，super()和this()只可以出现在构造方法中的第一行，
	目的是在一个构造方法中去调用另一个构造方法或父类构造方法；不允许在一个类的两个
	构造方法中使用this()互相调用；

- 所有的构造方法第一行，如果没有this()和super()，那么会默认有一个隐身的super()，
	会调用父类的无参构造，先初始化子类中的父类特征，再初始化子类特征；
	但是要注意：执行父类的构造方法只是为了给 “子类继承过来的属性” 赋值而已，
	没有new这个关键字当然不会在堆中创建一个父类对象；

- 子类在对父类进行方法重写/覆盖的时候，方法名，返回值类型，参数，修饰符列表最好完全相同；
	返回值类型可以更小，子Cat-->父Animal，但一般不这样用；
	子类方法的访问权限不能比父类访问权限更严格，且子类方法不能抛出比父类更宽泛的异常；
	所以父类没有抛异常子类也不能抛异常（私有方法无法被覆盖，静态方法不谈覆盖）

- 继承会将除了构造器之外所有的东西复制过去，但是由于私有方法只能在本类中访问，
	故子类继承过去了但还是不可见的，故私有方法无法被覆盖；用super可以调父类中的方法；
	静态方法不谈覆盖；

--------------------------------------------------------------------------------------

- 

--------------------------------------------------------------------------------------
类到对象是实例化，对象到类是抽象（ is a(extends)		has a(属性)		like a(implements) ）

抽象类： - 抽象类无法new实例化，它生来就是用于被子类继承的，它的的构造方法是供子类使用的
		- 抽象类中不一定要有抽象方法，但抽象方法必须出现在抽象类中
		- 非抽象类继承抽象类，必须将抽象类中所有的抽象方法实现/覆盖/重写
		- 抽象类可以继承抽象类

抽象方法：- 抽象方法只能用public或者protected修饰，默认是public修饰

接口：	 - 接口是完全抽象的，抽象类是半抽象；接口是特殊的抽象类；接口与接口之间可以多继承；
		- 接口不允许继承抽象类，但是抽象类可以实现多个接口；
		- 接口中所有东西的访问权限都是public；无论是默认方法还是静态方法还是变量都是public的
		- jdk8之前接口中只能出现“全局常量”和“抽象方法”，jdk8之后还可以有默认方法（default）和静态方法（static）
		- 接口中，public static final、public abstract、public等修饰符都可以省略
		- 类可以实现多个接口，但必须implements实现所有的抽象方法
		- 接口中没有构造器
（以后开发中，接口使用的较多，接口是对“行为”的抽象，注意接口就没有继承老祖宗这么一说了；
在Java中，接口并不直接继承Object；在Java8之前，接口没有显式的父类，然而从Java8开始，接口
可以有默认方法和静态方法，此时接口会隐式地继承Object类中的方法，因为Object类是所有类的根类）

--java.lang.Exception--------------------------------------------------------------------
异常：在程序运行中出现的异常可能会导致程序强行终止，有了异常机制就可以避免；出现了异常，及时在程序中进行处理，程序接着往下执行；
	Java中的异常是以类的形式表示，运行时出现了异常，此时JVM会new一个异常类并抛出，对异常的处理将会改变程序的控制流程；

	# 异常的老祖宗是Throwable类，它有两个重要的分支类，Error和Exception，它们都是可抛出的；
		Error只要发生就终止程序的执行然后退出JVM，Error是不能处理的；
	# Throwable中有3个方法：String getMessage()	String toString()	void printStackTrace()；
	# Exception中有RuntimeException和其他异常，其他异常都是编译时异常，Runtime分支属于运行时异常；
		编译时异常比较严重，必须在编译阶段对这种可能发生的异常进行处理，运行时异常可以处理可以不处理；
	# 编译时异常（受检异常）发生的概率较高，需要在编译时对它进行预处理；运行时异常（未受检异常）
		发生的概率较低，一般编译时不处理；
	# 所有的异常都是在运行阶段发生的，因为只有程序运行阶段对象才在内存中存在，异常的发生就是new了对象并抛出；
	# throw关键字可以抛出一个异常对象实例，此时一般会选择对抛出的异常进行throws继续上抛处理；
	# 异常的处理方式：（2种）
		· throws抛出（如果上抛到了main方法，main继续上抛交给JVM，那么运行时这个异常到jvm这，程序就终止了）
		· try catch捕捉
	# 如果选择上抛异常，那么异常只要发生，该异常下面的代码就不执行了；try语句块中发生了异常，
		那么就停止执行块中下面的代码，转去执行对应的catch块中的代码；
	# catch块遵循从上到下，从小到大原则；
	# jdk7可以这样写：catch(ClassCastException | NullPointerException e){ e.toString(); }
	# 在Java中，finally块主要用于执行重要的清理工作，但是在关闭这些资源的时候，也有可能抛出异常；
		如果在finally中关闭资源时也发生了异常，而这个异常又没有在finally块中被捕获处理，
		那么这个异常会被抛出，覆盖try或catch块中抛出的任何异常，这是我们不愿见到的；
		所以，Java7 中引入了一种新的异常处理机制，称为"try-with-resources"。这种机制可以自动关闭
		在try()括号中打开的资源，如文件、数据库连接等，不需要手动加finally块进行关闭了；并且在传统的
		异常处理机制中，我们在finally中关闭的资源也可能发生异常，这些异常如果没有被捕获，那么finally
		块中的异常会掩盖外层的异常；而在引入的 "try-with-resources" 机制中，这个问题得到了解决。
	# 自定义异常，继承Exception或RuntimeException，然后重写两个构造方法就行；

---junit单元测试（白盒测试）--------------------------------------------------------------------------------
	#一个项目是巨大的，我们必须保证每个类，类中每个方法都是正确的健壮的，所以我们需要对每一个单元进行测试，目前单元测试用junit5；
		做单元测试需要用别人写好的类，需要引入junit5框架

	#单元测试类/测试用例怎么写？XxxTest，单元测试方法怎么写？
		用“@Test”注解标注，返回值类型必须是void，不能有参数；方法名建议testXxx；junit5的修饰符列表可以不写，junit4中必须是public的；

	#@BeforeAll和@AfterAll用于，在所有测试方法执行之前/之后，要执行一次的代码。被标注的方法需要是静态的；
	#@BeforeEach和@AfterEach用于，在每个测试方法执行之前/之后，都要执行一遍的代码。
（单元测试中如果Scanner用不了，就help--Edit Custom VM Options--找到IDEA64.exe.vmoptions文件末加一行参数，
-Deditable.java.test.console=true，最后重启即可）
```

```txt
Java中的日期类有3代：Date、Calendar、LocalDateTime
-----------------------------------------------------------------------------------------------------------------
Date()/Date(long time)：普通的日期/时间；要用直接new即可，但是格式很不友好，通常会格式化之后再输出，格式化的方式为：
	普通格式化：DateFormat
		getDateInstance()/getDateTimeInstance()来获取一个DateFormat日期格式化对象，
		根据参数FULL/LONG/MEDIUM/SHORT的不同，获取的格式化方式不同；然后用它的format()
		传进去一个Date来格式化成String，很方便；它的parse()可以根据字符串格式来解析成对应的Date；
	高级格式化：SimpleDateFormat
		根据指定格式字符串new一个该对象后，调用它的format()传进去一个Date，格式化成一个String；
		同样也有parse可以解析为Date；具体格式：
			yyyy年（年是4位）| MM月（月是两位）| dd日| HH小时| mm分| ss秒| SSS毫秒
--------------------------------------------------------------------------------------------------------------
Calendar：
	java中Date类能够实现的功能Calendar类（日历类）几乎都能够实现，以后的开发中也建议使用Calendar类；
	Calendar是一个抽象类，需要用静态方法来实例化一个日历类：Calendar.getInstance()；
	该类主要是根据它的int get(int field)里面参数的不同来获取不同的时间/日期的数字；它里有几个常用的全局常量：
			Calendar.YEAR
			Calendar.MONTH（从0开始的，使用时要+1）
			Calendar.DAY_OF_MONTH
			Calendar.Date
			Calendar.HOUR
			Calendar.MINUTE
			Calendar.SECOND
	如果要更改时间，可以用cal对象的add、set方法，setTime和getTime方法可以让Date和Calendar互转，
	还有一个可以获取所在时区的方法：getTimeZone()，返回TimeZone对象；
------------------------------------------------------------------------------------------------------------------
第3代日期类，以后用它了：
------------------------------------------------------------------------------------------------------------------
LocalDateTime、LocalDate、LocalTime：
		Instant是JKD8中新增的日期API，简要说下Java中日期APi的背景：
	早在JDK1.0时代，JDK就包含了一个与时间相关的java.util.Date类，因为其本身的缺陷，
	其大多数方法都在JDK1.1时代被新引入的与时间相关的Calendar类取代，而Calendar类
	并不比Date类好太多，其本身也有不少问题。譬如：
		1、Calendar类是可变的，即可以直接修改内部储存的时间值，而日期时间这类属性
			应更类似于字符串String，具有不可变性才较为合理。
		2、Date的起始年份都按1900年开始计算，而Date与Calendar的月份都以0开始计算，
			这与人的直觉和其他属性的计算方式有冲突，我们称其为偏移量。
		3、我们知道有个SimpleDateFormat类可以对日期进行格式化操作，而这个类只能直接
			操作Date类，对于后来加入并取代Date类大部分方法的Calendar类并不能直接操作。
		4、上述类都不是线程安全的
	故而有了第三代日期类：LocalTime、LocalDate、LocalDateTime

	#LocalDateTime.now()静态方法来获取第三代日期类对象，对应的LocalDate、LocalTime同样；
		LocalDateTime.of(参数)静态方法可以获取自定义的日期对象；它精确到纳秒，强于前两个日期类；
		它里面有plus和minus，可以对时间进行加减；通过get方法来获取日期中的各部分数字；

	#日期格式化：
		如果想自定义日期格式，3代日期类格式化用DateTimeFormatter工具类，它是线程安全的：
			DateTimeFormatter df = DateTimeFormatter.ofPattern("yyyy-MM-dd");
			String s = df.format(now);
		解析日期字符串：LocalDateTime time = LocalDateTime.parse("2019-01-12", df);

----------------------------------------------------------------------------------------------------------------
时间戳（Instant）
    # 时间戳Instant，代表的是某个时刻，构造方法私有化，内部由2部分组成：
    	第一部分是保存的时间戳，第二部分保存的是纳秒数；Date和它可以互转；
    	它比System.currentTimeMillis()更精确更通俗；所谓的Instant类代表的是某个时刻，精确到纳秒，而旧版的Date只精确到毫秒；

    # Instant.now()获取当前时刻时间；它的toEpochMilli()方法返回当前时刻的时间戳，和System.currentTimeMillis()获取的相同，
		都是基于全球标准时间，不是东八区；

-----------------------------------------------------------------------------------------------------------------
Duration间隔类和Period时期类，提供了简单的时间和日期的计算：
	Duration类：
        LocalTime start = LocalTime.now();
        LocalTime end = LocalTime.of(23,12,59);
		//静态方法between获取Duration间隔对象
        Duration duration = Duration.between(start, end);
        //to方法获取，总共差了多少时间
        System.out.println("时间间隔为："+duration.toNanos()+"纳秒");
        System.out.println("时间间隔为："+duration.toMillis()+"毫秒");
        System.out.println("时间间隔为："+duration.toHours()+"小时");
    Period类：
        LocalDate birthday = LocalDate.of(2000,10,24);
        LocalDate now = LocalDate.now();
        Period period = Period.between(birthday, now);
		//get方法获取，年、月、日分别差了多少
        System.out.println("时期间隔："+period.getYears()+"年");
        System.out.println("时期间隔："+period.getMonths()+"月");
        System.out.println("时期间隔："+period.getDays()+"天");
-------------------------------------------------------------------------------------------------------------------
TemporalAdjusters时间矫正器：
	比如通过LocalDateTime.now()方法获取当前日期对象now，想在此基础上将时间修改矫正，就用now.with(时间矫正器的静态方法)来对时间进行修改；
```

```txt
Java中的集合（java.util.*）
>>>>结构：======================================================================================================
	集合实际上就是一个容器，可以存放不同类型的对象；集合中存的是引用，且一般存的都是
	相同类型的数据；每一个不同的集合底层对应的是不同的数据结构，在集合中放的数据本质上
	就是放在了不同的数据结构中了；Java中集合包括两大类：Collection和Map，分别存放
	单个数据和键值对数据/数据条目entry；
	foreach(元素 : 集合/数组)是jdk1.5的新特性，用于遍历集合或者数组，底层是Iterator，
	只能访问不能修改，且遍历Map一次只能遍历key/value中的一个，否则会出问题；

- Collection接口：需要掌握两个重要的子接口，List和Set；List有下标有序可重复，Set无下标无序不可重复：
（一般很少直接用Collection接口，都是用其子接口：List/Set/SortedSet/Queue）
	# List有3个重要的实现类：
		· ArrayList：底层是数组，非线安
		· Vector：底层也是数组，线安（效率低）
		· LinkedList：底层是双链表，非线安

	# Set有2个重要的实现类：
		· HashSet：底层是HashMap，元素放在了它的key部分
		· TreeSet：底层是TreeMap；TreeSet实现了SortedSet接口（Set的直接子接口），所以TreeSet会自动排序

- Map接口：Map集合是按照键值对的方式存储元素，键值对都是引用类型，key无序不可重复；它有以下几个重要的实现类：
	· HashMap：底层是哈希表，非线安
	· HashTable：底层也是哈希表，线安（效率低），用的很少，但是它有一个子类用的很多：Properties
	· TreeMap：底层是二叉树，TreeMap实现了SortedMap接口（Map的直接子接口），所以TreeMap也会自动排序

>>>>方法：=====================================================================================================
Collection中的常用方法（该集合中的所有子类都有构造方法，可以传进去一个非Collection集合，转换成该集合）：
	boolean add(Object e)：向集合中添加元素e
	int size()：获取集合中元素的个数
	void clear()：清空集合
	boolean contains(Object e)：判断集合中是否包含元素e
	boolean remove(Object e)：删除集合中的元素e
	boolean isEmpty()：判断集合是否为空
	Object[] toArray()：将Collection集合转换为数组

	Iterator iterator()：获取Collection集合的迭代器，用来遍历集合
	（需要注意的是：集合里元素的结构发生改变，迭代器必须重新获取，否则使用会出现问题；
		如果需要通过迭代器来删除，用迭代器里的remove方法）

List接口中的常用方法：
	boolean add(int index, Object e)：通过下标向List集合中添加元素e
	Obejct get(int index)：通过下标获取集合中的元素
	int indexOf(Object e)
	Object remove(int index)
	set(int index, Object e)

Map接口中的常用方法（该集合中的所有子集合同样有构造方法，可以传进去一个非Map集合，转换成该集合，但Properties没有）：
	V put(K key, V value)：向集合中添加键值对
	V get(Object key)：通过key获取value
	V remove(Object key)：通过key删除键值对
	void clear()：清空map
	boolean isEmpty()：判断是否为空map
	int size()：获取键值对个数
	boolean containsKey(Object key)：判断是否包含某个key 
	boolean containsValue(Object value)：判断是否包含某个value

	Set<K> keySet()：获取map中所有的key，返回一个Set
	Collection<V> values()：获取所有的value，返回一个Collection
	Set<Map.Entry<K,V>> entrySet()：将Map集合转换成Set集合，返回一个Set，
		使用该方法获取键值对效率较高；
	（Map.Entry是一个支持泛型的静态内部接口，不同map集合内部对它都有具体的实现）

>>>>详情：=========================================================================================================
* ArrayList默认初始化容量是10（当第一个元素被添加的时候），1.5倍扩容，用的较多
* Vector默认容量同样是10，2倍扩容，线安效率较低用的很少
* LinkedList底层采用有下标的双链表，适合元素移动变化比较频繁，经常增删，很少查找的情况
------------------
* HashMap的默认容量16，达到75%时扩容，2倍扩容；建议给定容量是2的倍数：
	· HashMap的put(K,V)方法底层实现：
		1、先将kv封装成node节点对象
		2、调用k的hashCode()方法得出hash值，然后通过“哈希算法”将hash值转换成数组下标，下标位置空就添加，
		非空就拿着key和链表所有节点依次equals，true则将value覆盖，全false则将新node节点添加到链表尾

	·  HashMap的get(K)方法底层实现：
		调用k的hashCode()方法得出hash值，然后通过“哈希算法”用hash得出数组下标；如果该位置空，返回null，该位置有链表，
		则拿着key和链表所有节点依次equals，true则返回对应的value，全false则返回null；因此equals方法决定HashMap不重复，
		而且基于这样的结构，若想让hash表发挥最大性能，还需要hashCode算法得出的哈希值使元素散列分布均匀才行；
		所以放在HashSet/HashMap集合中的元素必须同时重写HashCode()和equals()
		（在jdk8之后，如果哈希表结构的单链表上的元素超过8，则会变成红黑树，继续提高查询效率；若低于6，变回单链表）

	· 关于hashCode和equals方法：
		它们协同配合工作来判断两个对象是否相等；对象调用hashCode生成哈希值，由于不可避免地会存在哈希值冲突的问题
		（不同的对象也可能有相同的哈希值），因此哈希值相同还需调用equals进行一次值的比较；若hashCode不同则直接跳过
		equals；如果两个对象的equals相等，则它们的hashCode值也必须相同，所以任何时候重写equals都要重写hashCode；
* HashTable：默认容量11，加载因子0.75，扩容2倍+1
* Properties：属性集，它的key和value只能是String，有线程安全的方法：
	load和store用来操作属性配置文件；getProperty、setProperty、Set<String> stringPropertyNames()、Enumeration<?> propertyNames()
	（Enumeration类似于迭代器）
* TreeMap：放在TreeMap中的元素会自动根据key来排序，输出按照中序遍历（左根右），如果是自定义的元素若想排序是有代价的：
	1、必须实现可排序的接口(Comparable)，重写compareTo()方法
	2、创建集合时传进去比较器(Comparator)，将比较器实现（实现比较器需要重写compare方法）即可

----------------------------
java.util.Collections是集合工具类，java.util.Arrays是数组工具类，里面有很多好用的方法，需要时查看文档使用即可，这里给几个常用的：

Arrays: 
	toString()：将数组转换成字符串
	deepToString()：将二维数组转换成字符串
	equals()：判断数组相等
	deepEquals()：判断二维数组相等
	sort()：排序；自定义类型必须实现comparable接口
	parallelSort()：数据量大的数组排序，基于分治的归并排序法，支持多核cpu
	binarySearch()：二分查找
	fill()：填充数组，将数组若干单元填充同一个值
	copyOfRange() / copyOfRange()：数组拷贝
	asList()：将一组数据转换成list集合。参数是可边长的；

Collections:

----------------------------

============================================================================================================
泛型：
	# 泛型的本质是参数化类型，将具体的类型未知化（参数化），在使用时再具体指定；
		根据位置不同有泛型类、泛型方法、泛型接口

	# Java中的“泛型机制只在编译阶段起作用”，运行阶段意义不大；使用了泛型的好处是，
		集合中存储元素的类型统一了；jdk8后泛型支持“泛型的自动推断”，也叫钻石表达式；

	# 如果你自定义的类型也想使用泛型，就在类型后跟上<E>，这个E没有其他意思，
		仅仅是表示一种未知的类型，当然你写其他任何特殊的符号也是可以的，但是为了规范，
		通常：写E/T，E是element，T是type，K/V，key和value，N是Number；

	# 类名<E,K,V···>可以有多个，泛型可以在类中的方法参数，返回值类型，属性类型上使用；

	# 如果不使用泛型时，类型可以是任意的，也就是Object；

	# 泛型接口和泛型类类似，需要注意的是，实现泛型类有两种方式：
		1、非泛型类实现泛型接口，需要明确指定泛型接口的类型，使用时也是一样要明确指定出来泛型接口的类型
		2、泛型类实现泛型接口，泛型类中的泛型名和泛型接口名保持一致即可；

	# 普通类中也可以出现泛型方法，定义时在修饰符列表和返回值类型中间加定义泛型标志（<E>），
		使用时最好在方法名前加泛型，如：
		public <T> String eat(T t){}
		调用：obj.<Integer>eat(666);

	# 使用或定义泛型时可以用类型通配符?，表示接受任何类型的泛型，如：
		Person<?> p = new Person<String>("df");也可以用Object来代替?

	# 类型通配符也可以设定上下限，如：doSome(Collection<? extends Number> c)表示该类型
		必须是Number或子类，doSome(Collection<? super Number> c)表示该类型必须是Number或父类
===================================================================================================================
```

```txt
IO流（java.io.*）
==================================================================================================================
	# Java中IO流有4大家族：Writer、Reader、InputStream、OutputStream都是抽象类；字符流和字节流的区别在于，
		字符流会将读取到的二进制信息通过指定的编码转换成可以显示的符号，而字节流不会；
	# 以上4个祖先流都实现了Closeable接口，故所有的流都是可关闭的，都有close()方法；流毕竟是一个管道，
		这个管道是内存和硬盘之间的通道，用完之后一定要关闭，不然会耗费很多资源；
		以前一般在finally中关闭流，但是现在更好的做法是，将流的创建放在try(这里的流会自动关)；
	# 以上4个祖先流中的写入流和输出流，都实现了Flushable接口，故所有输出流都是可刷新的，都有flush()方法，
		所以写完一定要记得刷新一下，避免通道有残留丢失数据；
	# 关闭流只需要关闭外层的包装流即可，内部传进去的节点流会自动关闭
	# java.io包下需要掌握的流有16个：
		文件专属：
			· FileInputStream：available()/read()/read(byte[])/skip(int)
			· FileOutputStream：write(int/byte[])
			· FileReader：read(char[])/read()
			· FileWriter：write(String)/write(char[])/write(int)
		转换流：（字节流可以转成字符流，构造器中可以传参数，指定使用的字符编码）
			· InputStreamReader
			· InputStreamWriter
		缓冲流：
			· BufferedInputStream
			· BufferedOutputStream
			· BufferedWriter
			· BufferedReader：readLine()，返回类型String，读取不到返回null
-------------------------------------------------------------------------------------------------------------------------
File读写和Buffered读写都是在内存先建立了文件缓冲区，和c一样，但是对于FileInputStream，它确实有一个内部缓冲区，
当你调用read()方法时，它会从缓冲区中读取数据，如果缓冲区中没有足够的数据，它会从磁盘文件中读取更多的数据填充到缓冲区中。

然而，BufferedInputStream 和 BufferedReader 的缓冲机制更加高级。它们的缓冲区通常更大，可以存储更多的数据。
此外，它们在读取数据时更加智能，可以在适当的时候自动刷新缓冲区，从而减少磁盘I/O 操作的次数。

总的来说，虽然 FileInputStream 和 BufferedInputStream 或 BufferedReader 都使用了缓冲机制，但后者在性能和功能上通常更强大。在处理大型文件或需要频繁读取文件的情况下，
通常建议使用 BufferedInputStream 或 BufferedReader 而不是 FileInputStream；
-------------------------------------------------------------------------------------------------------------------------
		数据流：（按次序指定类型读写，不是普通的文件，记事本打不开的；类似于c读写中的fscanf函数）
			· DataInputStream：readInt()/readShort()...
			· DataOutputStream：writeInt()/writeShort()...
		标准输出流：
			· PrintWriter
			· PrintStream
		对象流：
			· ObjectInputStream：readObject()
			· ObjectOutputStream：writeObject(obj/list)
（System.setOut()可以传进去一个PrintStream，来控制标准输出的方向，改回来可以：System.setOut(new PrintStream(new FileOutputStream(FileDescriptor.out)));）

（Serialize序列化，将java对象存储到磁盘；DeSerialize反序列化，将磁盘中的对象文件恢复到内存；要序列化的对象以及
其中的所有属性都必须是可序列化的，即实现Serializable接口；该接口是一个标识性接口，JVM看到后会自动为该类生成一个
全球唯一的序列化版本号；transient表示该属性是游离的，不参与序列化；如果之后这个类改了，然后重新编译运行反序列化程序，
JVM会给该类生成新的序列化版本号，之前序列化出来的文件就反序列化不出来了，因为序列化版本号对不上了；Java区分一个类先看类名，
再看序列化版本号；所以建议凡是实现了Serializable接口的类都固定写上一个“序列化版本号”；
private static final long serialVersionUID = 1L）
（还有一个序列化接口：Externalizable，该接口有两个抽象方法，并且由程序员自己决定要序列化的信息，性能较好，
但是使用复杂，通常不用这个接口来序列化）

序列化的注意事项：
	* 序列化对象时，默认会将所有属性序列化进去，除了transient修饰的；
	* 序列化对象时，要求里面的属性也需要实现序列化接口；
	* 序列化具有可继承性，如果一个类实现了序列化接口，那么它的子类都是可序列化的；
	* 注意：如果多次开启对象流，然后将对象写入文件再关闭，那么读这些对象时就会发生错误：
		StreamCorruptedException，多个对象必须在同一次的文件流中被序列化写入，才可以被正确读取；
---------------------------------------------------------------------------------------------
File类：
	File类是文件或目录名的抽象表示形式
常用方法：
	static File createTempFile(String prefix, String suffix)
		在默认的临时文件目录中创建一个空的临时文件，使用给定的前缀和后缀来生成它的名称
	static File createTempFile(String prefix, String suffix, File directory)
		在指定的目录directory中创建一个新的空临时文件，使用给定的前缀和后缀字符串来生成它的名称
	void deleteOnExit()
		虚拟机终止前删除该文件
==================================================================================================================
多线程：
	# 进程是一个app，线程是一个执行单元，可以执行多个app；对于Java程序来说，除了负责main方法的主线程之外，
	至少还有一个负责垃圾回收的线程；堆内存和方法区内存是多线程共享的，但栈内存是一个线程一个；
	Java中有多线程机制，目的是提高程序处理效率；使用了多线程机制之后，main方法结束程序可能还没有结束

	# 对于单核单线程的cpu来说，只能多线程并发执行，但无法多个程序并行运行，也就是不是真正意义上的多线程并发；
		要想并行运行程序，需要同时满足：程序用了多线程、操作系统支持多线程、处理器多线程；现在基本操作系统和硬件
		都是可以多线程的，只要程序中写了多线程就可以并行运行；

	# Java中实现多线程有三种方式：
		· 编写一个类直接extends继承java.lang.Thread，重写run方法；
			然后在main中实例化该类，调用它的start()方法即可；
		· 编写一个类实现Runnable接口，实现run()；new Thread()传进去这个类，
			然后调用Thread类的start()方法即可（更灵活，常用）
		· 编写一个类实现Callable接口实现call()，再创建一个未来任务类对象
			java.util.concurrent.FutureTask，将该类作为参数传进去；然后就可以
			new一个Thread，将futuretask传进去，启动线程，最后可以futuretask.get()
			获取线程的返回值，但是有可能会导致main线程阻塞；

	# Thread.currentThread()可以获取当前线程对象；t.getName()/t.setName()可以设置线程名字；

	# Thread.sleep(1000)让当前线程休眠5秒，静态的方法；t.interrupt()方法可以通过异常的方式来中断线程t的睡眠；

	# t.stop()强行终止t线程，已过时不建议用，容易造成数据的丢失；一般我们会采用在run()的外层加一个boolean的成员变量，
		然后run()里加一个判断，要终止线程直接修改该成员即可，这时可以在判断条件里添加结束该线程之前要做的东西；

	# t.getPriority()/t.setPriority(int)可以设置和获取线程的优先级[1~10]，默认5；
		Thread.yield()静态方法，让位方法，让当前线程从运行状态转为就绪状态，丢掉时间片；
	# t.join()线程插队，让t线程进入阻塞，当前线程执行完毕后才轮到它执行，
		里面还可以跟long类型参数，表示最多阻塞多少ms之后就继续抢占；

线程安全：
	# 满足什么情况会存在线程安全问题？
		1、多线程并发
		2、有共享数据
		3、共享数据有修改的行为
	# 怎么解决线程安全问题？线程排队执行，不能并发了，这种机制称为“线程同步机制”，这时效率就低了
	# 用synchronized(){}，小括号里放要多个线程共享的对象，大括号里写要排队执行的代码；
		在Java中，任何一个对象都有一把“锁”，当不同的线程执行到此处时，先执行到的会占有这个
		对象锁，此时其他线程必须等待该线程执行完代码块之后，归还这个对象锁了，再去抢这个锁去
		访问该对象；这样多个线程共享这个对象时，在同一时刻该对象只能被一个线程访问，实现了线程排队；
	# 局部变量不会共享数据，常量不能修改，所以它们不用考虑线程安全问题
	# synchronized出现在实例方法上锁的是this，范围是整个方法；出现在静态方法上是“类锁”，锁的是整个类，
		无论多少个对象只要是访问该类都需要排队
	# synchronized关键字最好不要嵌套使用，很可能会导致死锁，并且查不出来
	# 开发中通常怎么解决线程安全问题？
		1、尽量使用局部变量代替实例变量和静态变量
		2、如果必须是实例变量，那么可以考虑创建多个相同的实例变量，不共享当然没有线程安全问题了
		3、如果以上两种方案都不行，那么就使用synchronized
	# 重入锁ReentrantLock和synchronized作用类似，区别在于需要手动上锁开锁，该类中有lock()和unlock()方法

守护线程：
	Java中线程包括两大类：用户线程和守护线程（后台线程），垃圾回收线程就是守护线程，一般守护线程是一个死循环，
	用户线程只要结束，守护线程自动结束；如何将一个普通线程设置守护线程？start()开启线程之前，t.setDaemon(true)将该线程设置为守护线程；

定时器：间隔特定的时间，执行特定的程序；最原始的定时器是使用sleep方法，Java中有写好的定时器java.util.Timer，它有一个方法：
		void schedule(TimerTask task, Date firstTime, long period)
		第一个参数是定时器任务类，第二个参数是定时器的第一次执行时间，第三个参数是每间隔多久执行一次；
		编写一个类继承TimerTask类重并写run()，在run()中编写要定时执行的代码即可，然后将这个参数传进去即可
----------------------------------------------------------------------------------------------------
Object类中的wait()和notify()：
	obj.wait()表示将正在obj对象上活动的线程进入阻塞状态，直到被notify()唤醒，obj.notify()会将obj上等待的线程唤醒，
	重新执行，notifyAll()会唤醒obj对象上所有的线程；
```

```tex
反射机制（java.lang.reflect.*）
==================================================================================================================
# 反射机制有什么用呢？
	通过反射机制可以在运行期间动态获取程序信息、操作字节码文件

# 反射机制相关的类：
	java.lang.Class：代表整个字节码文件，代表一个类型
	java.lang.reflect.Method：代表字节码中的方法字节码
	java.lang.reflect.Constructor：代表字节码中的构造方法字节码
	java.lang.Field：代表字节码中的属性字节码

# 反射获取一个类有3种方式：
	· Class.forName(String className)：该方法的执行会导致类加载
	· obj.getClass()：java中任何一个对象都有getClass()
	· Java中任何一种类型，包括基本类型，都有class属性

# 通用路径：该路径是通用的，前提是：文件得在类路径下（idea中src是文件的类路径）
	Thread.currentThread().getContextClassLoader().getResource("").getPath();返回String
	解释：	Thread.currentThread()	当前线程对象
			getContextClassLoader() 这是线程对象的方法，可以获取到当前线程的类加载器
			getResource("")			使用当前线程的类加载器，从resource类路径下加载资源
			getPath()				将抽象路径File转换为路径字符串

# 这种方式可以很方便的读取xxx.properties属性配置文件：
	java.util包下提供了一个资源绑定器，便于获取属性配置文件中的内容，同样这种方式是基于通用路径的，需要放在src类路径下
	ResourceBundle rb = ResourceBundle.getBundle("xxx");
	String name = rb.getString("username");

-----------------------------------------------------------------------------------------------------------------
# 关于JDK中自带的类加载器：
	· 什么是类加载器？专门负责加载类的工具ClassLoader
	· JDK中自带了3个类加载器：启动类加载器、扩展类加载器、应用类加载器(系统类加载器)
	· 启动类加载器专门加载JDK最核心的类库，其余加载不到的话，调用扩展类加载器加载扩展包中的类，其余的类通过应用类加载器加载；
		应用类加载器专门加载classpath中的jar包；这种机制称为“双亲委派机制”

# 获取类加载器：
	· 获取当前线程上下文的类加载器Thread.currentThread().getContextClassLoader()
	· 获取系统类加载器ClassLoader.getSystemClassLoader()
	· 获取加载当前类文件的类加载器String.class.getClassLoader()

	系统类加载器是Java应用运行时的初始线程的上下文类加载器。它负责加载Java应用中的类与资源。可以通过调用ClassLoader.getSystemClassLoader()方法
来获取系统类加载器。系统类加载器是默认的类加载器，如果没有通过setContextClassLoader()方法设置线程上下文类加载器，线程将继承其父线程的上下文类加载器；
	线程上下文类加载器是从JDK1.2开始引入的，通过Thread类的getContextClassLoader()和setContextClassLoader(ClassLoader cl)方法来获取和设置
线程上下文类加载器。线程上下文类加载器可以用于在线程中加载类与资源，它可以独立于类本身的类加载器。
	加载该类文件的类加载器是加载当前执行代码所在类的类加载器。可以通过调用class.getClassLoader()方法来获取加载该类文件的类加载器。该类加载器负责加载
当前类及其所依赖的类。
	这三种类加载器之间的区别在于加载类的范围和加载顺序。系统类加载器负责加载Java应用中的类与资源，线程上下文类加载器用于在线程中加载类与资源，
加载该类文件的类加载器负责加载当前执行代码所在类及其所依赖的类。
	它们之间的联系在于：线程上下文类加载器可以独立于类本身的类加载器，可以在某些情况下用于解决类加载器的层次结构导致的类加载问题，
比如在使用一些扩展类库或框架时，线程上下文类加载器可以用来加载这些扩展类库或框架所依赖的类。
	综上所述，系统类加载器负责加载Java应用中的类与资源，线程上下文类加载器用于在线程中加载类与资源，加载该类文件的类加载器负责加载
当前执行代码所在类及其所依赖的类。它们之间有区别和联系，可以根据具体的需求来选择使用不同的类加载器。
---------------------------------------------------------------------------------------------------------------------

# 通过反射机制反射到类中的属性Field，然后可以通过nameField.set(obj, "张三")、nameField.get(obj)来对内存中的obj对象的name属性赋值；
	反射机制的nameField.setAccessible(true)可以打破封装，即使私有的属性也可以获取到，这是一个缺点

# 通过反射机制也可以调用方法Method，然后methodObj.invoke(obj, int.class)表示调用obj对象的该方法，且参数是一个int；
	反射机制可以很方便在运行期间动态的调用不同的方法，很灵活

# 可以通过一个类来反射它的构造器Constrator，然后用这个构造器的con.newInstance()可以创建跟这个构造器所对应的该对象，newInstance()里的参数不同调用的构造器也不同

# clazz.getSuperclass()和clazz.getInterfaces()可以调用类的父类和实现的所有接口

=====================================================================================================================
注解（java.lang.annotation）
	# 注解是一种类型，部分元注解编译后同样生成class文件，类似枚举接口和类，定义注解类型的格式为：[修饰符列表] @interface 注解名{}，使用注解：
		@注解名(属性名1=值,...)（注解可以出现在类、接口、枚举、方法、属性、变量、注解上等..，甚至说注解可以出现在任何地方）

	# 所有的注解会自动继承java.lang.Annotation接口，且不能再继承别的类或是接口

	# Java内置了哪些注解（java.lang.*）？
		Deprecated：该注解标志的东西通常不鼓励程序员使用，通常因为过时了很危险或有更好的选择
		Override：标志性注解，该注解标志的方法表示重写了父类的方法；所有标志了这个注解那么编译器会在编译阶段进行检查，和运行期无关
		SuppressWarnings：取消编译器警告的，不用掌握

	# 什么是元注解？以及有哪些元注解呢？
		元注解就是修饰注解的注解，常见的元注解有：
			Target：
			Retention：

	# 如果一个注解中有属性，那么必须给属性赋值@MyAnno(属性名=值)，除非有默认值；注解中的属性默认值这样定义：
		类型 属性名() default "张三"; 表示如果没给属性赋值有默认值

	# 如果一个注解中属性只有一个，且名字是value的话，赋值时属性名可以省略；类型是数组，且赋值时只给一个元素，那么数组大括号可以省略；
		注解的属性可以这样引用：anno.left()，有点像调用方法，但实际上是枚举的属性

	# Class类中有关于注解的方法：
			boolean isAnnotation()：如果这个类是注解，则返回true 
			boolean isAnnotationPresent(Class<? extends Annotation> annotationClass)：如果在该类上发现了指定类型的注解，则返回true  
			Annotation[] getDeclaredAnnotations()：获取该类上的所有注解
			Annotation getDeclaredAnnotation(Class<A> annotationClass)：获取该类上指定的注解
			Annotation getAnnotation(Class<A> annotationClass)：获取该类上指定的注解
			Annotation[] getAnnotations()：返回该类上的所有注解
============================================================================================================
Java8新特性：
	· 速度更快（底层的数据结构例如hashmap、垃圾回收机制都做了改动）
	· 代码更少（Lambda表达式）
	· 便于并行
	· s
------------------------------
· Lambda表达式：
· Stream API：
```

