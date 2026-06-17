# JDBC编程

> 程序运行的时候，往往需要存取数据。现代应用程序最基本，也是使用最广泛的数据存储就是关系数据库。
>
> Java为关系数据库定义了一套标准的访问接口：JDBC（Java Database Connectivity），本章我们介绍如何在Java程序中使用JDBC。

- ## JDBC概述

  - #### 什么是JDBC

    > JDBC（Java DataBase Connectivity）就是Java数据库连接，说白了就是用Java语言来操作数据库。原来我们操作数据库是在控制台使用SQL语句来操作数据库，JDBC是用Java语言向数据库发送SQL语句。

  - #### JDBC原理

    > 早期SUN公司的天才们想编写一套可以连接天下所有数据库的API，但是当他们刚刚开始时就发现这是不可完成的任务，因为各个厂商的数据库服务器差异太大了。后来SUN开始与数据库厂商们讨论，最终得出的结论是，由SUN提供一套访问数据库的规范（就是一组接口），并提供连接数据库的协议标准，然后各个数据库厂商会遵循SUN的规范提供一套访问自己公司数据库服务器的API。SUN提供的规范命名为JDBC，而各个厂商提供的，遵循了JDBC规范的，可以访问自己数据库的API被称之为驱动！
    >
    > ![图片2.png](../assets/1701931393355-983a997d-c44c-4a48-b1b0-91b281b5a98b.webp)
    >
    > JDBC是接口，而JDBC驱动才是接口的实现，没有驱动无法完成数据库连接！每个数据库厂商都有自己的驱动，用来连接自己公司的数据库。
    >
    > 当然还有第三方公司专门为某一数据库提供驱动，这样的驱动往往不是开源免费的！

  - #### 模拟JDBC接口

    1. ##### 接口在开发中的作用：

       > Java中接口的作用主要有以下几个方面：
       >
       > 1.  定义标准：接口可以用于定义标准，规范应该如何完成某个任务或应该具有哪些属性、方法等。
       > 2.  隐藏实现：接口隔离了类的实现和外界的逻辑使用，使得外部无论是访问接口的常量或是接口的方法都不需要关心接口的实现。
       > 3.  实现多态：一个类实现多个接口，在实现接口的过程中，类便会具有接口中的所有方法。这样我们就可以在实际应用中方便的实现多态的效果。
       > 4.  扩展性和灵活性：通过接口可以为项目提供更好的扩展性和灵活性，接口定义了一个共同的标准，使得新的类可以很容易地加入到已有的系统中，而且不需要修改现有的代码。
       >
       > 总的来说，Java中的接口可以让我们通过规范来编写更加标准和灵活的代码，使得代码易于维护和扩展，并通过多态的特性来提高代码的重用性和可读性。**Java接口在使用场景中，一定是存在两个角色的，一个是接口的调用者，一个是接口的实现者，接口的出现让调用者和实现者解耦合了。**

    2. ##### 编写程序模拟JDBC接口：

       > **接口的制定者**：SUN公司负责制定
       >
       > ```java
       > // SUN公司负责制定JDBC接口
       > public interface JDBC {
       >        // 负责连接数据库的方法
       >        void getConnection();
       > }
       > ```

       > **接口的实现者**：各大数据库厂商分别对JDBC接口进行实现，实现类被称为**驱动**
       >
       > - MySQL数据库厂商对JDBC接口的实现：MySQL驱动
       >
       >   ```java
       >   public class MySQLDriver implements JDBC {
       >       public void getConnection(){
       >       	System.out.println("与MySQL数据库连接建立成功，您正在操作MySQL数据库");
       >       }
       >   }
       >   ```
       >
       > - Oracle数据库厂商对JDBC接口的实现：Oracle驱动
       >
       >   ```java
       >   public class OracleDriver implements JDBC {
       >       public void getConnection(){
       >       	System.out.println("与Oracle数据库连接建立成功，您正在操作Oracle数据库");
       >       }
       >   }
       >   ```

       > **接口的调用者**：要操作数据库的Java程序员（我们）
       >
       > ```java
       > public class Client {
       >        public static void main(String[] args){
       >            JDBC jdbc = new MySQLDriver();
       >            // 只需要面向接口编程即可，不需要关心具体的实现，不需要关心具体是哪个厂商的数据库
       >            jdbc.getConnection();
       >        }
       > }
       > ```
       >
       > 以上是操作MySQL数据库，如果要操作Oracle数据库的话，需要`new OracleDriver()`：
       >
       > ```java
       > public class Client{
       >        public static void main(String[] args){
       >            JDBC jdbc = new OracleDriver();
       >            // 只需要面向接口编程即可，不需要关心具体的实现，不需要关心具体是哪个厂商的数据库
       >            jdbc.getConnection();
       >        }
       > }
       > ```

       > 可能你会说，最终还是修改了java代码，不符合OCP原则呀，如果你想达到OCP，那可以将创建对象的任务交给反射机制，将类名配置到文件中，例如：
       >
       > 配置文件如下：（jdbc.properties）
       >
       > ```properties
       > driver=MySQLDriver
       > ```
       >
       > Java代码如下：
       >
       > ```java
       > import java.util.ResourceBundle;
       > 
       > public class Client{
       >     public static void main(String[] args) throws Exception{
       >            String driverClassName = ResourceBundle.getBundle("jdbc").getString("driver");
       >            Class c = Class.forName(driverClassName);
       >            JDBC jdbc = (JDBC)c.newInstance();
       >            // 只需要面向接口编程即可，不需要关心具体的实现，不需要关心具体是哪个厂商的数据库
       >            jdbc.getConnection();
       >        }
       > }
       > ```
       >
       > 最终通过修改jdbc.properties配置文件即可做到数据库的切换。这样就完全做到了调用者和实现者的解耦合。调用者不需要关心实现者，实现者也不需要关心调用者。双方都是面向接口编程。这就是JDBC的本质：它就是一套接口。

  - #### 配置CLASSPATH

    > 经过上面内容的讲解，大家应该知道JDBC开发有三个角色的参与：
    >
    > - 我们（对数据库中数据进行增删改查的Java程序员）
    > - JDBC接口的制定者
    > - JDBC接口的实现者（驱动）
    >
    > 以上三者凑齐了我们才能进行JDBC的开发。它们三个都在哪里呢？“我们”就不用多说了，写操作数据库的代码就行了。JDBC接口在哪（接口的class文件在哪）？JDBC接口实现类在哪（驱动在哪）？

    1. ##### JDBC接口在哪：

       > JDBC接口在JDK中。对应的包是：**java.sql.\*;**
       >
       > ![image.png](../assets/1701939712048-f4487a29-3eb7-494f-b7c0-b72c6c0c03ad.webp)
       >
       > ![image.png](../assets/1701939824373-e1c98bbf-cc6a-44c0-95b6-d2c3a0ecbf52.webp)

    2. ##### 驱动在哪：

       > 驱动是JDBC接口的实现类（即一些`.class`文件），这些实现类是各大数据库厂家自己实现的，所以这些实现类的就需要去数据库厂商相关的网站上下载了。通常这些实现类被全部放到一个`xxx.jar`包中。

    3. ##### 如果使用文本编辑器开发：

       > 如果使用文本编辑器开发，不使用集成开发环境的话，以上的jar包就需要手动配置到环境变量CLASSPATH当中。

    4. ##### 如果使用IDEA工具开发：

       > 如果是采用集成开发工具，例如IDEA，就不需要手动配置CLASSPATH了，只需要将jar包放到IDEA中（实际上放到IDEA工具中的过程就是等同于在配置CLASSPATH）。具体操作这里不再赘述。

