# MybatisPlus

> 大家在日常开发中应该能发现，单表的CRUD功能代码重复度很高，也没有什么难度。而这部分代码量往往比较大，开发起来比较费时。
>
> 因此，目前企业中都会使用一些组件来简化或省略单表的CRUD开发工作。目前在国内使用较多的一个组件就是MybatisPlus。
>
> 官方网站：https://www.baomidou.com/
>
> 当然，MybatisPlus不仅仅可以简化单表操作，而且还对Mybatis的功能有很多的增强。可以让我们的开发更加的简单，高效。

- ## 快速入门

  > 为了方便测试，我们先创建一个新的项目，并准备一些基础数据。

  - #### 环境准备

    > 复制课前资料提供好的一个项目到你的工作空间（不要包含空格和特殊字符）：
    >
    > ![img](./assets/1720167485060-2fcace79-c324-4739-a754-6a00842171c4.webp)
    >
    > 然后用你的IDEA工具打开，项目结构如下：
    >
    > ![img](./assets/1720167485303-dfb97790-c4dd-4dac-97c9-12011b529a2f.webp)
    >
    > 注意配置一下项目的JDK版本为JDK11。首先点击项目结构设置：
    >
    > ![img](./assets/1720167485527-fb4747e5-14e6-40da-8185-5309a87dd6a6.webp)
    >
    > 在弹窗中配置JDK：
    >
    > ![img](./assets/1720167485535-24811187-db1e-464f-b82d-07d15badfa36.webp)
    >
    > 接下来，要导入两张表，在课前资料中已经提供了SQL文件：
    >
    > ![img](./assets/1720167485501-85bda113-6774-4cc3-9df6-f5fc380acc20.webp)
    >
    > 对应的数据库表结构如下：
    >
    > ![img](./assets/1720167485923-d2d51d0a-43b7-4bf2-847e-9ca9e978e9bf.webp)
    >
    > 最后，在application.yaml中修改jdbc参数为你自己的数据库参数：
    >
    > ```yaml
    > spring:
    >     datasource:
    >         url: jdbc:mysql://127.0.0.1:3306/mp?useUnicode=true&characterEncoding=UTF-8&autoReconnect=true&serverTimezone=Asia/Shanghai
    >         driver-class-name: com.mysql.cj.jdbc.Driver
    >         username: root
    >         password: MySQL123
    > logging:
    >     level:
    >     com.itheima: debug
    >     pattern:
    >     dateformat: HH:mm:ss
    > ```

  - #### 快速开始

    > 比如我们要实现User表的CRUD，只需要下面几步：
    >
    > - 引入MybatisPlus依赖
    > - 定义Mapper

    1. ##### 引入依赖

       > MybatisPlus提供了starter，实现了自动Mybatis以及MybatisPlus的自动装配功能，坐标如下：
       >
       > ```xml
       > <dependency>
       >     <groupId>com.baomidou</groupId>
       >     <artifactId>mybatis-plus-boot-starter</artifactId>
       >     <version>3.5.3.1</version>
       > </dependency>
       > ```
       >
       > 由于这个starter包含对mybatis的自动装配，因此完全可以替换掉Mybatis的starter。 最终，项目的依赖如下：
       >
       > ```xml
       > <dependencies>
       >   <!--最终把下面的改成这个-->
       >   <dependency>
       >     <groupId>com.baomidou</groupId>
       >     <artifactId>mybatis-plus-boot-starter</artifactId>
       >     <version>3.5.3.1</version>
       >   </dependency>
       >   <dependency>
       >     <groupId>com.mysql</groupId>
       >     <artifactId>mysql-connector-j</artifactId>
       >     <scope>runtime</scope>
       >   </dependency>
       >   <dependency>
       >     <groupId>org.projectlombok</groupId>
       >     <artifactId>lombok</artifactId>
       >     <optional>true</optional>
       >   </dependency>
       >   <dependency>
       >     <groupId>org.springframework.boot</groupId>
       >     <artifactId>spring-boot-starter-test</artifactId>
       >     <scope>test</scope>
       >   </dependency>
       > </dependencies>
       > ```

    2. ##### 定义Mapper

       > 为了简化单表CRUD，MybatisPlus提供了一个基础的BaseMapper接口，其中已经实现了单表的CRUD：
       >
       > ![img](./assets/1720167486140-96b5232e-442a-4bb4-8620-678893195acc.webp)
       >
       > 因此我们自定义的Mapper只要实现了这个BaseMapper，就无需自己实现单表CRUD了。 修改mp-demo中的com.itheima.mp.mapper包下的UserMapper接口，让其继承BaseMapper：
       >
       > ![img](./assets/1720167486472-cd9f2f6e-4490-4d93-bc32-1e851204c930.webp)
       >
       > 代码如下：
       >
       > ```java
       > package com.itheima.mp.mapper;
       > 
       > import com.baomidou.mybatisplus.core.mapper.BaseMapper;
       > import com.itheima.mp.domain.po.User;
       > 
       > public interface UserMapper extends BaseMapper<User> {}
       > ```

    3. ##### 测试

       > 新建一个测试类，编写几个单元测试，测试基本的CRUD功能：
       >
       > ```java
       > package com.itheima.mp.mapper;
       > 
       > import com.itheima.mp.domain.po.User;
       > import org.junit.jupiter.api.Test;
       > import org.springframework.beans.factory.annotation.Autowired;
       > import org.springframework.boot.test.context.SpringBootTest;
       > 
       > import java.time.LocalDateTime;
       > import java.util.List;
       > 
       > @SpringBootTest
       > class UserMapperTest {
       > 
       >     @Autowired
       >     private UserMapper userMapper;
       > 
       >     @Test
       >     void testInsert() {
       >         User user = new User();
       >         user.setId(5L);
       >         user.setUsername("Lucy");
       >         user.setPassword("123");
       >         user.setPhone("18688990011");
       >         user.setBalance(200);
       >         user.setInfo("{\"age\": 24, \"intro\": \"英文老师\", \"gender\": \"female\"}");
       >         user.setCreateTime(LocalDateTime.now());
       >         user.setUpdateTime(LocalDateTime.now());
       >         userMapper.insert(user);
       >     }
       > 
       >     @Test
       >     void testSelectById() {
       >         User user = userMapper.selectById(5L);
       >         System.out.println("user = " + user);
       >     }
       > 
       >     @Test
       >     void testSelectByIds() {
       >         List<User> users = userMapper.selectBatchIds(List.of(1L, 2L, 3L, 4L, 5L));
       >         users.forEach(System.out::println);
       >     }
       > 
       >     @Test
       >     void testUpdateById() {
       >         User user = new User();
       >         user.setId(5L);
       >         user.setBalance(20000);
       >         userMapper.updateById(user);
       >     }
       > 
       >     @Test
       >     void testDelete() {
       >         userMapper.deleteById(5L);
       >     }
       > }
       > ```
       >
       > 可以看到，在运行过程中打印出的SQL日志，非常标准：
       >
       > ```java
       > 11:05:01  INFO 15524 --- [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Starting...
       > 11:05:02  INFO 15524 --- [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Start completed.
       > 11:05:02 DEBUG 15524 --- [           main] c.i.mp.mapper.UserMapper.selectById      : ==>  Preparing: SELECT id,username,password,phone,info,status,balance,create_time,update_time FROM user WHERE id=?
       > 11:05:02 DEBUG 15524 --- [           main] c.i.mp.mapper.UserMapper.selectById      : ==> Parameters: 5(Long)
       >                                                                                                         11:05:02 DEBUG 15524 --- [           main] c.i.mp.mapper.UserMapper.selectById      : <==      Total: 1
       > user = User(id=5, username=Lucy, password=123, phone=18688990011, info={"age": 21}, status=1, balance=20000, createTime=Fri Jun 30 11:02:30 CST 2023, updateTime=Fri Jun 30 11:02:30 CST 2023)
       > ```
       >
       > 只需要继承BaseMapper就能省去所有的单表CRUD，是不是非常简单！

  - #### 常见注解

    > 在刚刚的入门案例中，我们仅仅引入了依赖，继承了BaseMapper就能使用MybatisPlus，非常简单。但是问题来了： MybatisPlus如何知道我们要查询的是哪张表？表中有哪些字段呢？
    >
    > 大家回忆一下，UserMapper在继承BaseMapper的时候指定了一个泛型：
    >
    > ![img](./assets/1720167486790-e4eea350-063c-456f-8661-895d163f07d5.webp)
    >
    > 泛型中的User就是与数据库对应的PO。
    >
    > MybatisPlus就是根据PO实体的信息来推断出表的信息，从而生成SQL的。默认情况下：
    >
    > - MybatisPlus会把PO实体的类名驼峰转下划线作为表名
    > - MybatisPlus会把PO实体的所有变量名驼峰转下划线作为表的字段名，并根据变量类型推断字段类型
    > - MybatisPlus会把名为id的字段作为主键
    >
    > 但很多情况下，默认的实现与实际场景不符，因此MybatisPlus提供了一些注解便于我们声明表信息。

    1. ##### `@TableName`

       > 说明：
       >
       > - 描述：表名注解，标识实体类对应的表
       > - 使用位置：实体类
       >
       > 示例：
       >
       > ```java
       > @TableName("user")
       > public class User {
       >     private Long id;
       >     private String name;
       > }
       > ```
       >
       > TableName注解除了指定表名以外，还可以指定很多其它属性：
       >
       > | **属性**         | **类型** | **必须指定** | **默认值** | **描述**                                                     |
       > | ---------------- | -------- | ------------ | ---------- | ------------------------------------------------------------ |
       > | value            | String   | 否           | ""         | 表名                                                         |
       > | schema           | String   | 否           | ""         | schema                                                       |
       > | keepGlobalPrefix | boolean  | 否           | false      | 是否保持使用全局的 tablePrefix 的值（当全局 tablePrefix 生效时） |
       > | resultMap        | String   | 否           | ""         | xml 中 resultMap 的 id（用于满足特定类型的实体类对象绑定）   |
       > | autoResultMap    | boolean  | 否           | false      | 是否自动构建 resultMap 并使用（如果设置 resultMap 则不会进行 resultMap 的自动构建与注入） |
       > | excludeProperty  | String[] | 否           | {}         | 需要排除的属性名 @since 3.3.1                                |

    2. ##### `@TableId`

       > 说明：
       >
       > - 描述：主键注解，标识实体类中的主键字段
       > - 使用位置：实体类的主键字段
       >
       > 示例：
       >
       > ```java
       > @TableName("user")
       > public class User {
       >     @TableId
       >     private Long id;
       >     private String name;
       > }
       > ```
       >
       > TableId注解支持两个属性：
       >
       > | **属性** | **类型** | **必须指定** | **默认值**  | **描述**     |
       > | -------- | -------- | ------------ | ----------- | ------------ |
       > | value    | String   | 否           | ""          | 表名         |
       > | type     | Enum     | 否           | IdType.NONE | 指定主键类型 |
       >
       > IdType支持的类型有：
       >
       > | **值**        | **描述**                                                     |
       > | ------------- | ------------------------------------------------------------ |
       > | AUTO          | 数据库 ID 自增                                               |
       > | NONE          | 无状态，该类型为未设置主键类型（注解里等于跟随全局，全局里约等于 INPUT） |
       > | INPUT         | insert 前自行 set 主键值                                     |
       > | ASSIGN_ID     | 分配 ID(主键类型为 Number(Long 和 Integer)或 String)(since 3.3.0),使用接口IdentifierGenerator的方法nextId(默认实现类为DefaultIdentifierGenerator雪花算法) |
       > | ASSIGN_UUID   | 分配 UUID,主键类型为 String(since 3.3.0),使用接口IdentifierGenerator的方法nextUUID(默认 default 方法) |
       > | ID_WORKER     | 分布式全局唯一 ID 长整型类型(please use ASSIGN_ID)           |
       > | UUID          | 32 位 UUID 字符串(please use ASSIGN_UUID)                    |
       > | ID_WORKER_STR | 分布式全局唯一 ID 字符串类型(please use ASSIGN_ID)           |
       >
       > 这里比较常见的有三种：
       >
       > - AUTO：利用数据库的id自增长
       > - INPUT：手动生成id
       > - ASSIGN_ID：雪花算法生成Long类型的全局唯一id，这是默认的ID策略

    3. ##### `@TableField`

       > 说明：
       >
       > 描述：普通字段注解
       >
       > 示例：
       >
       > ```java
       > @TableName("user")
       > public class User {
       >     @TableId
       >     private Long id;
       >     private String name;
       >     private Integer age;
       >     @TableField("isMarried")
       >     private Boolean isMarried;
       >     @TableField("concat")
       >     private String concat;
       > }
       > ```
       >
       > 一般情况下我们并不需要给字段添加@TableField注解，一些特殊情况除外：
       >
       > - 成员变量名与数据库字段名不一致
       > - 成员变量是以isXXX命名，按照JavaBean的规范，MybatisPlus识别字段时会把is去除，这就导致与数据库不符。
       > - 成员变量名与数据库一致，但是与数据库的关键字冲突。使用@TableField注解给字段名添加转义字符：``
       >
       > 支持的其它属性如下：
       >
       > | **属性**         | **类型**   | **必填** | **默认值**            | **描述**                                                     |
       > | ---------------- | ---------- | -------- | --------------------- | ------------------------------------------------------------ |
       > | value            | String     | 否       | ""                    | 数据库字段名                                                 |
       > | exist            | boolean    | 否       | true                  | 是否为数据库表字段                                           |
       > | condition        | String     | 否       | ""                    | 字段 where 实体查询比较条件，有值设置则按设置的值为准，没有则为默认全局的 %s=#{%s}，[参考(opens new window)](https://github.com/baomidou/mybatis-plus/blob/3.0/mybatis-plus-annotation/src/main/java/com/baomidou/mybatisplus/annotation/SqlCondition.java) |
       > | update           | String     | 否       | ""                    | 字段 update set 部分注入，例如：当在version字段上注解update="%s+1" 表示更新时会 set version=version+1 （该属性优先级高于 el 属性） |
       > | insertStrategy   | Enum       | 否       | FieldStrategy.DEFAULT | 举例：NOT_NULL insert into table_a(<if test="columnProperty != null">column</if>) values (<if test="columnProperty != null">#{columnProperty}</if>) |
       > | updateStrategy   | Enum       | 否       | FieldStrategy.DEFAULT | 举例：IGNORED update table_a set column=#{columnProperty}    |
       > | whereStrategy    | Enum       | 否       | FieldStrategy.DEFAULT | 举例：NOT_EMPTY where <if test="columnProperty != null and columnProperty!=''">column=#{columnProperty}</if> |
       > | fill             | Enum       | 否       | FieldFill.DEFAULT     | 字段自动填充策略                                             |
       > | select           | boolean    | 否       | true                  | 是否进行 select 查询                                         |
       > | keepGlobalFormat | boolean    | 否       | false                 | 是否保持使用全局的 format 进行处理                           |
       > | jdbcType         | JdbcType   | 否       | JdbcType.UNDEFINED    | JDBC 类型 (该默认值不代表会按照该值生效)                     |
       > | typeHandler      | TypeHander | 否       |                       | 类型处理器 (该默认值不代表会按照该值生效)                    |
       > | numericScale     | String     | 否       | ""                    | 指定小数点后保留的位数                                       |

  - #### 常见配置

    > ![image.png](./assets/1720355089573-0792a9b5-b76e-4573-be9b-0d3ecbbc97d0.webp)
    >
    > MybatisPlus也支持基于yaml文件的自定义配置，详见官方文档：
    >
    > **https://www.baomidou.com/pages/56bac0/#%E5%9F%BA%E6%9C%AC%E9%85%8D%E7%BD%AE**
    >
    > 大多数的配置都有默认值，因此我们都无需配置。但还有一些是没有默认值的，例如:
    >
    > - 实体类的别名扫描包
    > - 全局id类型
    >
    > ```yaml
    > mybatis-plus:
    >   type-aliases-package: com.itheima.mp.domain.po
    >   global-config:
    >     db-config:
    >       id-type: auto # 全局id类型为自增长
    > ```
    >
    > 需要注意的是，MyBatisPlus也支持手写SQL的，而mapper文件的读取地址可以自己配置：
    >
    > ```yaml
    > mybatis-plus:
    >   mapper-locations: "classpath*:/mapper/**/*.xml" # Mapper.xml文件地址，当前这个是默认值。
    > ```
    >
    > 可以看到默认值是classpath*:/mapper/**/*.xml，也就是说我们只要把mapper.xml文件放置这个目录下就一定会被加载。
    >
    > 例如，我们新建一个UserMapper.xml文件：
    >
    > ![img](./assets/1720167487320-66ef5e1f-6b92-43cc-947d-90385812629d.webp)
    >
    > 然后在其中定义一个方法：
    >
    > ```java
    > User queryById(@Param("id") Long id);
    > ```
    >
    > ```xml
    > <?xml version="1.0" encoding="UTF-8"?>
    > <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    > <mapper namespace="com.itheima.mp.mapper.UserMapper">
    > 
    >   <select id="queryById" resultType="User">
    >     SELECT * FROM user WHERE id = #{id}
    >   </select>
    > </mapper>
    > ```
    >
    > 然后在测试类UserMapperTest中测试该方法：
    >
    > ```java
    > @Test
    > void testQuery() {
    >     User user = userMapper.queryById(1L);
    >     System.out.println("user = " + user);
    > }
    > ```

- ## 核心功能

  > 刚才的案例中都是以id为条件的简单CRUD，一些复杂条件的SQL语句就要用到一些更高级的功能了。

  - #### 条件构造器

    > 除了新增以外，修改、删除、查询的SQL语句都需要指定where条件。因此BaseMapper中提供的相关方法除了以id作为where条件以外，还支持更加复杂的where条件。
    >
    > ![img](./assets/1720167487359-5b346701-7f2d-4cf8-a6e4-64f2b8deaf20.webp)
    >
    > 参数中的Wrapper就是条件构造的抽象类，其下有很多默认实现，继承关系如图：
    >
    > ![img](./assets/1720167487469-6b3f18b3-1c68-44a7-9cf7-316ec9d1b01b.webp)
    >
    > Wrapper的子类AbstractWrapper提供了where中包含的所有条件构造方法：
    >
    > ![img](./assets/1720167487578-331636c7-1168-401f-b3a8-888fed21798a.webp)
    >
    > 而QueryWrapper在AbstractWrapper的基础上拓展了一个select方法，允许指定查询字段：
    >
    > ![img](./assets/1720167487773-5d9340e1-ebe4-4895-a639-c8ceb6d5f54b.webp)
    >
    > 而UpdateWrapper在AbstractWrapper的基础上拓展了一个set方法，允许指定SQL中的SET部分：
    >
    > ![img](./assets/1720167488004-0c75b6ef-8ca5-41b1-828d-c436408c2cd2.webp)
    >
    > 接下来，我们就来看看如何利用Wrapper实现复杂查询。

    1. ##### `QueryWrapper`

       > 无论是修改、删除、查询，都可以使用QueryWrapper来构建查询条件。接下来看一些例子： **查询**：查询出名字中带o的，存款大于等于1000元的人。代码如下：
       >
       > ```java
       > @Test
       > void testQueryWrapper() {
       >     // 1.构建查询条件 where name like "%o%" AND balance >= 1000
       >     QueryWrapper<User> wrapper = new QueryWrapper<User>()
       >     .select("id", "username", "info", "balance")
       >     .like("username", "o")
       >     .ge("balance", 1000);
       >     // 2.查询数据
       >     List<User> users = userMapper.selectList(wrapper);
       >     users.forEach(System.out::println);
       > }
       > ```
       >
       > **更新**：更新用户名为jack的用户的余额为2000，代码如下：
       >
       > ```java
       > @Test
       > void testUpdateByQueryWrapper() {
       >     // 1.构建查询条件 where name = "Jack"
       >     QueryWrapper<User> wrapper = new QueryWrapper<User>().eq("username", "Jack");
       >     // 2.更新数据，user中非null字段都会作为set语句
       >     User user = new User();
       >     user.setBalance(2000);
       >     userMapper.update(user, wrapper);
       > }
       > ```

    2. ##### `UpdateWrapper`

       > 基于BaseMapper中的update方法更新时只能直接赋值，对于一些复杂的需求就难以实现。 例如：更新id为1,2,4的用户的余额，扣200，对应的SQL应该是：
       >
       > ```sql
       > UPDATE user SET balance = balance - 200 WHERE id in (1, 2, 4)
       > ```
       >
       > SET的赋值结果是基于字段现有值的，这个时候就要利用UpdateWrapper中的setSql功能了：
       >
       > ```java
       > @Test
       > void testUpdateWrapper() {
       >     List<Long> ids = List.of(1L, 2L, 4L);
       >     // 1.生成SQL
       >     UpdateWrapper<User> wrapper = new UpdateWrapper<User>()
       >     .setSql("balance = balance - 200") // SET balance = balance - 200
       >     .in("id", ids); // WHERE id in (1, 2, 4)
       >     // 2.更新，注意第一个参数可以给null，也就是不填更新字段和数据，
       >     // 而是基于UpdateWrapper中的setSQL来更新
       >     userMapper.update(null, wrapper);
       > }
       > ```

    3. ##### `LambdaQueryWrapper`

       > 无论是QueryWrapper还是UpdateWrapper在构造条件的时候都需要写死字段名称，会出现字符串魔法值。这在编程规范中显然是不推荐的。 那怎么样才能不写字段名，又能知道字段名呢？
       >
       > 其中一种办法是基于变量的gettter方法结合反射技术。因此我们只要将条件对应的字段的getter方法传递给MybatisPlus，它就能计算出对应的变量名了。而传递方法可以使用JDK8中的方法引用和Lambda表达式。 因此MybatisPlus又提供了一套基于Lambda的Wrapper，包含两个：
       >
       > - LambdaQueryWrapper
       > - LambdaUpdateWrapper
       >
       > 分别对应QueryWrapper和UpdateWrapper
       >
       > 其使用方式如下：
       >
       > ```java
       > @Test
       > void testLambdaQueryWrapper() {
       >     // 1.构建条件 WHERE username LIKE "%o%" AND balance >= 1000
       >     QueryWrapper<User> wrapper = new QueryWrapper<>();
       >     wrapper.lambda()
       >         .select(User::getId, User::getUsername, User::getInfo, User::getBalance)
       >         .like(User::getUsername, "o")
       >         .ge(User::getBalance, 1000);
       >     // 2.查询
       >     List<User> users = userMapper.selectList(wrapper);
       >     users.forEach(System.out::println);
       > }
       > ```

  - #### 自定义SQL

    > 在演示UpdateWrapper的案例中，我们在代码中编写了更新的SQL语句：
    >
    > ![img](./assets/1720167488081-71877873-3d0e-47fa-98ed-3a8bae211987.webp)
    >
    > 这种写法在某些企业也是不允许的，因为SQL语句最好都维护在持久层，而不是业务层。就当前案例来说，由于条件是in语句，只能将SQL写在Mapper.xml文件，利用foreach来生成动态SQL。 这实在是太麻烦了。假如查询条件更复杂，动态SQL的编写也会更加复杂。
    >
    > 所以，MybatisPlus提供了自定义SQL功能，可以让我们利用Wrapper生成查询条件，再结合Mapper.xml编写SQL。

    1. ##### 基本用法

       > 以当前案例来说，我们可以这样写：
       >
       > ```java
       > @Test
       > void testCustomWrapper() {
       >     // 1.准备自定义查询条件
       >     List<Long> ids = List.of(1L, 2L, 4L);
       >     QueryWrapper<User> wrapper = new QueryWrapper<User>().in("id", ids);
       > 
       >     // 2.调用mapper的自定义方法，直接传递Wrapper
       >     userMapper.deductBalanceByIds(200, wrapper);
       > }
       > ```
       >
       > 然后在UserMapper中自定义SQL：
       >
       > ```java
       > package com.itheima.mp.mapper;
       > 
       > import com.baomidou.mybatisplus.core.mapper.BaseMapper;
       > import com.itheima.mp.domain.po.User;
       > import org.apache.ibatis.annotations.Param;
       > import org.apache.ibatis.annotations.Update;
       > import org.apache.ibatis.annotations.Param;
       > 
       > public interface UserMapper extends BaseMapper<User> {
       >     @Select("UPDATE user SET balance = balance - #{money} ${ew.customSqlSegment}")
       >     void deductBalanceByIds(@Param("money") int money, @Param("ew") QueryWrapper<User> wrapper);
       > }
       > ```
       >
       > 这样就省去了编写复杂查询条件的烦恼了。

    2. ##### 多表关联

       > 理论上来讲MyBatisPlus是不支持多表查询的，不过我们可以利用Wrapper中自定义条件结合自定义SQL来实现多表查询的效果。 例如，我们要查询出所有收货地址在北京的并且用户id在1、2、4之中的用户 要是自己基于mybatis实现SQL，大概是这样的：
       >
       > ```xml
       > <select id="queryUserByIdAndAddr" resultType="com.itheima.mp.domain.po.User">
       >   SELECT *
       >   FROM user u
       >   INNER JOIN address a ON u.id = a.user_id
       >   WHERE u.id
       >   <foreach collection="ids" separator="," item="id" open="IN (" close=")">
       >     #{id}
       >   </foreach>
       >   AND a.city = #{city}
       > </select>
       > ```
       >
       > 可以看出其中最复杂的就是WHERE条件的编写，如果业务复杂一些，这里的SQL会更变态。
       >
       > 但是基于自定义SQL结合Wrapper的玩法，我们就可以利用Wrapper来构建查询条件，然后手写SELECT及FROM部分，实现多表查询。 
       >
       > 查询条件这样来构建：
       >
       > ```java
       > @Test
       > void testCustomJoinWrapper() {
       >     // 1.准备自定义查询条件
       >     QueryWrapper<User> wrapper = new QueryWrapper<User>()
       >     .in("u.id", List.of(1L, 2L, 4L))
       >     .eq("a.city", "北京");
       > 
       >     // 2.调用mapper的自定义方法
       >     List<User> users = userMapper.queryUserByWrapper(wrapper);
       > 
       >     users.forEach(System.out::println);
       > }
       > ```
       >
       > 然后在UserMapper中自定义方法：
       >
       > ```java
       > @Select("SELECT u.* FROM user u INNER JOIN address a ON u.id = a.user_id ${ew.customSqlSegment}")
       > List<User> queryUserByWrapper(@Param("ew")QueryWrapper<User> wrapper);
       > ```
       >
       > 当然，也可以在UserMapper.xml中写SQL：
       >
       > ```xml
       > <select id="queryUserByIdAndAddr" resultType="com.itheima.mp.domain.po.User">
       >   SELECT * FROM user u INNER JOIN address a ON u.id = a.user_id ${ew.customSqlSegment}
       > </select>
       > ```

  - #### Service接口

    > MybatisPlus不仅提供了BaseMapper，还提供了通用的Service接口及默认实现，封装了一些常用的service模板方法。 通用接口为IService，默认实现为ServiceImpl，其中封装的方法可以分为以下几类：
    >
    > - save：新增
    > - remove：删除
    > - update：更新
    > - get：查询单个结果
    > - list：查询集合结果
    > - count：计数
    > - page：分页查询

    1. ##### CRUD

       > 我们先俩看下基本的CRUD接口。
       >
       > **新增**：
       >
       > ![img](./assets/1720167488306-f544b41e-e6bf-467b-9d98-60859bb4cb78.webp)
       >
       > - save是新增单个元素
       > - saveBatch是批量新增
       > - saveOrUpdate是根据id判断，如果数据存在就更新，不存在则新增
       > - saveOrUpdateBatch是批量的新增或修改
       >
       > **删除：**
       >
       > ![img](./assets/1720167488367-0e9d8c78-0eff-41c2-b110-33464a86e089.webp)
       >
       > - removeById：根据id删除
       > - removeByIds：根据id批量删除
       > - removeByMap：根据Map中的键值对为条件删除
       > - remove(Wrapper<T>)：根据Wrapper条件删除
       > - ~~removeBatchByIds~~：暂不支持
       >
       > **修改：**
       >
       > ![img](./assets/1720167488578-cc191c23-9a9b-44b0-a58c-1b2e35d72fed.webp)
       >
       > - updateById：根据id修改
       > - update(Wrapper<T>)：根据UpdateWrapper修改，Wrapper中包含set和where部分
       > - update(T，Wrapper<T>)：按照T内的数据修改与Wrapper匹配到的数据
       > - updateBatchById：根据id批量修改
       >
       > **Get：**
       >
       > ![img](./assets/1720167488715-b84e77f1-d768-4848-af99-b7162587c364.webp)
       >
       > - getById：根据id查询1条数据
       > - getOne(Wrapper<T>)：根据Wrapper查询1条数据
       > - getBaseMapper：获取Service内的BaseMapper实现，某些时候需要直接调用Mapper内的自定义SQL时可以用这个方法获取到Mapper
       >
       > **List：**
       >
       > ![img](./assets/1720167488985-22ec4370-94e4-4c1a-af5e-dd61a196a2b2.webp)
       >
       > - listByIds：根据id批量查询
       > - list(Wrapper<T>)：根据Wrapper条件查询多条数据
       > - list()：查询所有
       >
       > **Count**：
       >
       > ![img](./assets/1720167489027-b85aadf7-0632-42a1-bfbc-a5811730af18.webp)
       >
       > - count()：统计所有数量
       > - count(Wrapper<T>)：统计符合Wrapper条件的数据数量
       >
       > **getBaseMapper**： 当我们在service中要调用Mapper中自定义SQL时，就必须获取service对应的Mapper，就可以通过这个方法：
       >
       > ![img](./assets/1720167489120-b325f4b0-c0c6-43c9-9dfc-1f3d308f2537.webp)

    2. ##### 基本用法

       > 由于Service中经常需要定义与业务有关的自定义方法，因此我们不能直接使用IService，而是自定义Service接口，然后继承IService以拓展方法。同时，让自定义的Service实现类继承ServiceImpl，这样就不用自己实现IService中的接口了。
       >
       > ![image.png](./assets/1720424532894-2d03b8ed-05fe-4ae0-a55b-c50849f18693.webp)
       >
       > **使用步骤**
       >
       > 首先，定义IUserService，继承IService：
       >
       > ```java
       > package com.itheima.mp.service;
       > 
       > import com.baomidou.mybatisplus.extension.service.IService;
       > import com.itheima.mp.domain.po.User;
       > 
       > public interface IUserService extends IService<User> {
       >     // 拓展自定义方法
       > }
       > ```
       >
       > 然后，编写UserServiceImpl类，继承ServiceImpl，实现IUserService：
       >
       > ```java
       > package com.itheima.mp.service.impl;
       > 
       > import com.baomidou.mybatisplus.extension.service.impl.ServiceImpl;
       > import com.itheima.mp.domain.po.User;
       > import com.itheima.mp.domain.po.service.IUserService;
       > import com.itheima.mp.mapper.UserMapper;
       > import org.springframework.stereotype.Service;
       > 
       > @Service
       > public class UserServiceImpl extends ServiceImpl<UserMapper, User>
       > implements IUserService {}
       > ```
       >
       > 项目结构如下：
       >
       > ![img](./assets/1720167489284-9b14b64d-5328-4e2c-a83a-3272a1caf0ad.webp)
       >
       > 接下来，我们快速实现下面4个接口：
       >
       > | **编号** | **接口**       | **请求方式** | **请求路径** | **请求参数** | **返回值** |
       > | -------- | -------------- | ------------ | ------------ | ------------ | ---------- |
       > | 1        | 新增用户       | POST         | /users       | 用户表单实体 | 无         |
       > | 2        | 删除用户       | DELETE       | /users/{id}  | 用户id       | 无         |
       > | 3        | 根据id查询用户 | GET          | /users/{id}  | 用户id       | 用户VO     |
       > | 4        | 根据id批量查询 | GET          | /users       | 用户id集合   | 用户VO集合 |
       >
       > 首先，我们在项目中引入几个依赖：
       >
       > ```xml
       > <!--swagger-->
       > <dependency>
       >   <groupId>com.github.xiaoymin</groupId>
       >   <artifactId>knife4j-openapi2-spring-boot-starter</artifactId>
       >   <version>4.1.0</version>
       > </dependency>
       > <!--web-->
       > <dependency>
       >   <groupId>org.springframework.boot</groupId>
       >   <artifactId>spring-boot-starter-web</artifactId>
       > </dependency>
       > ```
       >
       > 然后需要配置swagger信息：
       >
       > ```yaml
       > knife4j:
       >   enable: true
       >   openapi:
       >     title: 用户管理接口文档
       >     description: "用户管理接口文档"
       >     email: zhanghuyi@itcast.cn
       >     concat: 虎哥
       >     url: https://www.itcast.cn
       >     version: v1.0.0
       >     group:
       >       default:
       >         group-name: default
       >         api-rule: package
       >         api-rule-resources:
       >           - com.itheima.mp.controller
       > ```
       >
       > 然后，接口需要两个实体：
       >
       > - UserFormDTO：代表新增时的用户表单
       > - UserVO：代表查询的返回结果
       >
       > 首先是UserFormDTO：
       >
       > ```java
       > package com.itheima.mp.domain.dto;
       > 
       > import com.baomidou.mybatisplus.annotation.TableField;
       > import com.baomidou.mybatisplus.extension.handlers.JacksonTypeHandler;
       > import io.swagger.annotations.ApiModel;
       > import io.swagger.annotations.ApiModelProperty;
       > import lombok.Data;
       > 
       > @Data
       > @ApiModel(description = "用户表单实体")
       > public class UserFormDTO {
       > 
       >     @ApiModelProperty("id")
       >     private Long id;
       > 
       >     @ApiModelProperty("用户名")
       >     private String username;
       > 
       >     @ApiModelProperty("密码")
       >     private String password;
       > 
       >     @ApiModelProperty("注册手机号")
       >     private String phone;
       > 
       >     @ApiModelProperty("详细信息，JSON风格")
       >     private String info;
       > 
       >     @ApiModelProperty("账户余额")
       >     private Integer balance;
       > }
       > ```
       >
       > 然后是UserVO：
       >
       > ```java
       > package com.itheima.mp.domain.vo;
       > 
       > import io.swagger.annotations.ApiModel;
       > import io.swagger.annotations.ApiModelProperty;
       > import lombok.Data;
       > 
       > @Data
       > @ApiModel(description = "用户VO实体")
       > public class UserVO {
       > 
       >     @ApiModelProperty("用户id")
       >     private Long id;
       > 
       >     @ApiModelProperty("用户名")
       >     private String username;
       > 
       >     @ApiModelProperty("详细信息")
       >     private String info;
       > 
       >     @ApiModelProperty("使用状态（1正常 2冻结）")
       >     private Integer status;
       > 
       >     @ApiModelProperty("账户余额")
       >     private Integer balance;
       > }
       > ```
       >
       > 最后，按照Restful风格编写Controller接口方法：
       >
       > ```java
       > package com.itheima.mp.controller;
       > 
       > import cn.hutool.core.bean.BeanUtil;
       > import com.itheima.mp.domain.dto.UserFormDTO;
       > import com.itheima.mp.domain.po.User;
       > import com.itheima.mp.domain.vo.UserVO;
       > import com.itheima.mp.service.IUserService;
       > import io.swagger.annotations.Api;
       > import io.swagger.annotations.ApiOperation;
       > import lombok.RequiredArgsConstructor;
       > import org.springframework.web.bind.annotation.*;
       > 
       > import java.util.List;
       > 
       > @Api(tags = "用户管理接口")
       > @RequiredArgsConstructor
       > @RestController
       > @RequestMapping("/users")
       > public class UserController {
       >     
       >     /* 注意：1.
       >         不推荐使用@Autowired注解的方式，推荐使用构造函数的方式来完成注入，但是写起来
       >         太麻烦了，所以使用了@RequiredArgsConstructor注解
       >         
       >            public UserController(IUserService userService){
       >             this.userService=userService;
       >         }
       > 
       > 
       >         注意2. private final IUserService userService;
       >         加上final 是配合@RequiredArgsConstructor注解的使用，
       >          在类加载的时候必须完成注入，没有加final的变量不完成注入。
       >     */
       > 
       >     
       >   //  @Autowired
       >     private final IUserService userService;
       > 
       >     @PostMapping
       >     @ApiOperation("新增用户")
       >     public void saveUser(@RequestBody UserFormDTO userFormDTO){
       >         // 1.转换DTO为PO
       >         User user = BeanUtil.copyProperties(userFormDTO, User.class);
       >         // 2.新增
       >         userService.save(user);
       >     }
       > 
       >     @DeleteMapping("/{id}")
       >     @ApiOperation("删除用户")
       >     public void removeUserById(@PathVariable("id") Long userId){
       >         userService.removeById(userId);
       >     }
       > 
       >     @GetMapping("/{id}")
       >     @ApiOperation("根据id查询用户")
       >     public UserVO queryUserById(@PathVariable("id") Long userId){
       >         // 1.查询用户
       >         User user = userService.getById(userId);
       >         // 2.处理vo
       >         return BeanUtil.copyProperties(user, UserVO.class);
       >     }
       > 
       >     @GetMapping
       >     @ApiOperation("根据id集合查询用户")
       >     public List<UserVO> queryUserByIds(@RequestParam("ids") List<Long> ids){
       >         // 1.查询用户
       >         List<User> users = userService.listByIds(ids);
       >         // 2.处理vo
       >         return BeanUtil.copyToList(users, UserVO.class);
       >     }
       > }
       > ```
       >
       > 可以看到上述接口都直接在controller即可实现，无需编写任何service代码，非常方便。
       >
       > 不过，一些带有业务逻辑的接口则需要在service中自定义实现了。例如下面的需求：
       >
       > - 根据id扣减用户余额
       >
       > 这看起来是个简单修改功能，只要修改用户余额即可。但这个业务包含一些业务逻辑处理：
       >
       > - 判断用户状态是否正常
       > - 判断用户余额是否充足
       >
       > 这些业务逻辑都要在service层来做，另外更新余额需要自定义SQL，要在mapper中来实现。因此，我们除了要编写controller以外，具体的业务还要在service和mapper中编写。
       >
       > 首先在UserController中定义一个方法：
       >
       > ```java
       > @PutMapping("{id}/deduction/{money}")
       > @ApiOperation("扣减用户余额")
       > public void deductBalance(@PathVariable("id") Long id, @PathVariable("money")Integer money){
       >     userService.deductBalance(id, money);
       > }
       > ```
       >
       > 然后是UserService接口：
       >
       > ```java
       > package com.itheima.mp.service;
       > 
       > import com.baomidou.mybatisplus.extension.service.IService;
       > import com.itheima.mp.domain.po.User;
       > 
       > public interface IUserService extends IService<User> {
       >     void deductBalance(Long id, Integer money);
       > }
       > ```
       >
       > 最后是UserServiceImpl实现类：
       >
       > ```java
       > package com.itheima.mp.service.impl;
       > 
       > import com.baomidou.mybatisplus.extension.service.impl.ServiceImpl;
       > import com.itheima.mp.domain.po.User;
       > import com.itheima.mp.mapper.UserMapper;
       > import com.itheima.mp.service.IUserService;
       > import org.springframework.stereotype.Service;
       > 
       > @Service
       > public class UserServiceImpl extends ServiceImpl<UserMapper, User> implements IUserService {
       >     @Override
       >     public void deductBalance(Long id, Integer money) {
       >         // 1.查询用户
       >         User user = getById(id);
       >         // 2.判断用户状态
       >         if (user == null || user.getStatus() == 2) {
       >             throw new RuntimeException("用户状态异常");
       >         }
       >         // 3.判断用户余额
       >         if (user.getBalance() < money) {
       >             throw new RuntimeException("用户余额不足");
       >         }
       >         // 4.扣减余额
       >         baseMapper.deductMoneyById(id, money);
       >     }
       > }
       > ```
       >
       > 最后是mapper：
       >
       > ```java
       > @Update("UPDATE user SET balance = balance - #{money} WHERE id = #{id}")
       > void deductMoneyById(@Param("id") Long id, @Param("money") Integer money);
       > ```

    3. ##### Lambda

       > IService中还提供了Lambda功能来简化我们的复杂查询及更新功能。我们通过两个案例来学习一下。
       >
       > 案例一：实现一个根据复杂条件查询用户的接口，查询条件如下：
       >
       > - name：用户名关键字，可以为空
       > - status：用户状态，可以为空
       > - minBalance：最小余额，可以为空
       > - maxBalance：最大余额，可以为空
       >
       > ![image.png](./assets/1720438826290-a6d04c05-caa0-4757-88de-393662353f88.webp)
       >
       > 可以理解成一个用户的后台管理界面，管理员可以自己选择条件来筛选用户，因此上述条件不一定存在，需要做判断。
       >
       > 我们首先需要定义一个查询条件实体，UserQuery实体：
       >
       > ```java
       > package com.itheima.mp.domain.query;
       > 
       > import io.swagger.annotations.ApiModel;
       > import io.swagger.annotations.ApiModelProperty;
       > import lombok.Data;
       > 
       > @Data
       > @ApiModel(description = "用户查询条件实体")
       > public class UserQuery {
       >     @ApiModelProperty("用户名关键字")
       >     private String name;
       >     @ApiModelProperty("用户状态：1-正常，2-冻结")
       >     private Integer status;
       >     @ApiModelProperty("余额最小值")
       >     private Integer minBalance;
       >     @ApiModelProperty("余额最大值")
       >     private Integer maxBalance;
       > }
       > ```
       >
       > 接下来我们在UserController中定义一个controller方法：
       >
       > ```java
       > @GetMapping("/list")
       > @ApiOperation("根据id集合查询用户")
       > public List<UserVO> queryUsers(UserQuery query){
       >     // 1.组织条件
       >     String username = query.getName();
       >     Integer status = query.getStatus();
       >     Integer minBalance = query.getMinBalance();
       >     Integer maxBalance = query.getMaxBalance();
       >     LambdaQueryWrapper<User> wrapper = new QueryWrapper<User>().lambda()
       >     .like(username != null, User::getUsername, username)
       >     .eq(status != null, User::getStatus, status)
       >     .ge(minBalance != null, User::getBalance, minBalance)
       >     .le(maxBalance != null, User::getBalance, maxBalance);
       > // 2.查询用户
       >     List<User> users = userService.list(wrapper);
       >     // 3.处理vo
       >     return BeanUtil.copyToList(users, UserVO.class);
       > }
       > ```
       >
       > 在组织查询条件的时候，我们加入了 username != null 这样的参数，意思就是当条件成立时才会添加这个查询条件，类似Mybatis的mapper.xml文件中的<if>标签。这样就实现了动态查询条件效果了。
       >
       > 不过，上述条件构建的代码太麻烦了。 因此Service中对LambdaQueryWrapper和LambdaUpdateWrapper的用法进一步做了简化。我们无需自己通过new的方式来创建Wrapper，而是直接调用lambdaQuery和lambdaUpdate方法：
       >
       > 基于Lambda查询：
       >
       > ```java
       > @GetMapping("/list")
       > @ApiOperation("根据id集合查询用户")
       > public List<UserVO> queryUsers(UserQuery query){
       >     // 1.组织条件
       >     String username = query.getName();
       >     Integer status = query.getStatus();
       >     Integer minBalance = query.getMinBalance();
       >     Integer maxBalance = query.getMaxBalance();
       >     // 2.查询用户
       >     List<User> users = userService.lambdaQuery()
       >     .like(username != null, User::getUsername, username)
       >     .eq(status != null, User::getStatus, status)
       >     .ge(minBalance != null, User::getBalance, minBalance)
       >     .le(maxBalance != null, User::getBalance, maxBalance)
       >     .list();
       >     // 3.处理vo
       >     return BeanUtil.copyToList(users, UserVO.class);
       > }
       > ```
       >
       > 可以发现lambdaQuery方法中除了可以构建条件，还需要在链式编程的最后添加一个list()，这是在告诉MP我们的调用结果需要是一个list集合。这里不仅可以用list()，可选的方法有：
       >
       > - `.one()`：最多1个结果
       > - `.list()`：返回集合结果
       > - `.count()`：返回计数结果
       >
       > MybatisPlus会根据链式编程的最后一个方法来判断最终的返回结果。
       >
       > 与lambdaQuery方法类似，IService中的lambdaUpdate方法可以非常方便的实现复杂更新业务。
       >
       > 例如下面的需求：
       >
       > 需求：改造根据id修改用户余额的接口，要求如下
       >
       > - 如果扣减后余额为0，则将用户status修改为冻结状态（2）
       >
       > 也就是说我们在扣减用户余额时，需要对用户剩余余额做出判断，如果发现剩余余额为0，则应该将status修改为2，这就是说update语句的set部分是动态的。
       >
       > 实现如下：
       >
       > ```java
       > @Override
       > @Transactional
       > public void deductBalance(Long id, Integer money) {
       >     // 1.查询用户
       >     User user = getById(id);
       >     // 2.校验用户状态
       >     if (user == null || user.getStatus() == 2) {
       >         throw new RuntimeException("用户状态异常！");
       >     }
       >     // 3.校验余额是否充足
       >     if (user.getBalance() < money) {
       >         throw new RuntimeException("用户余额不足！");
       >     }
       >     // 4.扣减余额 update tb_user set balance = balance - ?
       >     int remainBalance = user.getBalance() - money;
       >         lambdaUpdate()
       >         .set(User::getBalance, remainBalance) // 更新余额
       >         .set(remainBalance == 0, User::getStatus, 2) // 动态判断，是否更新status
       >         .eq(User::getId, id)
       >         .eq(User::getBalance, user.getBalance()) // 乐观锁
       >         .update();
       > }
       > ```

    4. ##### 批量新增

       > IService中的批量新增功能使用起来非常方便，但有一点注意事项，我们先来测试一下。 首先我们测试逐条插入数据：
       >
       > ```java
       > @Test
       > void testSaveOneByOne() {
       >     long b = System.currentTimeMillis();
       >     for (int i = 1; i <= 100000; i++) {
       >         userService.save(buildUser(i));
       >     }
       >     long e = System.currentTimeMillis();
       >     System.out.println("耗时：" + (e - b));
       > }
       > 
       > private User buildUser(int i) {
       >     User user = new User();
       >     user.setUsername("user_" + i);
       >     user.setPassword("123");
       >     user.setPhone("" + (18688190000L + i));
       >     user.setBalance(2000);
       >     user.setInfo("{\"age\": 24, \"intro\": \"英文老师\", \"gender\": \"female\"}");
       >     user.setCreateTime(LocalDateTime.now());
       >     user.setUpdateTime(user.getCreateTime());
       >     return user;
       > }
       > ```
       >
       > 执行结果如下：
       >
       > ![img](./assets/1720167489397-891a6bc4-b753-4384-8de3-64ec7cc2436f.webp)
       >
       > 可以看到速度非常慢。
       >
       > 然后再试试MybatisPlus的批处理：
       >
       > ```java
       > @Test
       > void testSaveBatch() {
       >     // 准备10万条数据
       >     List<User> list = new ArrayList<>(1000);
       >     long b = System.currentTimeMillis();
       >     for (int i = 1; i <= 100000; i++) {
       >         list.add(buildUser(i));
       >         // 每1000条批量插入一次
       >         if (i % 1000 == 0) {
       >             userService.saveBatch(list);
       >             list.clear();
       >         }
       >     }
       >     long e = System.currentTimeMillis();
       >     System.out.println("耗时：" + (e - b));
       > }
       > ```
       >
       > 执行最终耗时如下：
       >
       > ![img](./assets/1720167489637-1f87ce62-d9e2-48c9-a1e8-e02ebc900a54.webp)
       >
       > 可以看到使用了批处理以后，比逐条新增效率提高了10倍左右，性能还是不错的。
       >
       > 不过，我们简单查看一下MybatisPlus源码：
       >
       > ```java
       > @Transactional(rollbackFor = Exception.class)
       > @Override
       > public boolean saveBatch(Collection<T> entityList, int batchSize) {
       >     String sqlStatement = getSqlStatement(SqlMethod.INSERT_ONE);
       >     return executeBatch(entityList, batchSize, (sqlSession, entity) -> sqlSession.insert(sqlStatement, entity));
       > }
       > // ...SqlHelper
       > public static <E> boolean executeBatch(Class<?> entityClass, Log log, Collection<E> list, int batchSize, BiConsumer<SqlSession, E> consumer) {
       >     Assert.isFalse(batchSize < 1, "batchSize must not be less than one");
       >     return !CollectionUtils.isEmpty(list) && executeBatch(entityClass, log, sqlSession -> {
       >         int size = list.size();
       >         int idxLimit = Math.min(batchSize, size);
       >         int i = 1;
       >         for (E element : list) {
       >             consumer.accept(sqlSession, element);
       >             if (i == idxLimit) {
       >                 sqlSession.flushStatements();
       >                 idxLimit = Math.min(idxLimit + batchSize, size);
       >             }
       >             i++;
       >         }
       >     });
       > }
       > ```
       >
       > 可以发现其实MybatisPlus的批处理是基于PrepareStatement的预编译模式，然后批量提交，最终在数据库执行时还是会有多条insert语句，逐条插入数据。SQL类似这样：
       >
       > ```java
       > Preparing: INSERT INTO user ( username, password, phone, info, balance, create_time, update_time ) VALUES ( ?, ?, ?, ?, ?, ?, ? )
       > Parameters: user_1, 123, 18688190001, "", 2000, 2023-07-01, 2023-07-01
       > Parameters: user_2, 123, 18688190002, "", 2000, 2023-07-01, 2023-07-01
       > Parameters: user_3, 123, 18688190003, "", 2000, 2023-07-01, 2023-07-01
       > ```
       >
       > 而如果想要得到最佳性能，最好是将多条SQL合并为一条，像这样：
       >
       > ```java
       > INSERT INTO user ( username, password, phone, info, balance, create_time, update_time )
       > VALUES 
       > (user_1, 123, 18688190001, "", 2000, 2023-07-01, 2023-07-01),
       > (user_2, 123, 18688190002, "", 2000, 2023-07-01, 2023-07-01),
       > (user_3, 123, 18688190003, "", 2000, 2023-07-01, 2023-07-01),
       > (user_4, 123, 18688190004, "", 2000, 2023-07-01, 2023-07-01);
       > ```
       >
       > 该怎么做呢？ 
       >
       > MySQL的客户端连接参数中有这样的一个参数：rewriteBatchedStatements。顾名思义，就是重写批处理的statement语句。参考文档：
       >
       > **https://dev.mysql.com/doc/connector-j/8.0/en/connector-j-connp-props-performance-extensions.html#cj-conn-prop_rewriteBatchedStatements**
       >
       >  这个参数的默认值是false，我们需要修改连接参数，将其配置为true
       >
       > 修改项目中的application.yml文件，在jdbc的url后面添加参数&rewriteBatchedStatements=true
       >
       > ```yaml
       > spring:
       >   datasource:
       >     url: jdbc:mysql://127.0.0.1:3306/mp?useUnicode=true&characterEncoding=UTF-8&autoReconnect=true&serverTimezone=Asia/Shanghai&rewriteBatchedStatements=true
       >     driver-class-name: com.mysql.cj.jdbc.Driver
       >     username: root
       >     password: MySQL123
       > ```
       >
       > 再次测试插入10万条数据，可以发现速度有非常明显的提升：
       >
       > ![img](./assets/1720167489854-721d7531-7425-4d1e-9a80-243b85386ab4.webp)
       >
       > 在ClientPreparedStatement的executeBatchInternal中，有判断rewriteBatchedStatements值是否为true并重写SQL的功能：
       >
       > 最终，SQL被重写了：
       >
       > ![img](./assets/1720167489810-c23c0929-eb55-4804-8206-1252390390a4.webp)