- ## JDBC的增删改

  - #### JDBC六步

    > JDBC编程的步骤是很固定的，通常包含以下六步：
    >
    > 1. 注册驱动
    >
    >    - 作用一：将 JDBC 驱动程序从硬盘上的文件系统中加载到内存中。
    >
    >    - 作用二：使得 DriverManager 可以通过一个统一的接口来管理该驱动程序的所有连接操作。
    >
    > 2. 获取（数据库）连接
    >
    >    - 获取java.sql.Connection对象，该对象的创建标志着 MySql 进程和 JVM 进程之间的通道打开了。
    >
    > 3. 创建（数据库）操作对象
    >
    >    - 创建java.sql.Statement对象，该对象负责将SQL语句发送给数据库，数据库会执行该SQL语句。
    >
    > 4. 执行SQL语句
    >
    >    - 执行具体的SQL语句，例如：insert delete update select等。
    >
    > 5. 处理查询结果集
    >
    >    - 如果之前的操作是DQL查询语句，才会有处理查询结果集这一步。
    >
    >    - 执行DQL语句通常会返回查询结果集对象：java.sql.ResultSet。
    >
    >    - 对于ResultSet查询结果集来说，通常的操作是针对查询结果集进行结果集的遍历。
    >
    > 6. 释放资源
    >
    >    - 释放资源可以避免资源的浪费。在 JDBC 编程中，每次使用完 Connection、Statement、ResultSet 等资源后，都需要显式地调用对应的 close() 方法来释放资源，避免资源的浪费。
    >
    >    - 释放资源可以避免出现内存泄露问题。在 Java 中，当一个对象不再被引用时，会被 JVM 的垃圾回收机制进行回收。但是在 JDBC 编程中，如果不显式地释放资源，那么这些资源就不会被 JVM 的垃圾回收机制自动回收，从而导致内存泄露问题。

  - #### 数据库中准备数据

    > 1. 使用PowerDesigner设计用户表t_user。
    > 2. 使用Navicat for MySQL创建数据库，创建表，插入数据。

  - #### JDBC完成增/删/改

    1. ##### 注册驱动：

         > 注册驱动有两个作用：
         >
         > 1. 将 JDBC 驱动程序从硬盘上的文件系统中加载到内存。
         > 2. 让 DriverManager 可以通过一个统一的接口来管理该驱动程序的所有连接操作。
         >
         > ![image.png](../assets/1702375429683-7a25727f-4615-45f1-a56c-fd02eea60dd2.webp)

       ```java
       import java.sql.Driver;
       import java.sql.DriverManager;
       import java.sql.SQLException;
       
       public class JDBCTest01 {
           public static void main(String[] args){
               try {
                   // 1. 注册驱动
                   Driver driver = new com.mysql.cj.jdbc.Driver(); // 创建MySQL驱动对象
                   DriverManager.registerDriver(driver); // 完成驱动注册
               } catch(SQLException e){
                   e.printStackTrace();
               }
           }
       }
       ```

       > **注意：注册驱动调用的是java.sql.DriverManager的registerDriver()方法。这些方法的使用要参阅JDK的API帮助文档。**
       >
       > 思考1：为什么以上代码中new的时候，后面类名要带上包名呢？
       >
       > 思考2：以上代码中哪些是JDBC接口，哪些是JDBC接口的实现？

    2. ##### 获取连接：

         > 获取java.sql.Connection对象，该对象的创建标志着mysql进程和jvm进程之间的通道打开了。
         >
         > ![image.png](../assets/1702375535525-fad3b7e2-b7f5-4079-a2e1-31c597ae8165.webp)

       ```java
       import java.sql.Driver;
       import java.sql.DriverManager;
       import java.sql.SQLException;
       import java.sql.Connection;
       
       public class JDBCTest01 {
           public static void main(String[] args){
               try {
                   // 1. 注册驱动
                   Driver driver = new com.mysql.cj.jdbc.Driver(); // 创建MySQL驱动对象
                   DriverManager.registerDriver(driver); // 完成驱动注册
       
                   // 2. 获取连接
                   String url = "jdbc:mysql://localhost:3306/jdbc";
                   String user = "root";
                   String password = "123456";
                   Connection conn = DriverManager.getConnection(url, user, password);
       
                   System.out.println("连接对象：" + conn);
               } catch(SQLException e){
                   e.printStackTrace();
               }
           }
       }
       ```

         > 执行结果如下：
         >
         > ![image.png](../assets/1702372789612-ac3b38a8-6f6d-44f2-8024-ccab0bac9380.webp)
         >
         > 看到以上的输出结果，表示数据库已经连接成功了。
         >
         > 通过以上程序的输出结果得知：com.mysql.cj.jdbc.ConnectionImpl是java.sql.Connection接口的实现类，大家可以想象一下，如果换成Oracle数据库的话，这个实现类的类名是不是就会换一个呢？答案是肯定的。不过对于我们来说是不需要关心具体实现类的，因为后续的代码都是直接面向java.sql.Connection接口来调用方法的。面向接口编程在这里体现的淋漓尽致。确实降低了耦合度。
         >
         > 以上程序中演示了连接数据库需要提供三个信息：url，用户名，密码。其中用户名和密码容易理解。url是什么？

         ###### 什么是URL？

         > URL 是统一资源定位符 (Uniform Resource Locator) 的缩写，是互联网上标识、定位、访问资源的字符串。它可以用来指定互联网上各种类型的资源的位置，如网页、图片、视频等。
         >
         > URL 通常由协议、服务器名、服务器端口、路径和查询字符串组成。其中：
         >
         > - 协议是规定了访问资源所采用的通信协议，例如 HTTP、HTTPS、FTP 等；
         > - 服务器名是资源所在的服务器主机名或 IP 地址，可以是域名或 IP 地址；
         > - 服务器端口是资源所在的服务器的端口号；
         > - 路径是资源所在的服务器上的路径、文件名等信息；
         > - 查询字符串是向服务器提交的参数信息，用来定位更具体的资源。
         >
         > URL 在互联网中广泛应用，比如在浏览器中输入 URL 来访问网页或下载文件，在网站开发中使用 URL 来访问 API 接口或文件，在移动应用和桌面应用中使用 URL 来访问应用内部的页面或功能，在搜索引擎中使用 URL 来爬取网页内容等等。
         >
         > 总之，URL 是互联网上所有资源的唯一识别标识，是互联网通信的基础和核心技术之一。

         ###### JDBC连接MySQL时的URL格式：

         > JDBC URL 是在使用 JDBC 连接数据库时的一个 URL 字符串，它用来标识要连接的数据库的位置、认证信息和其他配置参数等。JDBC URL 的格式可以因数据库类型而异，但通常包括以下几个部分：
         >
         > - 协议：表示要使用的数据库管理系统（DBMS）的类型，如 `jdbc:mysql` 表示要使用 MySQL 数据库，`jdbc:postgresql` 表示要使用 PostgreSQL 数据库。
         > - 主机地址和端口号：表示要连接的数据库所在的服务器的 IP 地址或域名，以及数据库所在服务器监听的端口号。
         > - 数据库名称：表示要连接的数据库的名称。
         > - 其他可选参数：这些参数包括连接的超时时间、使用的字符集、连接池相关配置等。
         >
         > 例如，连接 MySQL 数据库的 JDBC URL 的格式一般如下：
         >
         > ```url
         > jdbc:mysql://<host>:<port>/<database_name>?<connection_parameters>
         > ```
         >
         > 其中：
         >
         > - `<host>` 是 MySQL 数据库服务器的主机名或 IP 地址；
         > - `<port>` 是 MySQL 服务器的端口号（默认为 3306）；
         > - `<database_name>` 是要连接的数据库名称；
         > - `<connection_parameters>` 包括连接的额外参数，例如用户名、密码、字符集等。
         >
         > JDBC URL 是连接数据库的关键，通过 JDBC URL，应用程序可以通过特定的 JDBC 驱动程序与数据库服务器进行通信，从而实现与数据库的交互。在开发 Web 应用和桌面应用时，使用 JDBC URL 可以轻松地连接和操作各种类型的数据库，例如 MySQL、PostgreSQL、Oracle 等。
         >
         > 以下是一个常见的JDBC MySQL URL：
         >
         > ```url
         > jdbc:mysql://localhost:3306/jdbc
         > ```
         >
         > - `jdbc:mysql://`是协议
         > - `localhost`表示连接本地主机的MySQL数据库，也可以写作`127.0.0.1`
         > - `3306`是MySQL数据库的端口号
         > - `jdbc`是数据库实例名

         ###### MySQL URL中的其它常用配置：

         > 在 JDBC MySQL URL 中，常用的配置参数有：

       - `serverTimezone`：MySQL 服务器时区，默认为 UTC，可以通过该参数来指定数据库服务器的时区；

           > 在 JDBC URL 中设置 `serverTimezone` 的作用是**指定数据库服务器的时区**。这个时区信息会影响 JDBC 驱动在处理日期时间相关数据类型时如何将其映射到数据库服务器上的日期时间值。
           >
           > 如果不设置 `serverTimezone`，则 JDBC 驱动程序默认将使用本地时区，也就是客户端机器（web服务器）上的系统时区，来处理日期时间数据。在这种情况下，如果数据库服务器的时区和客户端机器的时区不同，那么处理日期时间数据时可能会出现问题，从而导致数据错误或不一致。
           >
           > 例如，假设数据库服务器位于美国加州，而客户端（WEB服务器）位于中国上海，如果不设置 `serverTimezone` 参数，在客户端执行类似下面的查询：
           >
           > ```sql
           > SELECT * FROM orders WHERE order_date = '2022-11-11';
           > ```
           >
           > 由于客户端和服务器使用了不同的时区，默认使用的是客户端本地的时区，那么实际查询的时间就是客户端本地时间对应的时间，而不是服务器的时间。这可能会导致查询结果不正确，因为数据库服务器上的时间可能是比客户端慢或者快了多个小时。
           >
           > 通过在 JDBC URL 中设置 `serverTimezone` 参数，可以明确告诉 JDBC 驱动程序使用哪个时区来处理日期时间值，从而避免这种问题。在上述例子中，如果把时区设置为 `America/Los_Angeles`（即加州的时区）：
           >
           > ```url
           > jdbc:mysql://localhost:3306/mydatabase?user=myusername&password=mypassword&serverTimezone=America/Los_Angeles
           > ```
           >
           > 那么上面的查询就会在数据库服务器上以加州的时间来执行，结果更加准确。

       - `useSSL`：是否使用 SSL 进行连接，默认为 true；

           > `useSSL` 参数用于配置是否使用 SSL（Secure Sockets Layer）安全传输协议来加密 JDBC 和 MySQL 数据库服务器之间的通信。其设置为 `true` 表示使用 SSL 连接，设置为 `false` 表示不使用 SSL 连接。其区别如下：
           >
           > 当设置为 `true` 时，JDBC 驱动程序将使用 SSL 加密协议来保障客户端和服务器之间的通信安全。这种方式下，所有数据都会使用 SSL 加密后再传输，可以有效防止数据在传输过程中被窃听、篡改等安全问题出现。当然，也要求MySQL服务器端必须支持 SSL，否则会连接失败。
           >
           > 当设置为 `false` 时，JDBC 驱动程序会以明文方式传输数据，这种方式下，虽然数据传输的速度会更快，但也会存在被恶意攻击者截获和窃听数据的风险。因此，在不安全的网络环境下，或是要求数据传输安全性较高的情况下，建议使用 SSL 加密连接。
           >
           > 需要注意的是，使用 SSL 连接会对系统资源和性能消耗有一定的影响，特别是当连接数较多时，对 CPU 和内存压力都比较大。因此，在性能和安全之间需要权衡，根据实际应用场景合理设置 `useSSL` 参数。

       - `useUnicode`：是否使用Unicode编码进行数据传输，默认是true启用；

           > `useUnicode`是 JDBC 驱动程序连接数据库时的一个参数，用于告诉驱动程序在传输数据时是否使用 Unicode 编码。Unicode 是计算机科学中的一种字符编码方案，可以用于表示全球各种语言中的字符，包括 ASCII 码、中文、日文、韩文等。因此，使用 Unicode 编码可以确保数据在传输过程中能够正确、完整地呈现各种语言的字符。
           >
           > 具体地说，如果设置 `useUnicode=true`，JDBC 驱动程序会在传输数据时使用 Unicode 编码。这意味着，无论数据库中使用的是什么编码方案，都会先将数据转换为 Unicode 编码进行传输，确保数据能够跨平台、跨数据库正确传输。当从数据库中获取数据时，驱动程序会根据 `characterEncoding` 参数指定的字符集编码将数据转换为指定编码格式，以便应用程序正确处理数据。
           >
           > 需要注意的是，如果设置 `useUnicode=false`，则表示使用当前平台默认的字符集进行数据传输。这可能会导致在跨平台或跨数据库时出现字符编码不一致的问题，因此通常建议在进行数据传输时启用 Unicode 编码。
           >
           > 综上所述，设置 `useUnicode` 参数可以确保数据在传输过程中正确呈现各种字符集编码。对于应用程序处理多语言环境数据的场景，启用 `useUnicode` 参数尤为重要。

       - `characterEncoding`：连接使用的字符编码，默认为 UTF-8；

           > `characterEncoding` 参数用于设置 MySQL 服务器和 JDBC 驱动程序之间进行字符集转换时使用的字符集编码。其设置为 `UTF-8` 表示使用 UTF-8 编码进行字符集转换，设置为 `GBK` 表示使用 GBK 编码进行字符集转换。其区别如下：
           >
           > UTF-8 编码是一种可变长度的编码方式，可以表示世界上的所有字符，包括 ASCII、Unicode 和不间断空格等字符，是一种通用的编码方式。UTF-8 编码在国际化应用中被广泛使用，并且其使用的字节数较少，有利于提高数据传输的效率和节约存储空间。
           >
           > GBK 编码是一种固定长度的编码方式，只能表示汉字和部分符号，不能表示世界上的所有字符。GBK 编码通常只用于中文环境中，因为在英文和数字等字符中会出现乱码情况。
           >
           > 因此，在 MySQL 中使用 `UTF-8` 编码作为字符集编码的优势在于能够支持世界上的所有字符，而且在国际化应用中使用广泛，对于不同语言和地区的用户都能够提供良好的支持。而使用 `GBK` 编码则主要在于适用于中文环境中的数据存储和传输。
           >
           > 需要注意的是，在选择编码方式时需要考虑到应用本身的实际需要和数据的特性，根据具体情况进行选择，避免出现字符集编码错误的问题。同时，还要确保 MySQL 服务器、JDBC 驱动程序和应用程序之间的字符集编码一致，避免出现字符集转换错误的问题。

             > **注意：useUnicode和characterEncoding有什么区别？**
             >
             > - **useUnicode设置的是数据在传输过程中是否使用Unicode编码方式。**
             > - **characterEncoding设置的是数据被传输到服务器之后，服务器采用哪一种字符集进行编码。**
             >
             > 例如，连接 MySQL 数据库的 JDBC URL 可以如下所示：
             >
             > ```url
             > jdbc:mysql://localhost:3306/jdbc?useUnicode=true&serverTimezone=Asia/Shanghai&useSSL=true&characterEncoding=utf-8
             > ```
             >
             > 这里演示的是使用本地 MySQL 数据库，使用Unicode编码进行数据传输，服务器时区为 Asia/Shanghai，启用 SSL 连接，服务器接收到数据后使用 UTF-8 编码。

    3. ##### 获取操作对象：

         > 数据库操作对象是这个接口：java.sql.Statement。这个对象负责将SQL语句发送给数据库服务器，服务器接收到SQL后进行编译，然后执行SQL。
         >
         > ![image.png](../assets/1702441460073-15a0cb32-b979-442c-900b-ec3109722750.webp)

       ```java
       import java.sql.Driver;
       import java.sql.DriverManager;
       import java.sql.SQLException;
       import java.sql.Connection;
       import java.sql.Statement;
       
       public class JDBCTest01 {
           public static void main(String[] args){
               try {
                   // 1. 注册驱动
                   Driver driver = new com.mysql.cj.jdbc.Driver(); // 创建MySQL驱动对象
                   DriverManager.registerDriver(driver); // 完成驱动注册
       
                   // 2. 获取连接
                   String url = "jdbc:mysql://localhost:3306/jdbc?useUnicode=true&serverTimezone=Asia/Shanghai&useSSL=true&characterEncoding=utf-8";
                   String user = "root";
                   String password = "123456";
                   Connection conn = DriverManager.getConnection(url, user, password);
       
                   // 3. 获取数据库操作对象
                   Statement stmt1 = conn.createStatement();
                   System.out.println("数据库操作对象stmt = " + stmt1);
       				// 通过一个Connection对象是可以创建多个Statement对象的：
                   Statement stmt2 = conn.createStatement();
                   System.out.println("数据库操作对象stmt2 = " + stmt2);
               } catch(SQLException e){
                   e.printStackTrace();
               }
           }
       }
       ```

         > 执行结果如下：
         >
         > ![image.png](../assets/1702441181097-76775506-0d8a-4c6b-a077-4feaf637c82a.webp)
         >
         > 同样可以看到：java.sql.Statement接口在MySQL驱动中的实现类是：com.mysql.cj.jdbc.StatementImpl。不过我们同样是不需要关心这个具体的实现类。因为后续的代码仍然是面向Statement接口写代码的。

    4. ##### 执行SQL：

         > 当获取到Statement对象后，调用这个接口中的相关方法即可执行SQL语句。
         >
         > ![image.png](../assets/1702441577751-223506b2-73c9-47ee-a614-4b95fe555df3.webp)
         >
         > - 该方法的参数是一个SQL语句，只要将insert语句传递过来即可。当执行executeUpdate(sql)方法时，JDBC会将sql语句发送给数据库服务器，数据库服务器对SQL语句进行编译，然后执行SQL。
         > - 该方法的返回值是int类型，返回值的含义是：影响了数据库表当中几条记录。例如：返回1表示1条数据插入成功，返回2表示2条数据插入成功，以此类推。如果一条也没有插入，则返回0。
         > - 该方法适合执行的SQL语句是DML，包括：insert delete update。

       ```java
       import java.sql.Driver;
       import java.sql.DriverManager;
       import java.sql.SQLException;
       import java.sql.Connection;
       import java.sql.Statement;
       
       public class JDBCTest01 {
           public static void main(String[] args){
               try {
                   // 1. 注册驱动
                   Driver driver = new com.mysql.cj.jdbc.Driver(); // 创建MySQL驱动对象
                   DriverManager.registerDriver(driver); // 完成驱动注册
       
                   // 2. 获取连接
                   String url = "jdbc:mysql://localhost:3306/jdbc?useUnicode=true&serverTimezone=Asia/Shanghai&useSSL=true&characterEncoding=utf-8";
                   String user = "root";
                   String password = "123456";
                   Connection conn = DriverManager.getConnection(url, user, password);
       
                   // 3. 获取数据库操作对象
                   Statement stmt = conn.createStatement();
       
                   // 4. 执行SQL语句
                   String sql = "insert into t_user(name,password,realname,gender,tel) values('tangsanzang','123','唐三藏','男','12566568956')"; // sql语句最后的分号';'可以不写。
                   int count = stmt.executeUpdate(sql);
                   System.out.println("插入了" + count + "条记录");
                   
               } catch(SQLException e){
                   e.printStackTrace();
               }
           }
       }
       ```

         > 执行结果如下：
         >
         > ![image.png](../assets/1702441948716-d8c4638f-5d7a-4d26-b3d0-a2ed4872e6a4.webp)
         >
         > ![image.png](../assets/1702441971625-ba842802-4881-48ab-a4f3-17acc229d9c2.webp)

    5. ##### 释放资源：

         > 第五步去哪里了？第五步是处理查询结果集，以上操作不是select语句，所以第五步直接跳过，直接先看一下第六步释放资源。【后面学习查询语句的时候，再详细看第五步】
         
         - ##### 为什么要释放资源？
         
           > 在 JDBC 编程中，建立数据库连接、创建 Statement 对象等操作都需要申请系统资源，例如打开网络端口、申请内存等。为了避免占用过多的系统资源和避免出现内存泄漏等问题，我们需要在使用完资源后及时释放它们。
         
         - ##### 释放资源的原则：
         
           1. ###### 原则1：在finally语句块中释放
         
              > 建议在finally语句块中释放，因为程序执行过程中如果出现了异常，finally语句块中的代码是一定会执行的。也就是说：我们需要保证程序在执行过程中，不管是否出现了异常，最后的关闭是一定要执行的。当然了，也可以使用Java7的新特性：Try-with-resources。Try-with-resources 是 Java 7 引入的新特性。它简化了资源管理的代码实现，可以自动释放资源，减少了代码出错的可能性，同时也可以提供更好的代码可读性和可维护性。
         
           2. ###### 原则2：释放有顺序
         
              > 从小到大依次释放，创建的时候，先创建Connection，再创建Statement。那么关闭的时候，先关闭Statement，再关闭Connection。
         
           3. ###### 原则3：分别进行try...catch...
         
              > 关闭的时候调用close()方法，该方法有异常需要处理，建议分别对齐try...catch...进行异常捕获。如果只编写一个try...catch...进行一块捕获，在关闭过程中，如果某个关闭失败，会影响下一个资源的关闭。
         
         - ##### 代码：
         
           ```java
           import java.sql.Driver;
           import java.sql.DriverManager;
           import java.sql.SQLException;
           import java.sql.Connection;
           import java.sql.Statement;
           
           public class JDBCTest01 {
               public static void main(String[] args){
                   Connection conn = null;
                   Statement stmt = null;
                   try {
                       // 1. 注册驱动
                       Driver driver = new com.mysql.cj.jdbc.Driver(); // 创建MySQL驱动对象
                       DriverManager.registerDriver(driver); // 完成驱动注册
           
                       // 2. 获取连接
                       String url = "jdbc:mysql://localhost:3306/jdbc?useUnicode=true&serverTimezone=Asia/Shanghai&useSSL=true&characterEncoding=utf-8";
                       String user = "root";
                       String password = "123456";
                       conn = DriverManager.getConnection(url, user, password);
           
                       // 3. 获取数据库操作对象
                       stmt = conn.createStatement();
           
                       // 4. 执行SQL语句
                       String sql = "insert into t_user(name,password,realname,gender,tel) values('tangsanzang','123','唐三藏','男','12566568956')"; // sql语句最后的分号';'可以不写。
                       int count = stmt.executeUpdate(sql);
                       System.out.println("插入了" + count + "条记录");
                       
                   } catch(SQLException e){
                       e.printStackTrace();
                   } finally {
                       // 6. 释放资源
                       if(stmt != null){
                           try{
                               stmt.close();
                           }catch(SQLException e){
                               e.printStackTrace();
                           }
                       }
                       if(conn != null){
                           try{
                               conn.close();
                           }catch(SQLException e){
                               e.printStackTrace();
                           }
                       }
                   }
               }
           }
           ```

  - #### 注册驱动的常用方式

    > 上面在注册驱动的时候，执行了这样的代码：
    >
    > ```java
    > java.sql.Driver driver = new com.mysql.cj.jdbc.Driver();
    > java.sql.DriverManager.registerDriver(driver);
    > ```
    >
    > 这种方式是自己new驱动对象，然后调用DriverManager的registerDriver()方法来完成驱动注册，还有另一种方式，并且这种方式是常用的：
    >
    > ```java
    > Class.forName("com.mysql.cj.jdbc.Driver");
    > ```
    >
    > 为什么这种方式常用？
    >
    > - 第一：代码少了很多。
    > - 第二：这种方式可以很方便的将`com.mysql.cj.jdbc.Driver`类名配置到属性文件当中。
    >
    > 实现原理是什么？找一下`com.mysql.cj.jdbc.Driver`的源码：
    >
    > ![image.png](../assets/1702447333885-23189b4e-5767-4dba-ae21-bdc543241db6.webp)
    >
    > 通过源码不难发现，在`com.mysql.cj.jdbc.Driver`类中有一个静态代码块，在这个静态代码块中调用了`java.sql.DriverManager.registerDriver(new Driver());`完成了驱动的注册。而`Class.forName("com.mysql.cj.jdbc.Driver");`代码的作用就是让`com.mysql.cj.jdbc.Driver`类完成加载，执行它的静态代码块。

  - #### JDBC4.0后不用手动注册驱动（了解）

    > 从JDBC 4.0（**也就是Java6**）版本开始，驱动的注册不需要再手动完成，系统会根据JDBC URL自动选择合适的驱动来完成。
    >
    > ```java
    > import java.sql.DriverManager;
    > import java.sql.SQLException;
    > import java.sql.Connection;
    > import java.sql.Statement;
    > 
    > public class JDBCTest03 {
    >     public static void main(String[] args){
    >         Connection conn = null;
    >         Statement stmt = null;
    >         try {
    >             // 2. 获取连接
    >             String url = "jdbc:mysql://localhost:3306/jdbc?useUnicode=true&serverTimezone=Asia/Shanghai&useSSL=true&characterEncoding=utf-8";
    >             String user = "root";
    >             String password = "123456";
    >             conn = DriverManager.getConnection(url, user, password);
    > 
    >             // 3. 获取数据库操作对象
    >             stmt = conn.createStatement();
    > 
    >             // 4. 执行SQL语句
    >             String sql = "insert into t_user(name,password,realname,gender,tel) values('tangsanzang','123','唐三藏','男','12566568956')"; // sql语句最后的分号';'可以不写。
    >             int count = stmt.executeUpdate(sql);
    >             System.out.println("插入了" + count + "条记录");
    >             
    >         } catch(SQLException e){
    >             e.printStackTrace();
    >         } finally {
    >             // 6. 释放资源
    >             if(stmt != null){
    >                 try{
    >                     stmt.close();
    >                 }catch(SQLException e){
    >                     e.printStackTrace();
    >                 }
    >             }
    >             if(conn != null){
    >                 try{
    >                     conn.close();
    >                 }catch(SQLException e){
    >                     e.printStackTrace();
    >                 }
    >             }
    >         }
    >     }
    > }
    > ```
    >
    > **注意**：虽然大部分情况下不需要进行手动注册驱动了，但在实际的开发中有些数据库驱动程序不支持自动发现功能，仍然需要手动注册。所以建议大家还是别省略了。

  - #### 动态配置连接数据库的信息

    > 为了程序的通用性，为了切换数据库的时候不需要修改Java程序，为了符合OCP开闭原则，建议将连接数据库的信息配置到属性文件中，例如：
    >
    > ```properties
    > driver=com.mysql.cj.jdbc.Driver
    > url=jdbc:mysql://localhost:3306/jdbc?useUnicode=true&serverTimezone=Asia/Shanghai&useSSL=true&characterEncoding=utf-8
    > user=root
    > password=123456
    > ```
    >
    > 然后使用IO流读取属性文件，动态获取连接数据库的信息：
    >
    > ```java
    > import java.sql.DriverManager;
    > import java.sql.SQLException;
    > import java.sql.Connection;
    > import java.sql.Statement;
    > import java.util.ResourceBundle;
    > 
    > public class JDBCTest04 {
    >     public static void main(String[] args){
    >         
    >     	// 通过以下代码获取属性文件中的配置信息
    > 		ResourceBundle bundle = ResourceBundle.getBundle("jdbc");
    > 		String driver = bundle.getString("driver");
    > 		String url = bundle.getString("url");
    > 		String user = bundle.getString("user");
    > 		String password = bundle.getString("password");
    > 
    >         Connection conn = null;
    >         Statement stmt = null;
    >         try {
    >             // 1. 注册驱动
    >             Class.forName(driver);
    > 
    >             // 2. 获取连接
    >             conn = DriverManager.getConnection(url, user, password);
    > 
    >             // 3. 创建数据库操作对象
    >             stmt = conn.createStatement();
    > 
    >             // 4. 执行SQL语句
    >             String sql = "insert into t_user(name,password,realname,gender,tel) values('tangsanzang','123','唐三藏','男','12566568956')"; // sql语句最后的分号';'可以不写。
    >             int count = stmt.executeUpdate(sql);
    >             System.out.println("插入了" + count + "条记录");
    >             
    >         } catch(SQLException | ClassNotFoundException e){
    >             e.printStackTrace();
    >         } finally {
    >             // 6. 释放资源
    >             if(stmt != null){
    >                 try{
    >                     stmt.close();
    >                 }catch(SQLException e){
    >                     e.printStackTrace();
    >                 }
    >             }
    >             if(conn != null){
    >                 try{
    >                     conn.close();
    >                 }catch(SQLException e){
    >                     e.printStackTrace();
    >                 }
    >             }
    >         }
    >     }
    > }
    > ```
    >
    > 以后要连接其他数据库，只要修改属性文件中的配置即可。

  - #### 获取连接的其他方式（了解）

    > 除了以上的这种方式之外，还有两种方式，通过API帮助文档可以看到：
    >
    > ![image.png](../assets/1702453145756-8174de75-a789-4030-b496-8b7fd700afb6.webp)

    - ##### `getConnection(String url)`：

      > 这种方式参数只有一个url，那用户名和密码放在哪里呢？可以放到url当中，代码如下：
      >
      > ```java
      > import java.sql.Driver;
      > import java.sql.DriverManager;
      > import java.sql.SQLException;
      > import java.sql.Connection;
      > 
      > public class JDBCTest05 {
      >     public static void main(String[] args){
      >         try {
      >             // 1. 注册驱动
      >             Class.forName("com.mysql.cj.jdbc.Driver");
      > 
      >             // 2. 获取连接
      >             String url = "jdbc:mysql://localhost:3306/jdbc?user=root&password=123456";
      >             Connection conn = DriverManager.getConnection(url);
      > 
      >             System.out.println("连接对象：" + conn);
      >         } catch(SQLException|ClassNotFoundException e){
      >             e.printStackTrace();
      >         }
      >     }
      > }
      > ```

    - ##### `getConnection(String url, Properties info)`：

      > 这种方式有两个参数，一个是url，一个是Properties对象。
      >
      > - url：可以单纯提供一个url地址
      > - info：可以将url的参数存放到该对象中
      >
      > ```java
      > import java.sql.Driver;
      > import java.sql.DriverManager;
      > import java.sql.SQLException;
      > import java.sql.Connection;
      > import java.util.Properties;
      > 
      > public class JDBCTest06 {
      >     public static void main(String[] args){
      >         try {
      >             // 1. 注册驱动
      >             Class.forName("com.mysql.cj.jdbc.Driver");
      > 
      >             // 2. 获取连接
      >             String url = "jdbc:mysql://localhost:3306/jdbc";
      >             
      >             Properties info = new Properties();
      >             info.setProperty("user", "root");
      >             info.setProperty("password", "123456");
      >             info.setProperty("useUnicode", "true");
      >             info.setProperty("serverTimezone", "Asia/Shanghai");
      >             info.setProperty("useSSL", "true");
      >             info.setProperty("characterEncoding", "utf-8");
      >             
      >             Connection conn = DriverManager.getConnection(url, info);
      > 
      >             System.out.println("连接对象：" + conn);
      >         } catch(SQLException|ClassNotFoundException e){
      >             e.printStackTrace();
      >         }
      >     }
      > }
      > ```

- ## JDBC的查询

  - #### JDBC的查询操作

    > ResultSet 是 JDBC （Java 数据库连接） API 提供的接口，它用于表示 SQL 查询的结果集。ResultSet 对象中包含了查询结果的所有行，可以通过 next() 方法逐行地获取并处理每一行的数据。它最常用于执行 SELECT 语句查询出来的结果集。
    >
    > ResultSet 的遍历是基于 JDBC 的**流式处理机制**的，即一行一行地获取结果，避免将所有结果全部取出后再进行处理导致内存溢出问题。
    >
    > 在使用 ResultSet 遍历查询结果时，一般会采用以下步骤：
    >
    > 1. 执行 SQL 查询，获取 `ResultSet` 对象。
    > 2. 使用 `ResultSet` 的 `next()` 方法移动游标指向结果集的下一行，判断是否有更多的数据行。
    > 3. 如果有更多的数据行，则使用 `ResultSet` 对象提供的 `getXXX()` 方法获取当前行的各个字段（XXX 表示不同的数据类型）。例如，`getLong("id")` 方法用于获取当前行的 id 列对应的 `Long` 类型的值。
    > 4. 处理当前行的数据，例如将其存入 Java 对象中。
    > 5. 重复执行步骤 2~4，直到结果集中的所有行都被遍历完毕。
    > 6. 调用 `ResultSet` 的 `close()` 方法释放资源。
    >
    > 需要注意的是，在使用完 ResultSet 对象之后，需要及时关闭它，以释放数据库资源并避免潜在的内存泄漏问题。否则，如果在多个线程中打开了多个 ResultSet 对象，并且没有正确关闭它们的话，可能会导致数据库连接过多，从而影响系统的稳定性和性能。

    1. ##### 通过列索引获取数据（以String类型获取）：

       > 需求：获取t_user表中所有数据，在控制台打印输出每一行的数据。
       >
       > ```sql
       > select id,name,password,realname,gender,tel from t_user;
       > ```
       >
       > 要查询的数据如下图：
       >
       > ![image.png](../assets/1702536722789-fc5bbe25-598a-4619-b5b0-2dc1871da569.webp)

       ###### 代码如下（重点关注第4步、第5步、第6步）：

       ```java
       import java.sql.DriverManager;
       import java.sql.SQLException;
       import java.sql.Connection;
       import java.sql.Statement;
       import java.util.ResourceBundle;
       import java.sql.ResultSet;
       
       public class JDBCTest09 {
           public static void main(String[] args){
               
           	// 通过以下代码获取属性文件中的配置信息
       		ResourceBundle bundle = ResourceBundle.getBundle("jdbc");
       		String driver = bundle.getString("driver");
       		String url = bundle.getString("url");
       		String user = bundle.getString("user");
       		String password = bundle.getString("password");
       
               Connection conn = null;
               Statement stmt = null;
               ResultSet rs = null;
               try {
                   // 1. 注册驱动
                   Class.forName(driver);
       
                   // 2. 获取连接
                   conn = DriverManager.getConnection(url, user, password);
       
                   // 3. 获取数据库操作对象
                   stmt = conn.createStatement();
       
                   // 4. 执行SQL语句
                   String sql = "select id,name,password,realname,gender,tel from t_user";
                   rs = stmt.executeQuery(sql);
       
                   // 5. 处理查询结果集（这里的处理方式就是：遍历所有数据并输出）
                   while(rs.next()){
                       String id = rs.getString(1);
                       String name = rs.getString(2);
                       String pwd = rs.getString(3);
                       String realname = rs.getString(4);
                       String gender = rs.getString(5);
                       String tel = rs.getString(6);
                       System.out.println(id + "\t" + name + "\t" + pwd + "\t" + realname + "\t" + gender + "\t" + tel);
                   }
                   
               } catch(SQLException | ClassNotFoundException e){
                   e.printStackTrace();
               } finally {
                   // 6. 释放资源
                   if(rs != null){
                       try{
                           rs.close();
                       }catch(SQLException e){
                           e.printStackTrace();
                       }
                   }
                   if(stmt != null){
                       try{
                           stmt.close();
                       }catch(SQLException e){
                           e.printStackTrace();
                       }
                   }
                   if(conn != null){
                       try{
                           conn.close();
                       }catch(SQLException e){
                           e.printStackTrace();
                       }
                   }
               }
           }
       }
       ```

       > 执行结果如下：
       >
       > ![image.png](../assets/1702537178277-7ea8b4eb-1088-45cb-9493-18fe44b90287.webp)

       ###### 代码解读：

       ```java
       // 4. 执行SQL语句
       String sql = "select id,name,password,realname,gender,tel from t_user";
       rs = stmt.executeQuery(sql);
       ```

       > 执行insert delete update语句的时候，调用Statement接口的 `executeUpdate()` 方法。
       >
       > 执行select语句的时候，**调用Statement接口的 `executeQuery()` 方法**。执行select语句后返回结果集对象：ResultSet。

       ```java
       // 5. 处理查询结果集（这里的处理方式就是：遍历所有数据并输出）
       while(rs.next()){
           String id = rs.getString(1);
           String name = rs.getString(2);
           String pwd = rs.getString(3);
           String realname = rs.getString(4);
           String gender = rs.getString(5);
           String tel = rs.getString(6);
           System.out.println(id + "\t" + name + "\t" + pwd + "\t" + realname + "\t" + gender + "\t" + tel);
       }
       ```

       > - rs.next() 将游标移动到下一行，如果移动后指向的这一行有数据则返回true，没有数据则返回false。
       > - while循环体当中的代码是处理当前游标指向的这一行的数据。（注意：是处理的一行数据）
       > - rs.getString(int columnIndex) 其中 int 类型的 columnIndex 是查询结果的列下标，列下标从1开始，以1递增。
       >
       > ![image.png](../assets/1702538306701-4341b895-f91b-4501-af67-4746b6327884.webp)
       >
       > - rs.getString(...) 方法在执行时，不管底层数据库中的数据类型是什么，统一以字符串 String 类型来接收。

       ```java
       // 6. 释放资源
       if(rs != null){
           try{
               rs.close();
           }catch(SQLException e){
               e.printStackTrace();
           }
       }
       if(stmt != null){
           try{
               stmt.close();
           }catch(SQLException e){
               e.printStackTrace();
           }
       }
       if(conn != null){
           try{
               conn.close();
           }catch(SQLException e){
               e.printStackTrace();
           }
       }
       ```

       > ResultSet最终也是需要关闭的。**先关闭ResultSet，再关闭Statement，最后关闭Connection**。

    2. ##### 通过列名获取数据（以String类型获取）：

       > 获取当前行的数据，不仅可以通过列下标获取，还可以通过查询结果的列名来获取，通常这种方式是被推荐的，因为可读性好：`rs.getString(String name)`
       >
       > 例如这样的SQL：
       >
       > ```sql
       > select id, name as username, realname from t_user;
       > ```
       >
       > 执行结果是：
       >
       > ![image.png](../assets/1702539677907-26c84361-6874-421b-a612-dd754f7fb8f3.webp)
       >
       > 我们可以按照查询结果的列名来获取数据：
       >
       > ![image.png](../assets/1702540371842-53a5c738-db3b-4040-aa0a-cd6dc9b9bb22.webp)
       >
       > **注意：是根据查询结果的列名，而不是表中的列名。以上查询的时候将字段name起别名username了，所以要根据username来获取，而不能再根据name来获取了。**

    3. ##### 以指定的类型获取数据：

       > 前面的程序可以看到，不管数据库表中是什么数据类型，都以String类型返回。当然，也能以指定类型返回。
       >
       > 使用PowerDesigner再设计一张商品表：t_product，使用Navicat for MySQL工具准备数据如下：
       >
       > ![image.png](../assets/1702541223024-4e5acb77-ef8b-4437-ba3d-10c02ba0999b.webp)
       >
       > ![image.png](../assets/1702541500905-f5e0a70b-19b0-4469-97db-68e414f92984.webp)
       >
       > id以long类型获取，name以String类型获取，price以double类型获取，create_time以`java.sql.Date`类型获取，代码如下：
       >
       > ```java
       > import java.sql.DriverManager;
       > import java.sql.SQLException;
       > import java.sql.Connection;
       > import java.sql.Statement;
       > import java.util.ResourceBundle;
       > import java.sql.ResultSet;
       > 
       > public class JDBCTest11 {
       >     public static void main(String[] args){
       >         
       >     	// 通过以下代码获取属性文件中的配置信息
       > 		ResourceBundle bundle = ResourceBundle.getBundle("jdbc");
       > 		String driver = bundle.getString("driver");
       > 		String url = bundle.getString("url");
       > 		String user = bundle.getString("user");
       > 		String password = bundle.getString("password");
       > 
       >         Connection conn = null;
       >         Statement stmt = null;
       >         ResultSet rs = null;
       >         try {
       >             // 1. 注册驱动
       >             Class.forName(driver);
       > 
       >             // 2. 获取连接
       >             conn = DriverManager.getConnection(url, user, password);
       > 
       >             // 3. 获取数据库操作对象
       >             stmt = conn.createStatement();
       > 
       >             // 4. 执行SQL语句
       >             String sql = "select id,name,price,create_time as createTime from t_product";
       >             rs = stmt.executeQuery(sql);
       > 
       >             // 5. 处理查询结果集（这里的处理方式就是：遍历所有数据并输出）
       >             while(rs.next()){
       >                 long id = rs.getLong("id");
       >                 String name = rs.getString("name");
       >                 double price = rs.getDouble("price");
       >                 java.sql.Date createTime = rs.getDate("createTime");
       >                 // 以指定类型获取后是可以直接用的，例如获取到价格后，统一让价格乘以2
       >                 System.out.println(id + "\t" + name + "\t" + price * 2 + "\t" + createTime);
       >             }
       >             
       >         } catch(SQLException | ClassNotFoundException e){
       >             e.printStackTrace();
       >         } finally {
       >             // 6. 释放资源
       >             if(rs != null){
       >                 try{
       >                     rs.close();
       >                 }catch(SQLException e){
       >                     e.printStackTrace();
       >                 }
       >             }
       >             if(stmt != null){
       >                 try{
       >                     stmt.close();
       >                 }catch(SQLException e){
       >                     e.printStackTrace();
       >                 }
       >             }
       >             if(conn != null){
       >                 try{
       >                     conn.close();
       >                 }catch(SQLException e){
       >                     e.printStackTrace();
       >                 }
       >             }
       >         }
       >     }
       > }
       > ```
       >
       > 执行结果如下：
       >
       > ![image.png](../assets/1702541874721-10c9a4f2-370f-4ce4-985e-6cf8da2e3ffb.webp)

    4. ##### 获取结果集的元数据信息（了解）：

       > `ResultSetMetaData` 是一个接口，用于描述 ResultSet 中的元数据信息，即查询结果集的结构信息，例如查询结果集中包含了哪些列，每个列的数据类型、长度、标识符等。
       >
       > ResultSetMetaData 可以通过 ResultSet 接口的 `getMetaData()` 方法获取，一般在对 ResultSet 进行元数据信息处理时使用。例如，可以使用 ResultSetMetaData 对象获取查询结果中列的信息，如列名、列的类型、列的长度等。通过 ResultSetMetaData 接口的方法，可以实现对查询结果的基本描述信息操作，例如获取查询结果集中有多少列、列的类型、列的标识符等。以下是一段通过 ResultSetMetaData 获取查询结果中列的信息的示例代码：
       >
       > ```java
       > import java.sql.DriverManager;
       > import java.sql.SQLException;
       > import java.sql.Connection;
       > import java.sql.Statement;
       > import java.util.ResourceBundle;
       > import java.sql.ResultSet;
       > import java.sql.ResultSetMetaData;
       > 
       > public class JDBCTest12 {
       >        public static void main(String[] args){
       >                 
       >        	// 通过以下代码获取属性文件中的配置信息
       > 		ResourceBundle bundle = ResourceBundle.getBundle("jdbc");
       > 		String driver = bundle.getString("driver");
       > 		String url = bundle.getString("url");
       > 		String user = bundle.getString("user");
       > 		String password = bundle.getString("password");
       > 
       >            Connection conn = null;
       >            Statement stmt = null;
       >            ResultSet rs = null;
       >            try {
       >                // 1. 注册驱动
       >                Class.forName(driver);
       > 
       >                // 2. 获取连接
       >                conn = DriverManager.getConnection(url, user, password);
       > 
       >                // 3. 获取数据库操作对象
       >                stmt = conn.createStatement();
       > 
       >                // 4. 执行SQL语句
       >                String sql = "select id,name,price,create_time as createTime from t_product";
       >                rs = stmt.executeQuery(sql);
       > 
       >                // 获取元数据信息
       >                ResultSetMetaData rsmd = rs.getMetaData();
       >                int columnCount = rsmd.getColumnCount();
       >                for (int i = 1; i <= columnCount; i++) {
       >                    System.out.println("列名：" + rsmd.getColumnName(i) + "，数据类型：" + rsmd.getColumnTypeName(i) +
       >                                       "，列的长度：" + rsmd.getColumnDisplaySize(i));
       >                }
       >                         
       >            } catch(SQLException | ClassNotFoundException e){
       >                e.printStackTrace();
       >            } finally {
       >                // 6. 释放资源
       >                if(rs != null){
       >                    try{
       >                        rs.close();
       >                    }catch(SQLException e){
       >                        e.printStackTrace();
       >                    }
       >                }
       >                if(stmt != null){
       >                    try{
       >                        stmt.close();
       >                    }catch(SQLException e){
       >                        e.printStackTrace();
       >                    }
       >                }
       >                if(conn != null){
       >                    try{
       >                        conn.close();
       >                    }catch(SQLException e){
       >                        e.printStackTrace();
       >                    }
       >                }
       >            }
       >        }
       > }
       > ```
       >
       > 执行结果如下：
       >
       > ![image.png](../assets/1702542217121-d413b4dc-1bf2-45ca-80c8-551d2a3705b8.webp)
       >
       > 在上面的代码中，我们首先创建了一个 Statement 对象，然后执行了一条 SQL 查询语句，生成了一个 ResultSet 对象。接下来，我们通过 ResultSet 对象的 getMetaData() 方法获取了 ResultSetMetaData 对象，进而获取了查询结果中列的信息并进行输出。需要注意的是，在进行列信息的获取时，列的编号从 1 开始计算。该示例代码将获取查询结果集中所有列名、数据类型以及长度等信息。

  - #### 获取新增行的自增主键

    > 有很多表的主键字段值都是自增的，在某些特殊的业务环境下，当我们插入了新数据后，希望能够获取到这条新数据的主键值，应该如何获取呢？
    >
    > 在 JDBC 中，如果要获取插入数据后的主键值，可以使用 Statement 接口的 `executeUpdate()` 方法的重载版本，该方法接受一个额外的参数，用于指定是否需要获取自动生成的主键值。然后，通过以下两个步骤获取插入数据后的主键值：
    >
    > 1. 在执行 `executeUpdate()` 方法时指定一个标识位，表示需要返回插入的主键值。
    > 2. 调用 Statement 对象的 `getGeneratedKeys()` 方法，返回一个包含插入的主键值的 ResultSet 对象。 
    >
    > ```java
    > import java.sql.DriverManager;
    > import java.sql.SQLException;
    > import java.sql.Connection;
    > import java.sql.Statement;
    > import java.util.ResourceBundle;
    > import java.sql.ResultSet;
    > 
    > public class JDBCTest13 {
    >     public static void main(String[] args){
    > 
    >     	// 通过以下代码获取属性文件中的配置信息
    > 		ResourceBundle bundle = ResourceBundle.getBundle("jdbc");
    > 		String driver = bundle.getString("driver");
    > 		String url = bundle.getString("url");
    > 		String user = bundle.getString("user");
    > 		String password = bundle.getString("password");
    > 
    >         Connection conn = null;
    >         Statement stmt = null;
    >         ResultSet rs = null;
    >         try {
    >             // 1. 注册驱动
    >             Class.forName(driver);
    > 
    >             // 2. 获取连接
    >             conn = DriverManager.getConnection(url, user, password);
    > 
    >             // 3. 获取数据库操作对象
    >             stmt = conn.createStatement();
    > 
    >             // 4. 执行SQL语句
    >             String sql = "insert into t_user(name,password,realname,gender,tel) values('zhangsan','111','张三','男','19856525352')";
    >             // 第一步
    >             int count = stmt.executeUpdate(sql, Statement.RETURN_GENERATED_KEYS);
    >             // 第二步
    >             rs = stmt.getGeneratedKeys();
    >             if(rs.next()){
    >                 int id = rs.getInt(1);
    >                 System.out.println("新增数据行的主键值：" + id);
    >             }
    > 
    >         } catch(SQLException | ClassNotFoundException e){
    >             e.printStackTrace();
    >         } finally {
    >             // 6. 释放资源
    >             if(rs != null){
    >                 try{
    >                     rs.close();
    >                 }catch(SQLException e){
    >                     e.printStackTrace();
    >                 }
    >             }
    >             if(stmt != null){
    >                 try{
    >                     stmt.close();
    >                 }catch(SQLException e){
    >                     e.printStackTrace();
    >                 }
    >             }
    >             if(conn != null){
    >                 try{
    >                     conn.close();
    >                 }catch(SQLException e){
    >                     e.printStackTrace();
    >                 }
    >             }
    >         }
    >     }
    > }
    > ```
    >
    > 执行结果如下：
    >
    > ![image.png](../assets/1702543846750-ba186e76-04fa-4ef1-8d57-7fc40598c02e.webp)
    >
    > ![image.png](../assets/1702543887747-8bfa21c9-e7b4-49c9-8dec-dbca00a645a7.webp)
    >
    > 以上代码中，我们将 `Statement.RETURN_GENERATED_KEYS` 传递给 `executeUpdate()` 方法，以指定需要获取插入的主键值。然后，通过调用 Statement 对象的 `getGeneratedKeys()` 方法获取包含插入的主键值的 ResultSet 对象，通过 ResultSet 对象获取主键值。需要注意的是，在使用 Statement 对象的 `getGeneratedKeys()` 方法获取自动生成的主键值时，主键值的获取方式具有一定的差异，需要根据不同的数据库种类和版本来进行调整。
    >
    > （注意：获取的自动生成的主键的顺序，不一定和sql语句执行的顺序一致，不同数据库的行为可能不同）

------

