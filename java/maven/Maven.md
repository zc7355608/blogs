# Maven

> Maven是一个Java项目管理和构建工具，它可以**定义项目结构、项目依赖**，并**使用统一的方式进行自动化构建**，是Java项目不可缺少的工具。

- #### Maven介绍

  > 在了解Maven之前，我们先来看看一个Java项目需要的东西。首先，我们需要确定引入哪些依赖包。例如，如果我们需要用到[commons logging](https://commons.apache.org/proper/commons-logging/)，我们就必须把commons logging的jar包放入classpath。如果我们还需要[log4j](https://logging.apache.org/log4j/)，就需要把log4j相关的jar包都放到classpath中。这些就是依赖包的管理。
  >
  > 其次，我们要确定项目的目录结构。例如，`src`目录存放Java源码，`resources`目录存放配置文件，`bin`目录存放编译生成的`.class`文件。
  >
  > 此外，我们还需要配置环境，例如JDK的版本，编译打包的流程，当前代码的版本号。
  >
  > 最后，除了使用Eclipse这样的IDE进行编译外，我们还必须能通过命令行工具进行编译，才能够让项目在一个独立的服务器上编译、测试、部署。
  >
  > 这些工作难度不大，但是非常琐碎且耗时。如果每一个项目都自己搞一套配置，肯定会一团糟。我们需要的是一个标准化的Java项目管理和构建工具。
  >
  > Maven就是是专门为Java项目打造的管理和构建工具，它的主要功能有：
  >
  > - 提供了一套标准化的项目结构；
  > - 提供了一套标准化的构建流程（编译，测试，打包，发布……）；
  > - 提供了一套依赖管理机制。
  >

  - ##### Maven项目结构：

    > 一个使用Maven管理的普通的Java项目，它的目录结构默认如下：
    >
    > ```tex
    > a-maven-project
    >     ├── pom.xml
    >     ├── src
    >     │   ├── main
    >     │   │   ├── java
    >     │   │   └── resources
    >     │   └── test
    >     │       ├── java
    >     │       └── resources
    >     └── target
    > ```
    >
    > 项目的根目录`a-maven-project`是项目名，它有一个项目描述文件`pom.xml`，存放Java源码的目录是`src/main/java`，存放资源文件的目录是`src/main/resources`，存放测试源码的目录是`src/test/java`，存放测试资源的目录是`src/test/resources`，最后，所有编译、打包生成的文件都放在`target`目录里。这些就是一个Maven项目的标准目录结构。
    >
    > 所有的目录结构都是约定好的标准结构，我们千万不要随意修改目录结构。使用标准结构不需要做任何配置，Maven就可以正常使用。
    >
    > 我们再来看最关键的一个项目描述文件`pom.xml`，它的内容长得像下面：
    >
    > ```xml
    > <project ...>
    > 	<modelVersion>4.0.0</modelVersion>
    > 	<groupId>com.itranswarp.learnjava</groupId>
    > 	<artifactId>hello</artifactId>
    > 	<version>1.0</version>
    > 	<packaging>jar</packaging>
    > 	<properties>
    >         <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    > 		<maven.compiler.release>17</maven.compiler.release>
    > 	</properties>
    > 	<dependencies>
    >         <dependency>
    >             <groupId>org.slf4j</groupId>
    >             <artifactId>slf4j-simple</artifactId>
    >             <version>2.0.16</version>
    >         </dependency>
    > 	</dependencies>
    > </project>
    > ```
    >
    > 其中，`groupId`类似于Java的包名，通常是公司或组织名称，`artifactId`类似于Java的类名，通常是项目名称，再加上`version`，一个Maven工程就是由`groupId`，`artifactId`和`version`作为唯一标识。
    >
    > 我们在引用其他第三方库的时候，也是通过这3个变量确定。例如，依赖`org.slfj4:slf4j-simple:2.0.16`：
    >
    > ```xml
    > <dependency>
    >      <groupId>org.slf4j</groupId>
    >      <artifactId>slf4j-simple</artifactId>
    >      <version>2.0.16</version>
    > </dependency>
    > ```
    >
    > 使用`<dependency>`声明一个依赖后，Maven就会自动下载这个依赖包并把它放到classpath中。
    >
    > 另外，注意到`<properties>`定义了一些属性，常用的属性有：
    >
    > - `project.build.sourceEncoding`：表示项目源码的字符编码，通常应设定为`UTF-8`；
    > - `maven.compiler.release`：表示使用的JDK版本，例如`21`；
    > - `maven.compiler.source`：表示Java编译器读取的源码版本；
    > - `maven.compiler.target`：表示Java编译器编译的Class版本。
    >
    > 从Java 9开始，推荐使用`maven.compiler.release`属性，保证编译时输入的源码和编译输出版本一致。如果源码和输出版本不同，则应该分别设置`maven.compiler.source`和`maven.compiler.target`。
    >
    > 通过`<properties>`定义的属性，就可以固定JDK版本，防止同一个项目的不同的开发者各自使用不同版本的JDK。

  - ##### 安装Maven：

    > 要安装Maven，可以从[Maven官网](https://maven.apache.org/)下载最新的Maven 3.9.x，然后在本地解压，设置几个环境变量：
    >
    > ```bash
    > M2_HOME=/path/to/maven-3.9.x
    > PATH=$PATH:$M2_HOME/bin
    > ```
    >
    > Windows可以把`%M2_HOME%\bin`添加到系统Path变量中。
    >
    > 然后，打开命令行窗口，输入`mvn -v`，应该看到Maven的版本信息：
    >
    > ```bash
    > ┌─────────────────────────────────────────────────────────┐
    > │Windows PowerShell                                 - □ x │
    > ├─────────────────────────────────────────────────────────┤
    > │Windows PowerShell                                       │
    > │Copyright (C) Microsoft Corporation. All rights reserved.│
    > │                                                         │
    > │PS C:\Users\liaoxuefeng> mvn -version                    │
    > │Apache Maven 3.9.x ...                                   │
    > │Maven home: C:\Users\liaoxuefeng\maven                   │
    > │Java version: ...                                        │
    > │...                                                      │
    > │                                                         │
    > └─────────────────────────────────────────────────────────┘
    > ```
    >
    > 如果提示命令未找到，说明系统PATH路径有误，需要修复后再运行。

    > Maven是一个Java项目的管理和构建工具：
    >
    > - Maven使用`pom.xml`定义项目内容，并使用预设的目录结构；
    > - 在Maven中声明一个依赖项可以自动下载并导入classpath；
    > - Maven使用`groupId`，`artifactId`和`version`唯一定位一个依赖。

- #### 依赖管理

  > 如果我们的项目依赖第三方的jar包，例如commons logging，那么问题来了：commons logging发布的jar包在哪下载？
  >
  > 如果我们还希望依赖log4j，那么使用log4j需要哪些jar包？
  >
  > 类似的依赖还包括：JUnit，JavaMail，MySQL驱动等等，一个可行的方法是通过搜索引擎搜索到项目的官网，然后手动下载zip包，解压，放入classpath。但是，这个过程非常繁琐。
  >
  > Maven解决了依赖管理问题。例如，我们的项目依赖`abc`这个jar包，而`abc`又依赖`xyz`这个jar包：
  >
  > ```tex
  > ┌──────────────┐
  > │Sample Project│
  > └──────────────┘
  >         │
  >         ▼
  > ┌──────────────┐
  > │     abc      │
  > └──────────────┘
  >         │
  >         ▼
  > ┌──────────────┐
  > │     xyz      │
  > └──────────────┘
  > ```
  >
  > 当我们声明了`abc`的依赖时，Maven自动把`abc`和`xyz`都加入了我们的项目依赖，不需要我们自己去研究`abc`是否需要依赖`xyz`。
  >
  > 因此，Maven的第一个作用就是解决依赖管理。我们声明了自己的项目需要`abc`，Maven会自动导入`abc`的jar包，再判断出`abc`需要`xyz`，又会自动导入`xyz`的jar包，这样，最终我们的项目会依赖`abc`和`xyz`两个jar包。
  >
  > 我们来看一个复杂依赖示例：
  >
  > ```xml
  > <dependency>
  >      <groupId>org.springframework.boot</groupId>
  >      <artifactId>spring-boot-starter-web</artifactId>
  >      <version>1.4.2.RELEASE</version>
  > </dependency>
  > ```
  >
  > 当我们声明一个`spring-boot-starter-web`依赖时，Maven会自动解析并判断最终需要大概二三十个其他依赖：
  >
  > ```tex
  >spring-boot-starter-web
  >   spring-boot-starter
  >     spring-boot
  >       sprint-boot-autoconfigure
  >        spring-boot-starter-logging
  >          logback-classic
  >    	    logback-core
  >    	    slf4j-api
  >    	  jcl-over-slf4j
  >    	    slf4j-api
  >    	  jul-to-slf4j
  >    	    slf4j-api
  >    	  log4j-over-slf4j
  >    	    slf4j-api
  >        spring-core
  >        snakeyaml
  >      spring-boot-starter-tomcat
  >        tomcat-embed-core
  >       tomcat-embed-el
  >        tomcat-embed-websocket
  >    	  tomcat-embed-core
  >      jackson-databind
  >      ...
  >   ```
  >   
  > 如果我们自己去手动管理这些依赖是非常费时费力的，而且出错的概率很大。

  - ##### 依赖关系：

    > Maven定义了几种依赖关系，分别是`compile`、`test`、`runtime`和`provided`：
    >
    > | scope    | 说明                                          | 示例            |
    > | -------- | --------------------------------------------- | --------------- |
    > | compile  | 编译时需要用到该jar包（默认）                 | commons-logging |
    > | test     | 编译Test时需要用到该jar包                     | junit           |
    > | runtime  | 编译时不需要，但运行时需要用到                | mysql           |
    > | provided | 编译时需要用到，但运行时由JDK或某个服务器提供 | servlet-api     |
    >
    > 其中，默认的`compile`是最常用的，Maven会把这种类型的依赖直接放入classpath。
    >
    > `test`依赖表示仅在测试时使用，正常运行时并不需要。最常用的`test`依赖就是JUnit：
    >
    > ```xml
    > <dependency>
    >      <groupId>org.junit.jupiter</groupId>
    >      <artifactId>junit-jupiter-api</artifactId>
    >      <version>5.3.2</version>
    >      <scope>test</scope>
    > </dependency>
    > ```
    >
    > `runtime`依赖表示编译时不需要，但运行时需要。最典型的`runtime`依赖是JDBC驱动，例如MySQL驱动：
    >
    > ```xml
    > <dependency>
    >      <groupId>mysql</groupId>
    >      <artifactId>mysql-connector-java</artifactId>
    >      <version>5.1.48</version>
    >      <scope>runtime</scope>
    > </dependency>
    > ```
    >
    > `provided`依赖表示编译时需要，但运行时不需要。最典型的`provided`依赖是Servlet API，编译的时候需要，但是运行时，Servlet服务器内置了相关的jar，所以运行期不需要：
    >
    > ```xml
    > <dependency>
    >      <groupId>jakarta.servlet</groupId>
    >      <artifactId>jakarta.servlet-api</artifactId>
    >      <version>4.0.0</version>
    >      <scope>provided</scope>
    > </dependency>
    > ```
    >
    > 最后一个问题是，Maven如何知道从何处下载所需的依赖？也就是相关的jar包？答案是Maven维护了一个中央仓库（[repo1.maven.org](https://repo1.maven.org/)），所有第三方库将自身的jar以及相关信息上传至中央仓库，Maven就可以从中央仓库把所需依赖下载到本地。
    >
    > Maven并不会每次都从中央仓库下载jar包。一个jar包一旦被下载过，就会被Maven自动缓存在本地目录（用户主目录的`.m2`目录），所以，除了第一次编译时因为下载需要时间会比较慢，后续过程因为有本地缓存，并不会重复下载相同的jar包。

    > 本地缓存又被称为Maven本地仓库。如果开发者不希望大量的依赖包被缓存到本地的`.m2`目录中，可以通过Maven的核心配置文件`maven安装目录/conf/settings.xml`来修改本地仓库的位置：
    >
    > ```xml
    > <localRepository>D:\dev\repository</localRepository>
    > ```

  - ##### 依赖的唯一ID：

    > 对于某个依赖，Maven只需要3个变量即可唯一确定某个jar包：
    >
    > - groupId：属于组织的名称，类似Java的包名；
    > - artifactId：该jar包自身的名称，类似Java的类名；
    > - version：该jar包的版本。
    >
    > 通过上述3个变量，即可唯一确定某个jar包。Maven通过对jar包进行PGP签名确保任何一个jar包一经发布就无法修改。**修改已发布jar包的唯一方法是发布一个新版本**。因此，某个jar包一旦被Maven下载过，即可永久地安全缓存在本地。
    >

    > 后续我们在表示Maven依赖时，使用简写形式 `groupId:artifactId:version`，例如：`org.slf4j:slf4j-api:2.0.4`

    **注意**：在 Maven 中，以 `-SNAPSHOT` 结尾的依赖（如 `1.0.0-SNAPSHOT`）具有特殊的更新机制，其行为与正式版本（如 `1.0.0`）不同。以下是关键点：

    - **本地仓库存储**：`-SNAPSHOT` 依赖会保留**多个版本**（带时间戳），但 Maven 仅使用最新的缓存版本。`-SNAPSHOT` 依赖会被缓存到Maven本地仓库，但**文件名会附加时间戳和构建号**，例如：`mylib-1.0.0-SNAPSHOT-20230701.123456-1.jar`。

      > **清理旧版本**：本地仓库不会自动清理旧的 `-SNAPSHOT` 文件，需手动删除或使用 `mvn dependency:purge-local-repository`。

    - **远程仓库检查**：Maven 默认会**定期检查远程仓库**是否有更新的 `-SNAPSHOT` 版本（即使本地仓库已存在该依赖）。默认策略是**每天检查一次**，若远程有更新，Maven 会下载最新的 `-SNAPSHOT` 到本地仓库，覆盖旧版本。可通过配置或命令行参数来**强制更新**。

      - 命令行：在命令中添加 `-U` 或 `--update-snapshots` 参数，强制检查远程仓库并下载最新 `-SNAPSHOT`：

        ```bash
        mvn clean install -U
        ```

      - 配置：在 `settings.xml` 中修改 `-SNAPSHOT` 的更新策略（默认 `daily`）：

        ```xml
        <settings>
          <profiles>
            <profile>
              <id>custom-snapshot-policy</id>
              <repositories>
                <repository>
                  <id>my-repo</id>
                  <url>http://repo.example.com</url>
                  <snapshots>
                    <!-- always: 每次构建检查 | never: 从不检查 | interval:X: 每X分钟检查 -->
                    <updatePolicy>always</updatePolicy>
                  </snapshots>
                </repository>
              </repositories>
            </profile>
          </profiles>
          <activeProfiles>
            <activeProfile>custom-snapshot-policy</activeProfile>
          </activeProfiles>
        </settings>
        ```

    - **禁用 `-SNAPSHOT` 更新**：在 `settings.xml` 中设置 `updatePolicy` 为 `never`

      ```xml
      <updatePolicy>never</updatePolicy>
      ```

  - ##### Maven镜像：

    > 除了可以从Maven的中央仓库下载外，还可以从Maven的镜像仓库下载。如果访问Maven的中央仓库非常慢，我们可以选择一个速度较快的Maven的镜像仓库。Maven镜像仓库定期从中央仓库同步：
    >
    > ```tex
    >            slow    ┌───────────────────┐
    >     ┌─────────────▶│Maven Central Repo.│
    >     │              └───────────────────┘
    >     │                        │
    >     │                        │sync
    >     │                        ▼
    > ┌───────┐  fast    ┌───────────────────┐
    > │ User  │─────────▶│Maven Mirror Repo. │
    > └───────┘          └───────────────────┘
    > ```
    >
    > 在 `settings.xml`（通常位于 `~/.m2/settings.xml` 或 `%M2_HOME%/conf/settings.xml`）中进行配置（全局配置）：
    >
    > **注意**：Maven的相关配置也可以在项目的`pom.xml`中进行配置，这样配置的是局部的（项目范围）。而在`settings.xml`中的是全局配置。

    1. ###### 单个镜像配置（推荐阿里云）

       > ```xml
       > <settings>
       >      <mirrors>
       >        <!-- 阿里云镜像（国内推荐） -->
       >        <mirror>
       >          <id>aliyun</id>
       >          <name>阿里云公共仓库</name>
       >          <url>https://maven.aliyun.com/repository/public</url>
       >          <mirrorOf>central,jcenter,!repo1,!sonatype,!jitpack</mirrorOf>
       >        </mirror>
       >      </mirrors>
       > </settings>
       > ```
       >
       > **关键参数说明**：
       >
       > - `mirrorOf` 控制镜像覆盖范围：
       >   - `central`：覆盖 Maven 中央仓库
       >   - `*`：覆盖所有仓库（慎用，可能影响私有仓库）
       >   - `!repo1`：排除特定仓库（如 `repo1`）

    2. ###### 多镜像源配置（按需切换）

       > 如果需要多个[镜像源](https://so.csdn.net/so/search?q=镜像源&spm=1001.2101.3001.7020)（如阿里云 + 华为云），可通过 `profile` 实现灵活切换：
       >
       > ```xml
       > <settings>
       >     <profiles>
       >         <!-- 阿里云配置 -->
       >         <profile>
       >             <id>aliyun</id>
       >             <repositories>
       >                 <repository>
       >                     <id>aliyun-public</id>
       >                     <url>https://maven.aliyun.com/repository/public</url>
       >                     <releases><enabled>true</enabled></releases>
       >                     <snapshots><enabled>false</enabled></snapshots>
       >                 </repository>
       >             </repositories>
       >         </profile>
       > 
       >         <!-- 华为云配置 -->
       >         <profile>
       >             <id>huaweicloud</id>
       >             <repositories>
       >                 <repository>
       >                     <id>huaweicloud-public</id>
       >                     <url>https://repo.huaweicloud.com/repository/maven/</url>
       >                     <releases><enabled>true</enabled></releases>
       >                     <snapshots><enabled>false</enabled></snapshots>
       >                 </repository>
       >             </repositories>
       >         </profile>
       > 	</profiles>
       > 
       >     <!-- 激活阿里云为默认镜像 -->
       >     <activeProfiles>
       >     	<activeProfile>aliyun</activeProfile>
       >     </activeProfiles>
       > </settings>
       > ```
       >
       > 切换镜像源：
       >
       > ```bash
       > # 使用华为云镜像
       > mvn clean install -P huaweicloud
       > # 使用阿里云镜像（默认）
       > mvn clean install
       > ```

    3. ###### 私有仓库与镜像共存

       > 如果同时使用私有仓库（如 Nexus），需排除镜像覆盖：
       >
       > ```xml
       > <mirror>
       >      <id>aliyunmaven</id>
       >      <url>https://maven.aliyun.com/repository/public</url>
       >      <mirrorOf>central,jcenter,!internal-repo</mirrorOf>
       > </mirror>
       > ```
       >
       > 在 `pom.xml` 中定义私有仓库：
       >
       > ```xml
       > <repositories>
       >      <repository>
       >        <id>internal-repo</id>
       >        <url>http://nexus.example.com/repository/maven-public/</url>
       >      </repository>
       > </repositories>
       > ```

    > 配置镜像仓库后，Maven的下载速度就会非常快。

  - ##### 搜索第三方组件：

    > 最后一个问题：如果我们要引用一个第三方组件，比如`okhttp`，如何确切地获得它的`groupId`、`artifactId`和`version`？方法是通过[search.maven.org](https://search.maven.org/)搜索关键字，找到对应的组件后，直接复制：
    >
    > ![maven-info](https://liaoxuefeng.com/books/java/maven/dependency/maven-info.png)
    >

  - ##### 命令行编译：

    > 在命令中，进入到`pom.xml`所在目录，输入以下命令：
    >
    > ```bash
    > $ mvn clean package
    > ```
    >
    > 如果一切顺利，即可在`target`目录下获得编译后自动打包的`.jar`文件。

  - ##### 在IDE中使用Maven：

    > 几乎所有的IDE都内置了对Maven的支持。在Eclipse/IDEA中进行项目开发时，除了要配置项目的JDK路径，还有就是配置正确版本的Maven。只要JDK和Maven配置好之后，就可以正确下载依赖并运行项目了。
    >

- #### 构建流程

  > Maven不但有标准化的项目结构，而且还有一套标准化的构建流程，可以自动化实现编译，打包，发布，等等。

  - ##### Lifecycle和Phase：

    > 使用Maven时，我们首先要了解什么是Maven的生命周期（lifecycle）。
    >
    > Maven的生命周期由一系列阶段（phase）构成，以内置的生命周期`default`为例，它包含以下阶段：
    >
    > - validate
    > - initialize
    > - generate-sources
    > - process-sources
    > - generate-resources
    > - process-resources
    > - compile
    > - process-classes
    > - generate-test-sources
    > - process-test-sources
    > - generate-test-resources
    > - process-test-resources
    > - test-compile
    > - process-test-classes
    > - test
    > - prepare-package
    > - package
    > - pre-integration-test
    > - integration-test
    > - post-integration-test
    > - verify
    > - install
    > - deploy
    >
    > Maven的构建命令：`mvn 阶段1 阶段2...`
    >
    > 如果我们运行`mvn package`，Maven就会执行`default`生命周期，它会从开始一直运行到`package`这个phase为止：
    >
    > - validate
    > - initialize
    > - ...
    > - prepare-package
    > - package
    >
    > 如果我们运行`mvn compile`，Maven也会执行`default`生命周期，但这次它只会运行到`compile`，即以下几个phase：
    >
    > - validate
    > - initialize
    > - ...
    > - process-resources
    > - compile
    >
    > Maven另一个常用的生命周期是`clean`，它会执行3个阶段：
    >
    > - pre-clean
    > - clean
    > - post-clean
    >
    > 所以，我们使用`mvn`这个命令时，后面的参数是phase，Maven自动根据生命周期运行到指定的phase。
    >
    > 更复杂的例子是指定多个phase，例如，运行`mvn clean package`，Maven先执行`clean`生命周期并运行到`clean`这个phase，然后执行`default`生命周期并运行到`package`这个phase，实际执行的phase如下：
    >
    > - pre-clean
    > - clean
    > - validate（开始执行default生命周期的第一个phase）
    > - initialize
    > - ...
    > - prepare-package
    > - package
    >
    > 在实际开发过程中，经常使用的命令有：
    >
    > `mvn clean`：清理所有生成的class和jar；
    >
    > `mvn clean compile`：先清理，再执行到`compile`；
    >
    > `mvn clean test`：先清理，再执行到`test`，因为执行`test`前必须执行`compile`，所以这里不必指定`compile`；
    >
    > `mvn clean package`：先清理，再执行到`package`。
    >
    > 经常用到的phase其实只有几个：
    >
    > - clean：清理
    > - compile：编译
    > - test：运行测试
    > - package：打包
    >
    > **通常，由于我们没有在`pom.xml`中进行相关的配置，因此大多数phase在执行过程中什么都不做。**

  - ##### Goal：

    > 执行一个phase又会触发一个或多个目标（goal）：
    >
    > | 执行的Phase | 对应执行的Goal                     |
    > | ----------- | ---------------------------------- |
    > | compile     | compiler:compile                   |
    > | test        | compiler:testCompile surefire:test |
    >
    > goal的命名总是`abc:xyz`这种形式。
    >
    > 看到这里，相信大家对lifecycle、phase和goal已经明白了吧？
    >
    > ![meng](https://liaoxuefeng.com/books/java/maven/process/meng.jpg)
    >
    > 其实我们类比一下就明白了：
    >
    > - lifecycle相当于Java的package，它包含一个或多个phase；
    > - phase相当于Java的class，它包含一个或多个goal；
    > - goal相当于class的method，它其实才是真正干活的。
    >
    > 大多数情况，我们只要指定phase，就默认执行这些phase默认绑定的goal，只有少数情况，我们可以直接指定运行一个goal，例如，启动Tomcat服务器：`mvn tomcat:run`
    >

    > Maven通过lifecycle、phase和goal来提供标准的构建流程。
    >
    > 最常用的构建命令是指定phase，然后让Maven执行到指定的phase：
    >
    > - mvn clean
    > - mvn clean compile
    > - mvn clean test
    > - mvn clean package
    >
    > 通常情况，我们总是执行phase默认绑定的goal，因此不必指定goal。

- #### 使用插件

  > 我们在前面介绍了Maven的lifecycle，phase和goal：使用Maven构建项目就是执行lifecycle，执行到指定的phase为止。每个phase会执行自己默认的一个或多个goal。goal是最小任务单元。
  >
  > 我们以`compile`这个phase为例，如果执行：
  >
  > ```bash
  > $ mvn compile
  > ```
  >
  > Maven将执行`compile`这个phase，这个phase会调用`compiler`插件执行关联的`compiler:compile`这个goal。
  >
  > 实际上，**执行每个phase都是通过某个插件（plugin）来执行的**，Maven本身其实并不知道如何执行`compile`，它只是负责找到对应的`compiler`插件，然后执行默认的关联的goal来完成编译（`compiler:compile`）。
  >
  > 所以，使用Maven，实际上就是配置好需要使用的插件，然后通过phase调用它们。
  >
  > Maven已经内置了一些常用的标准插件：
  >
  > | 插件名称 | 对应执行的phase |
  >| -------- | --------------- |
  > | clean    | clean           |
  > | compiler | compile         |
  > | surefire | test            |
  > | jar      | package         |
  > 
  > 如果标准插件无法满足需求，我们还可以使用自定义插件。使用自定义插件的时候，需要声明。例如，使用`maven-shade-plugin`可以创建一个可执行的jar，要使用这个插件，需要在`pom.xml`中声明它：
  >
  > ```xml
  ><project>
  >  ...
  > 	<build>
  >    		<plugins>
  > 			<plugin>
  > 				<groupId>org.apache.maven.plugins</groupId>
  > 				<artifactId>maven-shade-plugin</artifactId>
  >              <version>3.2.1</version>
  > 				<executions>
  >    					<execution>
  > 						<phase>package</phase>
  > 						<goals>
  > 							<goal>shade</goal>
  > 						</goals>
  > 						<configuration>
  >                          ...插件配置...
  > 						</configuration>
  >    					</execution>
  > 				</executions>
  > 			</plugin>
  > 		</plugins>
  > 	</build>
  > </project>
  > ```
  > 
  > 自定义插件往往需要一些配置，例如，`maven-shade-plugin`需要指定Java程序的入口，它的配置是：
  >
  > ```xml
  ><configuration>
  >    <transformers>
  >        <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
  >        	<mainClass>com.itranswarp.learnjava.Main</mainClass>
  >        </transformer>
  >      </transformers>
  >    </configuration>
  >    ```
  >    
  >    注意，Maven自带的标准插件例如`compiler`是无需声明的，只有引入其它的插件才需要声明。
  > 
  > 下面列举了一些常用的插件：（插件的配置和用法需参考插件的官方文档）
  >
  > - maven-shade-plugin：打包所有依赖包并生成可执行jar；
  >- cobertura-maven-plugin：生成单元测试覆盖率报告；
  > - findbugs-maven-plugin：对Java源码进行静态分析以找出潜在问题。
  >
  
- #### 模块管理

  > 在软件开发中，把一个大项目分拆为多个模块是降低软件复杂度的有效方法：
  >
  > ```tex
  >                         ┌ ─ ─ ─ ─ ─ ─ ┐
  >                           ┌─────────┐
  >                         │ │Module A │ │
  >                           └─────────┘
  > ┌──────────────┐ split  │ ┌─────────┐ │
  > │Single Project│───────▶  │Module B │
  > └──────────────┘        │ └─────────┘ │
  >                           ┌─────────┐
  >                         │ │Module C │ │
  >                           └─────────┘
  >                         └ ─ ─ ─ ─ ─ ─ ┘
  > ```
  >
  > 对于Maven工程来说，原来是一个大项目：
  >
  > ```
  > single-project
  > ├── pom.xml
  > └── src
  > ```
  >
  > 现在可以分拆成3个模块：
  >
  > ```
  > multiple-projects
  >   ├── module-a
  >   │   ├── pom.xml
  >   │   └── src
  >   ├── module-b
  >   │   ├── pom.xml
  >   │   └── src
  >   └── module-c
  >          ├── pom.xml
  >          └── src
  > ```
  >
  > Maven可以有效地管理多个模块，我们只需要把每个模块当作一个独立的Maven项目，它们有各自独立的`pom.xml`。例如，模块A的`pom.xml`：
  >
  > ```xml
  > <project xmlns="http://maven.apache.org/POM/4.0.0"
  >     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  >     xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  >     <modelVersion>4.0.0</modelVersion>
  > 
  >     <groupId>com.itranswarp.learnjava</groupId>
  >     <artifactId>module-a</artifactId>
  >     <version>1.0</version>
  >     <packaging>jar</packaging>
  > 
  >     <name>module-a</name>
  > 
  >      <properties>
  >        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  >        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
  >        <maven.compiler.source>11</maven.compiler.source>
  >        <maven.compiler.target>11</maven.compiler.target>
  >        <java.version>11</java.version>
  >      </properties>
  > 
  >      <dependencies>
  >        <dependency>
  >          <groupId>org.slf4j</groupId>
  >          <artifactId>slf4j-api</artifactId>
  >          <version>1.7.28</version>
  >        </dependency>
  >        <dependency>
  >          <groupId>ch.qos.logback</groupId>
  >          <artifactId>logback-classic</artifactId>
  >          <version>1.2.3</version>
  >          <scope>runtime</scope>
  >        </dependency>
  >        <dependency>
  >          <groupId>org.junit.jupiter</groupId>
  >          <artifactId>junit-jupiter-engine</artifactId>
  >          <version>5.5.2</version>
  >          <scope>test</scope>
  >        </dependency>
  >      </dependencies>
  > </project>
  > ```
  >
  > 模块B的`pom.xml`：
  >
  > ```xml
  ><project xmlns="http://maven.apache.org/POM/4.0.0"
  >  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  >  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  >      <modelVersion>4.0.0</modelVersion>
  >    
  >      <groupId>com.itranswarp.learnjava</groupId>
  >    <artifactId>module-b</artifactId>
  >      <version>1.0</version>
  >      <packaging>jar</packaging>
  >    
  >    	<name>module-b</name>
  > 
  >      <properties>
  >        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  >        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
  >        <maven.compiler.source>11</maven.compiler.source>
  >        <maven.compiler.target>11</maven.compiler.target>
  >        <java.version>11</java.version>
  >      </properties>
  >    
  >      <dependencies>
  >        <dependency>
  >          <groupId>org.slf4j</groupId>
  >          <artifactId>slf4j-api</artifactId>
  >          <version>1.7.28</version>
  >        </dependency>
  >        <dependency>
  >          <groupId>ch.qos.logback</groupId>
  >          <artifactId>logback-classic</artifactId>
  >          <version>1.2.3</version>
  >          <scope>runtime</scope>
  >        </dependency>
  >        <dependency>
  >          <groupId>org.junit.jupiter</groupId>
  >          <artifactId>junit-jupiter-engine</artifactId>
  >          <version>5.5.2</version>
  >          <scope>test</scope>
  >        </dependency>
  >      </dependencies>
  >    </project>
  >    ```
  > 
  > 可以看出来，模块A和模块B的`pom.xml`高度相似，因此，我们可以提取出共同部分作为`parent`：
  >
  > ```xml
  ><project xmlns="http://maven.apache.org/POM/4.0.0"
  >  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  > xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  >  <modelVersion>4.0.0</modelVersion>
  > 
  >     <groupId>com.itranswarp.learnjava</groupId>
  >     <artifactId>parent</artifactId>
  >     <version>1.0</version>
  >  <packaging>pom</packaging>
  >    
  >     <name>parent</name>
  >    
  >     <properties>
  >      <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  >         <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
  >      <maven.compiler.source>11</maven.compiler.source>
  >         <maven.compiler.target>11</maven.compiler.target>
  >         <java.version>11</java.version>
  >     </properties>
  >    
  >     <dependencies>
  >         <dependency>
  >             <groupId>org.slf4j</groupId>
  >          <artifactId>slf4j-api</artifactId>
  >             <version>1.7.28</version>
  >         </dependency>
  >         <dependency>
  >             <groupId>ch.qos.logback</groupId>
  >             <artifactId>logback-classic</artifactId>
  >             <version>1.2.3</version>
  >             <scope>runtime</scope>
  >         </dependency>
  >         <dependency>
  >             <groupId>org.junit.jupiter</groupId>
  >             <artifactId>junit-jupiter-engine</artifactId>
  >             <version>5.5.2</version>
  >             <scope>test</scope>
  >         </dependency>
  >     </dependencies>
  >    </project>
  >    ```
  >    
  >    注意到parent的`<packaging>`是`pom`而不是`jar`，因为`parent`本身不含任何Java代码。编写`parent`的`pom.xml`只是为了在各个模块中减少重复的配置。现在我们的整个工程结构如下：
  > 
  > ```tex
  >multiple-project
  >   ├── pom.xml
  >  ├── parent
  >   │   └── pom.xml
  >  ├── module-a
  >   │   ├── pom.xml
  >   │   └── src
  >   ├── module-b
  >   │   ├── pom.xml
  >   │   └── src
  >   └── module-c
  >          ├── pom.xml
  >          └── src
  > ```
  > 
  > 这样模块A就可以简化为：
  > 
  >    ```xml
  >    <project xmlns="http://maven.apache.org/POM/4.0.0"
  >  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  > xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  >      <modelVersion>4.0.0</modelVersion>
  >
  >      <parent>
  >            <groupId>com.itranswarp.learnjava</groupId>
  >            <artifactId>parent</artifactId>
  >            <version>1.0</version>
  >            <relativePath>../parent/pom.xml</relativePath>
  >      </parent>
  >    
  >        <artifactId>module-a</artifactId>
  >        <packaging>jar</packaging>
  >        <name>module-a</name>
  >    </project>
  >    ```
  > 
  >    模块B、模块C都可以直接从`parent`继承，大幅简化了`pom.xml`的编写。
  >    
  >    如果模块A依赖模块B，则模块A需要模块B的jar包才能正常编译，我们需要在模块A中引入模块B：
  > 
  > ```xml
  > ...
  >  <dependencies>
  >        <dependency>
  >            <groupId>com.itranswarp.learnjava</groupId>
  >            <artifactId>module-b</artifactId>
  >            <version>1.0</version>
  >        </dependency>
  >  </dependencies>
  >    ```
  >    
  >    最后，在编译的时候，需要在根目录创建一个`pom.xml`统一编译：
  >    
  >    ```xml
  >    <project xmlns="http://maven.apache.org/POM/4.0.0"
  >    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  >    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  >  
  >     <modelVersion>4.0.0</modelVersion>
  >     <groupId>com.itranswarp.learnjava</groupId>
  >     <artifactId>build</artifactId>
  >      <version>1.0</version>
  >     <packaging>pom</packaging>
  >      <name>build</name>
  >  
  >        <modules>
  >            <module>parent</module>
  >          <module>module-a</module>
  >            <module>module-b</module>
  >            <module>module-c</module>
  >        </modules>
  >    </project>
  >    ```
  >    
  > 这样，在根目录执行`mvn clean package`时，Maven根据根目录的`pom.xml`找到包括`parent`在内的共4个`<module>`，一次性全部编译。
  >    
  >    ##### 中央仓库
  >    
  >    其实我们使用的大多数第三方模块都是这个用法，例如，我们使用commons logging、log4j这些第三方模块，就是第三方模块的开发者自己把编译好的jar包发布到Maven的中央仓库中。
  >    
  >    ##### 私有仓库
  > 
  > 私有仓库是指公司内部如果不希望把源码和jar包放到公网上，那么可以搭建私有仓库。私有仓库总是在公司内部使用，它只需要在本地的`~/.m2/settings.xml`中配置好，使用方式和中央仓位没有任何区别。
  >
  > ##### 本地仓库
  >
  > 本地仓库是指把本地开发的项目“发布”在本地，这样其他项目可以通过本地仓库引用它。但是我们不推荐把自己的模块安装到Maven的本地仓库，因为每次修改某个模块的源码，都需要重新安装，非常容易出现版本不一致的情况。更好的方法是使用模块化编译，在编译的时候，告诉Maven几个模块之间存在依赖关系，需要一块编译，Maven就会自动按依赖顺序编译这些模块。
  >
  
- #### 使用mvnw

  > 我们使用Maven时，基本上只会用到`mvn`这一个命令。有些童鞋可能听说过`mvnw`，这个是啥？
  >
  > Maven Wrapper，通常简称为 "MavenW" 或 "mvnw"（对于 UNIX 系统）和 "mvnw.cmd"（对于 Windows 系统），是一个为 Maven 项目提供的工具，它可以确保无论在哪里运行项目，都可以使用正确和预期的 Maven 版本。
  >
  > 默认情况下，我们的所有项目都会使用全局安装的这个Maven版本。但是对于某些项目来说，它可能必须使用某个特定的Maven版本，这时候就可以使用Maven Wrapper，它可以负责给这个特定的项目安装指定版本的Maven，而其他项目不受影响。
  >
  > Maven Wrapper 的主要功能和优势如下：
  >
  > 1. **版本独立性**： 当你使用 Maven Wrapper 运行项目，它会确保使用的是项目所需的特定 Maven 版本。这意味着，不管开发人员或 CI/CD 系统上安装了哪个 Maven 版本，项目都会使用与其配置相匹配的 Maven 版本。
  > 2. **无需预先安装 Maven**： 如果在系统上没有安装 Maven，Maven Wrapper 会自动下载所需的 Maven 版本。这对于新的开发人员或新的构建环境特别有用，因为它们可以立即开始构建项目，而无需手动安装和配置 Maven。
  > 3. **项目一致性**： 由于所有开发者和构建系统都使用相同的 Maven 版本，这消除了由于版本差异导致的构建不一致性问题。
  > 4. **简化的命令**： 使用 Maven Wrapper，你可以使用 `./mvnw`（在 UNIX-like 系统）或 `mvnw.cmd`（在 Windows）代替标准的 `mvn` 命令来执行 Maven 构建。
  > 5. **集成到项目中**： Maven Wrapper 包括一些必要的脚本文件和配置，这些文件被提交到项目的版本控制中。这确保了无论谁检出项目，都可以立即使用 Maven Wrapper。
  >
  > 创建 Maven Wrapper 的常见方法是使用 `maven-wrapper-plugin`，这个插件可以帮助你为项目生成必要的 Wrapper 文件和配置。
  >
  > 总的来说，Maven Wrapper 是一个确保 Maven 项目在任何环境中都能使用正确 Maven 版本的工具，这对于项目的一致性和便利性非常有帮助。
  
  - ##### 执行 Maven Wrapper 插件，为你的项目添加 Maven Wrapper：（该插件没有会自动下载）
  
    > 在项目的根目录（即`pom.xml`所在的目录）下运行安装命令：
    >
    > ```bash
    > $ mvn wrapper:wrapper
    > ```
    >
    > 执行上述命令后，你的项目目录中应该会出现以下新文件和目录：
    >
    > ```tex
    > my-project
    > ├── .mvn
    > │   └── wrapper
    > │       └── maven-wrapper.properties
    > │       └── maven-wrapper.jar
    > ├── mvnw
    > ├── mvnw.cmd
    > ├── pom.xml
    > └── src
    >     ├── main
    >     │   ├── java
    >     │   └── resources
    >     └── test
    >         ├── java
    >         └── resources
    > ```
    >
    > - `mvnw`: 这是 Unix/Linux/macOS 系统上的 Maven Wrapper 启动脚本。
    > - `mvnw.cmd`: 这是 Windows 系统上的 Maven Wrapper 启动脚本。
    > - `.mvn/wrapper/maven-wrapper.properties`: 这个文件包含 Maven Wrapper 的配置信息，例如要使用的 Maven 版本和下载 URL。
    > - `.mvn/wrapper/maven-wrapper.jar`: 这是 Maven Wrapper 的核心 JAR 文件，它负责确保使用正确的 Maven 版本。
    >
    > 我们只需要把`mvn`命令改成`mvnw`就可以使用跟项目关联的Maven：`mvnw clean package`
    
    > - 添加的 Maven Wrapper 默认使用最新版本的Maven。如果要指定使用的Maven版本，使用下面的安装命令指定版本，例如`3.9.0`：
    >
    >   ```bash
    >   $ mvn wrapper:wrapper -Dmaven=3.9.0
    >   ```
    >
    > - 或者打开 `maven-wrapper.properties` 文件，找到 `distributionUrl` 属性，这个属性定义了 Maven 分发的下载 URL。例如：
    >
    >   ```properties
    >   distributionUrl=https\://repo.maven.apache.org/maven2/org/apache/maven/apache-maven/3.6.3/apache-maven-3.6.3-bin.zip
    >   ```
    >
    >   假设我们想改为使用 Maven 3.8.1，你可以将 URL 中的版本号 `3.6.3` 更改为 `3.8.1`：
    >
    >   ```properties
    >   distributionUrl=https\://repo.maven.apache.org/maven2/org/apache/maven/apache-maven/3.8.1/apache-maven-3.8.1-bin.zip
    >   ```
    >
    >   如果这是你首次使用 Maven 3.8.1 版本，Maven Wrapper 会自动从指定的 URL 下载它。
  
- #### 发布Artifact

  > 当我们使用`commons-logging`这些第三方开源库的时候，我们实际上是通过Maven自动下载它的jar包，并根据其`pom.xml`解析依赖，自动把相关依赖包都下载后加入到classpath。
  >
  > 那么问题来了：当我们自己写了一个牛逼的开源库时，非常希望别人也能使用，总不能直接放个jar包的链接让别人下载吧？
  >
  > 如果我们把自己的开源库放到Maven的repo中，那么，别人只需按标准引用`groupId:artifactId:version`，即可自动下载jar包以及相关依赖。因此，本节我们介绍如何发布一个库到Maven的repo中。
  >
  > 把自己的库发布到Maven的repo中有好几种方法，我们介绍3种最常用的方法。
  
  - ##### 以静态文件发布：
  
    > 如果我们观察一个中央仓库的Artifact结构，例如[Commons Math](https://commons.apache.org/proper/commons-math/)，它的groupId是`org.apache.commons`，artifactId是`commons-math3`，以版本`3.6.1`为例，发布在中央仓库的文件夹路径就是https://repo1.maven.org/maven2/org/apache/commons/commons-math3/3.6.1/，在此文件夹下，`commons-math3-3.6.1.jar`就是发布的jar包，`commons-math3-3.6.1.pom`就是它的`pom.xml`描述文件，`commons-math3-3.6.1-sources.jar`是源代码，`commons-math3-3.6.1-javadoc.jar`是文档。其它以`.asc`、`.md5`、`.sha1`结尾的文件分别是GPG签名、MD5摘要和SHA-1摘要。
    >
    > 我们只要按照这种目录结构组织文件，它就是一个有效的Maven仓库。
    >
    > 我们以广受好评的开源项目[how-to-become-rich](https://github.com/michaelliao/how-to-become-rich)为例，先创建Maven工程目录结构如下：
    >
    > ```tex
    > how-to-become-rich
    > ├── maven-repo        <-- Maven本地文件仓库
    > ├── pom.xml           <-- 项目文件
    > ├── src
    > │   ├── main
    > │   │   ├── java      <-- 源码目录
    > │   │   └── resources <-- 资源目录
    > │   └── test
    > │       ├── java      <-- 测试源码目录
    > │       └── resources <-- 测试资源目录
    > └── target            <-- 编译输出目录
    > ```
    >
    > 在`pom.xml`中添加如下内容：
    >
    > ```xml
    > <project ...>
    >     ...
    >     <distributionManagement>
    >         <repository>
    >             <id>local-repo-release</id>
    >             <name>GitHub Release</name>
    >             <url>file://${project.basedir}/maven-repo</url>
    >         </repository>
    >     </distributionManagement>
    > 
    >     <build>
    >         <plugins>
    >             <plugin>
    >                 <artifactId>maven-source-plugin</artifactId>
    >                 <executions>
    >                     <execution>
    >                         <id>attach-sources</id>
    >                         <phase>package</phase>
    >                         <goals><goal>jar-no-fork</goal></goals>
    >                     </execution>
    >                 </executions>
    >             </plugin>
    >             <plugin>
    >                 <artifactId>maven-javadoc-plugin</artifactId>
    >                 <executions>
    >                     <execution>
    >                         <id>attach-javadocs</id>
    >                         <phase>package</phase>
    >                         <goals><goal>jar</goal></goals>
    >                     </execution>
    >                 </executions>
    >             </plugin>
    >         </plugins>
    >     </build>
    > </project>
    > ```
    >
    > 注意到`<distributionManagement>`，它指示了发布的软件包的位置，这里的`<url>`是项目根目录下的`maven-repo`目录，在`<build>`中定义的两个插件`maven-source-plugin`和`maven-javadoc-plugin`分别用来创建源码和javadoc，如果不想发布源码，可以把对应的插件去掉。
    >
    > 我们直接在项目根目录下运行Maven命令`mvn clean package deploy`，如果一切顺利，我们就可以在`maven-repo`目录下找到部署后的所有文件：[how-to-become-rich-repo](https://github.com/michaelliao/how-to-become-rich/tree/master/maven-repo)
    >
    > 最后一步，是把这个工程推到GitHub上，并选择`Settings`-`GitHub Pages`，选择`master branch`启用Pages服务：
    >
    > ![enable-github-pages](https://liaoxuefeng.com/books/java/maven/deploy/github-page.jpg)
    >
    > 这样，把全部内容推送至GitHub后，即可作为静态网站访问Maven的repo，它的地址是https://michaelliao.github.io/how-to-become-rich/maven-repo/。版本`1.0.0`对应的jar包地址是：`https://michaelliao.github.io/how-to-become-rich/maven-repo/com/itranswarp/rich/how-to-become-rich/1.0.0/how-to-become-rich-1.0.0.jar`。
    >
    > 现在，如果其他人希望引用这个Maven包，我们可以告知如下依赖即可：
    >
    > ```xml
    > <dependency>
    >      <groupId>com.itranswarp.rich</groupId>
    >      <artifactId>how-to-become-rich</artifactId>
    >      <version>1.0.0</version>
    > </dependency>
    > ```
    >
    > 但是，除了正常导入依赖外，对方还需要再添加一个`<repository>`的声明，即使用方完整的`pom.xml`如下：
    >
    > ```xml
    > <project xmlns="http://maven.apache.org/POM/4.0.0"
    > xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    > xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    >     <modelVersion>4.0.0</modelVersion>
    > 
    >     <groupId>example</groupId>
    >     <artifactId>how-to-become-rich-usage</artifactId>
    >     <version>1.0-SNAPSHOT</version>
    >     <packaging>jar</packaging>
    > 
    >     <properties>
    >         <maven.compiler.source>11</maven.compiler.source>
    >         <maven.compiler.target>11</maven.compiler.target>
    >         <java.version>11</java.version>
    >     </properties>
    > 
    >     <repositories>
    >         <repository>
    >             <id>github-rich-repo</id>
    >             <name>The Maven Repository on Github</name>
    >             <url>https://michaelliao.github.io/how-to-become-rich/maven-repo/</url>
    >         </repository>
    >     </repositories>
    > 
    >     <dependencies>
    >         <dependency>
    >             <groupId>com.itranswarp.rich</groupId>
    >             <artifactId>how-to-become-rich</artifactId>
    >             <version>1.0.0</version>
    >         </dependency>
    >     </dependencies>
    > </project>
    > ```
    >
    > 在`<repository>`中，我们必须声明发布的Maven的repo地址，其中`<id>`和`<name>`可以任意填写，`<url>`填入GitHub Pages提供的地址+`/maven-repo/`后缀。现在，即可正常引用这个库并编写代码如下：
    >
    > ```java
    > Millionaire millionaire = new Millionaire();
    > System.out.println(millionaire.howToBecomeRich());
    > ```
    >
    > 有的童鞋会问，为什么使用`commons-logging`等第三方库时，并不需要声明repo地址？这是因为这些库都是发布到Maven中央仓库的，发布到中央仓库后，不需要告诉Maven仓库地址，因为它知道中央仓库的地址默认是https://repo1.maven.org/maven2/，也可以通过`~/.m2/settings.xml`指定一个代理仓库地址以替代中央仓库来提高速度（参考[依赖管理](https://liaoxuefeng.com/books/java/maven/20-dependency/index.html)的Maven镜像）。
    >
    > 因为GitHub Pages并不会把我们发布的Maven包同步到中央仓库，所以使用方必须手动添加一个我们提供的仓库地址。
    >
    > 此外，通过GitHub Pages发布Maven repo时需要注意一点，即不要改动已发布的版本。因为Maven的仓库是不允许修改任何版本的，对一个库进行修改的唯一方法是发布一个新版本。但是通过静态文件的方式发布repo，实际上我们是可以修改jar文件的，但最好遵守规范，不要修改已发布版本。
  
  - ##### 通过Nexus发布到中央仓库：
  
    > 有的童鞋会问，能不能把自己的开源库发布到Maven的中央仓库，这样用户就不需要声明repo地址，可以直接引用，显得更专业。
    >
    > 当然可以，但我们不能直接发布到Maven中央仓库，而是通过曲线救国的方式，发布到[central.sonatype.org](https://central.sonatype.org/)，它会定期自动同步到Maven的中央仓库。[Nexus](https://www.sonatype.com/nexus-repository-oss)是一个支持Maven仓库的软件，由Sonatype开发，有免费版和专业版两个版本，很多大公司内部都使用Nexus作为自己的私有Maven仓库，而这个[central.sonatype.org](https://central.sonatype.org/)相当于面向开源的一个Nexus公共服务。
    >
    > 所以，第一步是在[central.sonatype.org](https://central.sonatype.org/)上注册一个账号，如果注册顺利并审核通过，会得到一个登录账号，然后，通过[这个页面](https://central.sonatype.org/pages/apache-maven.html)一步一步操作就可以成功地将自己的Artifact发布到Nexus上，再耐心等待几个小时后，你的Artifact就会出现在Maven的中央仓库中。
    >
    > 这里简单提一下发布重点与难点：
    >
    > - 必须正确创建GPG签名，Linux和Mac下推荐使用gnupg2；
    > - 必须在`~/.m2/settings.xml`中配置好登录用户名和口令，以及GPG口令：
    >
    > ```xml
    > <settings ...>
    >    ...
    >     <servers>
    >         <server>
    >            <id>ossrh</id>
    >             <username>OSSRH-USERNAME</username>
    >             <password>OSSRH-PASSWORD</password>
    >         </server>
    >     </servers>
    >     <profiles>
    >         <profile>
    >             <id>ossrh</id>
    >             <activation>
    >                 <activeByDefault>true</activeByDefault>
    >             </activation>
    >             <properties>
    >                 <gpg.executable>gpg2</gpg.executable>
    >                 <gpg.passphrase>GPG-PASSWORD</gpg.passphrase>
    >             </properties>
    >         </profile>
    >     </profiles>
    > </settings>
    > ```
    >
    > 在待发布的Artifact的`pom.xml`中添加OSS的Maven repo地址，以及`maven-jar-plugin`、`maven-source-plugin`、`maven-javadoc-plugin`、`maven-gpg-plugin`、`nexus-staging-maven-plugin`：
    >
    > ```xml
    > <project ...>
    >     ...
    >    <distributionManagement>
    >         <snapshotRepository>
    >            <id>ossrh</id>
    >             <url>https://oss.sonatype.org/content/repositories/snapshots</url>
    >        </snapshotRepository>
    > 
    >         <repository>
    >             <id>ossrh</id>
    >             <name>Nexus Release Repository</name>
    >             <url>http://oss.sonatype.org/service/local/staging/deploy/maven2/</url>
    >         </repository>
    >     </distributionManagement>
    > 
    >     <build>
    >         <plugins>
    >             <plugin>
    >                 <groupId>org.apache.maven.plugins</groupId>
    >                 <artifactId>maven-jar-plugin</artifactId>
    >                 <executions>
    >                     <execution>
    >                         <goals>
    >                             <goal>jar</goal>
    >                             <goal>test-jar</goal>
    >                         </goals>
    >                     </execution>
    >                 </executions>
    >             </plugin>
    >             <plugin>
    >                 <groupId>org.apache.maven.plugins</groupId>
    >                 <artifactId>maven-source-plugin</artifactId>
    >                 <executions>
    >                     <execution>
    >                         <id>attach-sources</id>
    >                         <goals>
    >                             <goal>jar-no-fork</goal>
    >                         </goals>
    >                     </execution>
    >                 </executions>
    >             </plugin>
    >             <plugin>
    >                 <groupId>org.apache.maven.plugins</groupId>
    >                 <artifactId>maven-javadoc-plugin</artifactId>
    >                 <executions>
    >                     <execution>
    >                         <id>attach-javadocs</id>
    >                         <goals>
    >                             <goal>jar</goal>
    >                         </goals>
    >                         <configuration>
    >                             <additionalOption>
    >                                 <additionalOption>-Xdoclint:none</additionalOption>
    >                             </additionalOption>
    >                         </configuration>
    >                     </execution>
    >                 </executions>
    >             </plugin>
    >             <plugin>
    >                 <groupId>org.apache.maven.plugins</groupId>
    >                 <artifactId>maven-gpg-plugin</artifactId>
    >                 <executions>
    >                     <execution>
    >                         <id>sign-artifacts</id>
    >                         <phase>verify</phase>
    >                         <goals>
    >                             <goal>sign</goal>
    >                         </goals>
    >                     </execution>
    >                 </executions>
    >             </plugin>
    >             <plugin>
    >                 <groupId>org.sonatype.plugins</groupId>
    >                 <artifactId>nexus-staging-maven-plugin</artifactId>
    >                 <version>1.6.3</version>
    >                 <extensions>true</extensions>
    >                 <configuration>
    >                     <serverId>ossrh</serverId>
    >                     <nexusUrl>https://oss.sonatype.org/</nexusUrl>
    >                     <autoReleaseAfterClose>true</autoReleaseAfterClose>
    >                 </configuration>
    >             </plugin>
    >         </plugins>
    >     </build>
    > </project>
    > ```
    >
    > 最后执行命令`mvn clean package deploy`即可发布至[central.sonatype.org](https://central.sonatype.org/)。
    >
    > 此方法前期需要复杂的申请账号和项目的流程，后期需要安装调试GPG，但只要跑通流程，后续发布都只需要一行命令。
  
  - ##### 发布到私有仓库：
  
    > 通过`nexus-staging-maven-plugin`除了可以发布到[central.sonatype.org](https://central.sonatype.org/)外，也可以发布到私有仓库，例如，公司内部自己搭建的Nexus服务器。
    >
    > 如果没有私有Nexus服务器，还可以发布到[GitHub Packages](https://github.com/features/packages)。GitHub Packages是GitHub提供的仓库服务，支持Maven、NPM、Docker等。使用GitHub Packages时，无论是发布Artifact，还是引用已发布的Artifact，都需要明确的授权Token，因此，GitHub Packages只能作为私有仓库使用。
    >
    > 在发布前，我们必须首先登录后在用户的`Settings`-`Developer settings`-`Personal access tokens`中创建两个Token，一个用于发布，一个用于使用。发布Artifact的Token必须有`repo`、`write:packages`和`read:packages`权限：
    >
    > ![token-scopes](https://liaoxuefeng.com/books/java/maven/deploy/private-repo.jpg)
    >
    > 使用Artifact的Token只需要`read:packages`权限。
    >
    > 在发布端，把GitHub的用户名和发布Token写入`~/.m2/settings.xml`配置中：
    >
    > ```xml
    > <settings ...>
    >      ...
    >      <servers>
    >        <server>
    >          <id>github-release</id>
    >          <username>GITHUB-USERNAME</username>
    >          <password>f052...c21f</password>
    >        </server>
    >      </servers>
    > </settings>
    > ```
    >
    > 然后，在需要发布的Artifact的`pom.xml`中，添加一个`<repository>`声明：
    >
    > ```xml
    > <project ...>
    >     ...
    >     <distributionManagement>
    >         <repository>
    >             <id>github-release</id>
    >             <name>GitHub Release</name>
    >            <url>https://maven.pkg.github.com/michaelliao/complex</url>
    >         </repository>
    >    </distributionManagement>
    > </project>
    > ```
    >
    > 
    >
    > 注意到`<id>`和`~/.m2/settings.xml`配置中的`<id>`要保持一致，因为发布时Maven根据id找到用于登录的用户名和Token，才能成功上传文件到GitHub。我们直接通过命令`mvn clean package deploy`部署，成功后，在GitHub用户页面可以看到该Artifact：
    >
    > ![github-packages](https://liaoxuefeng.com/books/java/maven/deploy/github-packages.jpg)
    >
    > 完整的配置请参考[complex](https://github.com/michaelliao/complex/)项目，这是一个非常简单的支持复数运算的库。
    >
    > 使用该Artifact时，因为GitHub的Package只能作为私有仓库使用，所以除了在使用方的`pom.xml`中声明`<repository>`外：
    >
    > ```xml
    > <project ...>
    >     ...
    >    <repositories>
    >         <repository>
    >            <id>github-release</id>
    >             <name>GitHub Release</name>
    >            <url>https://maven.pkg.github.com/michaelliao/complex</url>
    >         </repository>
    >    </repositories>
    > 
    >    <dependencies>
    >         <dependency>
    >             <groupId>com.itranswarp</groupId>
    >             <artifactId>complex</artifactId>
    >             <version>1.0.0</version>
    >         </dependency>
    >     </dependencies>
    >     ...
    > </project>
    > ```
    >
    > 还需要把有读权限的Token配置到`~/.m2/settings.xml`文件中。
  
  > 使用Maven发布一个Artifact时：
  >
  > - 可以发布到本地，然后推送到远程Git库，由静态服务器提供基于网页的repo服务，使用方必须声明repo地址；
  >- 可以发布到[central.sonatype.org](https://central.sonatype.org/)，并自动同步到Maven中央仓库，需要前期申请账号以及本地配置；
  > - 可以发布到GitHub Packages作为私有仓库使用，必须提供Token以及正确的权限才能发布和使用。



![4e4e736deff44f98a47c81246b114352](C:\Users\22737\Desktop\studyJava\1javaSE\maven\assets\4e4e736deff44f98a47c81246b114352.png)

- 1

  > ```tex
  > ================================================maven基础=====================================================================
  > （看工程javaweb下的模块mvn1）
  > 
  > # 传统开发项目的问题（没有使用maven）：
  > 	*很多模块，模块之间有父子或依赖关系，手工管理关系比较费劲；
  > 	*各个第三方的jar包之间相互依赖，a依赖b，使用a之前还得先引入b；各个依赖之间的版本也需要兼容，自己手动加及其浪费时间且容易出错；
  > 	*编译测试打包部署我们自己做很麻烦；
  > 
  > # 什么是maven？
  > 		maven就是一个项目的管理工具，它将项目开发和管理过程抽象成一个“项目对象模型”POM（Project Object Model），这个模型由maven来做，
  > 	项目模型信息通过程序员给定的pom.xml文件，每个项目的pom文件就代表了不同的项目对象模型POM，通过这个POM模型maven就可以帮我们来做Java
  > 	项目的构建和依赖管理，极大地减少了我们的工作量，maven就是Java写的；
  > 		maven有两大功能：项目构建和依赖管理；依赖管理就是我们用到的jar包，我们配置以下它就帮我们自己去仓库里找资源了，不需要我们配置classpath
  > 	或添加编译时的依赖了；项目构建是测试、打包、部署、编译、运行等一系列构建项目相关的操作，它都帮我们做了，这些构建项目相关的功能是由一些插件程序来做的，
  > 	我们直接通过命令使用即可非常方便；
  > 		不仅如此，它提供了一个统一的、标准的，项目结构目录；以前我们得根据使用的开发工具，eclipse或idea提供的项目目录，项目部署运行很不方便，
  > 	即使JavaWeb约定了项目上线时的目录，但不同编译器上项目的源代码目录结构还是不同；现在我们的maven将项目源码的目录结构进行了统一，这个很棒；
  > 
  > # maven的安装：（http://maven.apache.org/）
  > 	将下载的压缩包解压即安装；
  > 	如果要在cmd中运行，需要配置MAVEN_HOME；由于maven是Java写的，所以也需要JAVA_HOME环境变量的支撑；然后在cmd中输入“mvn -v”测试是否成功；
  > 		（后期我们用idea操作maven，所以也可以不配置）
  > 	maven的安装目录：
  > 		bin：存放maven的核心运行程序文件，包括清理、编译、测试、打包等工具的启动程序都在这里存放；
  > 		boot：这里存放maven的类加载器；maven帮我们管理依赖，所以它有自己的类加载器去帮我们加载Java类，我们不需要自己配置classpath去使用jdk的类加载器；
  > 		conf：配置文件目录；settings.xml是maven的核心配置文件，logging是日志目录；
  > 		lib：maven运行所需要的jar包依赖；
  > 
  > ------------------------------------------
  > maven的核心概念：
  > 	1、仓库：仓库中存放了大量jar包，我们项目中需要的所有jar包，都会通过pom文件去指定jar包位置，然后maven自动去仓库中加载jar包来用，共分为为3种：
  > 		*本地仓库：pom中指定jar的包，maven会先到本地计算机中的本地仓库中去找，没有的话再去私服中找，还没有就去中央仓库找；只要找到就下载到本地/私服；
  > 		*私服仓库：它是公司范围内存储资源的仓库，在公司的局域网内部；如果你的项目/模块做完了，希望别人用，也可以安装/上传到私服中；并且可以保护
  > 				公司内部具有版权或购买的私有项目jar包；
  > 		*中央仓库：它由maven团队维护，里面存放了世界上99%的jar包，所有开源的jar包都在中央仓库中存放，这些都是全世界共享的；以后开发，
  > 			开源的jar包可以不去官网找了，直接去maven中央仓库网站上下载就行；（中央仓库地址：https://mvnrepository.com）
  > 
  > 	2、坐标：它是标识资源在仓库的唯一位置的，是maven找依赖的地方；坐标由3部分顺序组成：
  > 			组织id（groupId）：通常由【com.公司名.业务线.子业务线】最多4层构成，每一层都是一个目录，如org.mybatis就是2层目录；
  > 			项目名（artifactId）：通常由【产品线名-模块名】组成，它是一层目录，如junit
  > 			项目版本（version）：通常由【主版本号.次版本号.修订版本号】组成，它是一层目录，如1.1.0-SNAPSHOT；
  > 							如果该项目还在开发中，是不稳定版本，通常在版本号后带【-SNAPSHOT】快照的意思；【RELEASE】是正式发布版本）
  > 			（了解）打包方式（packaging）：这个项目最终是以jar还是war包形式打包，默认是jar包，包名通常是【artifactId-version.war/jar】
  > 
  > 本地仓库的位置：只要命令行执行了mvn，那么本地仓库文件夹就会在计算机中创建好，不配置的话默认在C:\Users\用户名\.m2\repository这就是仓库的根目录；
  > 	但是这个位置在c盘中，以后这个仓库会越来越庞大，我想改位置不希望在c盘，怎么自定义仓库位置呢，需要在maven的配置文件settings.xml中配置；
  > 	找到注释中的：<localRepository>/path/to/local/repo</localRepository>给它拿出来配置即可；
  > 
  > 中央仓库的位置：在maven的lib中用压缩软件随便打开一个jar包，然后返回上一级查找“pom*.*”，找到“pom-4.0.0.xml”，定位过去打开，里面配置了中央仓库地址：
  > 	<url>https://repo.maven.apache.org/maven2</url>
  > 	我们如果每次找文件去这个国外服务器就很慢，所以我们需要配置一个中央仓库的国内镜像地址，这里用阿里云镜像，以后找东西去镜像服务器找更快；
  > 	配置镜像服务器地址：在settings.xml中进行配置，在其中找到<mirrors>标签，里面加上：
  > ---------------------
  > <mirror>
  > 	<!-- 镜像名称 -->
  > 	<id>nexus-aliyun</id>
  > 	<!-- 镜像谁 -->
  > 	<mirrorOf>central</mirrorOf>
  > 	<!-- 名称不重要 -->
  > 	<name>Nexus aliyun</name>
  > 	<!-- 阿里云镜像地址 -->
  > 	<url>http://maven.aliyun.com/nexus/content/groups/public/</url>
  > </mirror>
  > <!-- 备选镜像，也是可以通过 url 去查找确定一下，该镜像是否含有你想要的包，它比 spring-libs-milestone 快  -->
  > <mirror>
  >     <id>central-repository</id>
  >     <mirrorOf>*</mirrorOf>
  >     <name>Central Repository</name>
  >     <url>http://central.maven.org/maven2/</url>
  > </mirror>
  > ---------------------
  > 
  > （全局settings和局部settings：如果其他用户登录上此电脑，不想用刚刚的设置，可以在repository同级目录中新建一个settings.xml然后进行配置，
  > 	这是该用户自己的局部settings，换用户登录就不起作用了；如果局部和全局不同，局部会覆盖windows全局的配置）
  > 
  > 高版本的jdk还得在配置文件中配置jdk版本，找到profiles环境标签内部添加：（后面再说环境标签<profile>）
  > ---------------------
  > <profile>
  > 	<id>jdk17</id>
  > 	<activation>
  > 		<activeByDefault>true</activeByDefault>
  > 		<jdk>17</jdk>
  > 	</activation>
  > 	<properties>
  > 		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  > 		<maven.compiler.source>17</maven.compiler.source>
  > 		<maven.compiler.target>17</maven.compiler.target>
  > 		<maven.compiler.compilerVersion>17</maven.compiler.compilerVersion>
  > 	</properties>
  > </profile>
  > ---------------------
  > 
  > 这里用的maven-3.8.4版本，它默认使用的插件版本太低，我们给它插件版本设置高一点：到
  > 	【apache-maven-3.8.4\lib\maven-core-3.8.4.jar\META-INF\plexus\components.xml】目录中将默认的插件版本改成最新的；
  > 	https://maven.apache.org/plugins/index.html中有插件的版本；
  > 	插件在本地仓库的【org.apache.maven.plugins】里面
  > 
  > ===========================手工使用maven=============================================================================
  > 
  > # maven项目的目录结构：在工程的src类路径下进行maven项目结构的搭建：（用nova.rar就行）
  > ---------------------------------------------------
  > xx项目目录
  > 	--src
  > 		--main（主程序目录）
  > 			--java（主程序代码，这就是类路径了）
  > 			--resources（主程序的配置文件，这也是类路径）
  > 		--test（测试程序目录）
  > 			--java（测试程序）
  > 			--resources（测试程序的配置文件）
  > 	--pom.xml（重要：maven工程的核心文件，没有它就不是maven工程，它和项目中的src平级）
  > -----暂时pom中这样写：
  > <?xml version="1.0" encoding="UTF-8"?>
  > <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  > 	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  > 
  > 	<!-- maven的POM模型对象的版本，对于maven2.x和maven3.x来说，它只能是4.0.0版本 -->
  > 	<modelVersion>4.0.0</modelVersion>
  > 	
  > 	<!-- 自己项目的坐标，这些只决定，将来只决定将来安装在仓库中的目录结构；-->
  > 	<groupId>com.itheima</groupId><!-- java目录下的源代码的包结构通常和groupId结构保持一致，不一致也没事 -->
  > 	<artifactId>nova</artifactId><!-- 工程名通常和我们建的maven模块的模块名保持一致，不一致也没事 -->
  > 	<version>1.1.0-SNAPSHOT</version>
  > 	<packaging>war</packaging>
  > 	
  > 	<!-- 项目的依赖 -->
  > 	<dependencies>
  > 		<dependency>
  > 			<!-- 别人项目的坐标 -->
  > 			<groupId>mysql</groupId>
  > 			<artifactId>mysql-connector-java</artifactId>
  > 			<version>8.5.10</version>
  > 			<!-- 依赖的生效范围，后面再说 -->
  > 			<scope>compile</scope>
  > 		</dependency>
  > 		...
  > 	</dependencies>
  > </project>
  > ---------------------------------------------------然后在项目中写个主程序和测试程序，再进行maven的构建
  > 
  > #  <!--手动激活profiles的列表，按照profile被应用的顺序定义activeProfile。 该元素包含了一组activeProfile元素，每个activeProfile都含有一个profile id。任何在activeProfile中定义的profile id，不论环境设置如何，其对应的        profile都会被激活。如果没有匹配的profile，则什么都不会发生。例如，env-test是一个activeProfile，则在pom.xml（或者profile.xml）中对应id的profile会被激活。如果运行过程中找不到这样一个profile，Maven则会像往常一样运行。 -->   <activeProfiles>    <activeProfile>env-test</activeProfile>   </activeProfiles></settings>XML
  > 	刚开始maven仓库是空的，执行这些命令时就会下载maven命令需要的插件jar包和项目需要的依赖jar包到本地仓库中；
  > 		mvn clean：清理；把之前项目编译的东西都删除掉，为新的编译代码做准备，也就是删掉target目录；
  > 		mvn compile：编译主程序；将主程序源代码编译为可执行代码，放在target/classes目录下；
  > 		mvn test-compile：编译测试程序；
  > 		mvn test：测试；用测试代码对主程序进行测试，其中测试的字节码放在target/test-classes目录下；测试结果在surefire-reports目录中；
  > 		mvn package：打包；将项目主程序进行打包（jar/war），放到target目录的压缩文件中；
  > 		mvn install：安装；将生成的jar/war文件安装到“本地仓库”；
  > 		mvn deploy：部署；只将jar/war部署maven“私服”上，这里的部署并不是将war放在tomcat中；（后面再说）
  > 		（下面的执行了对应上面的某些阶段也会自动执行）
  > 	我们执行mvn compile后等待，出现【BUILD SUCCESS】就说明ok了；成功后我们发现，pom同级目录中出现了一个文件target，
  > 	这就是存放，我们操作完成的生成物的；target下有一个classes是存放主程序类文件的；
  > 	以后我们会用linux环境下的maven来将项目源码进行编译部署和打包的操作；
  > 
  > # 刚刚我们是手工创建maven工程的目录结构，很麻烦，也可以使用maven提供的命令来做：mvn archetype:generate命令使用maven工程模板来创建maven项目目录；
  > 	后面可以跟参数：-DgroupId=组织id -DartifactId=项目名 -Dversion=版本号-snapshot -DarchetypeArtifactId=模板名称 -DinteractiveMode=false
  > 	模板名称通常有两个选择，web工程用【maven-archetype-webapp】，java工程用【maven-archetype-quickstart】
  > 	（注意：这个命令必须在maven工程外面执行，不能在出现pom的目录中执行）
  > 
  > # 如果是打war包可能会出现war包插件和jdk17版本不匹配，需要在pom中指定war插件的版本：（后面再说插件的配置<plugin>）
  > 	<build>
  > 		<plugins>
  > 			<plugin>
  > 				<groupId>org.apache.maven.plugins/groupId>
  > 				<artifactId>maven-war-plugin</artifactId>
  > 				<version>3.2.2</version>
  > 			</plugin>
  > 		</plugins>
  > 	</build>
  > 
  > ------------------------------------------接下来在idea中使用maven-------------------------------------------------------------------
  > 
  > # idea中内置了一个maven，但是我们不用这个不方便，我们用自己的maven，在idea中关联maven；设置-BuildTools-maven-mavenhome和mavensettings；
  > 	然后创建maven模块，以后创建模块都是用maven了；构建项目用右边maven窗口来做，也可以在idea运行栏中进行配置；
  > ```

- 2

  > ```tex
  > ============================================关于pom文件的配置=====================================================================
  > 
  > # 依赖：pom文件中的依赖在<dependencies></dependencies>标签中通过<dependency>来配置，里面写坐标和一些配置信息，这些直接在中央仓库网站copy；
  > # 依赖传递：依赖具有传递性；如果依赖jar包中还用到了其他项目依赖，那么maven会自动将这些依赖都导入，这些此时你的项目也可以使用这些依赖；
  > 	*如果我不想用对方项目中的某个依赖，不要依赖传递怎么办，可以进行依赖断开；在引入的依赖的<dependency>中添加：
  > 			<exclusions>
  > 				<exclusion>
  > 					<groupId>log4j</groupId>
  > 					<artifactId>log4j</artifactId>
  > 					//不需要写版本
  > 				</exclusion>
  > 				...
  > 			</exclusions>
  > 	*如果不希望对方通过依赖传递来使用我项目中的依赖，可以将我用的依赖设置为可选的，在我的依赖标签<dependency>中添加<optional>true</optional>；
  > # 依赖范围scope：依赖的jar默认可以在任何地方使用是compile，可以通过依赖中的<scope>标签来设置依赖的使用范围，可选值有：
  > 		compile所有范围（log4j）；provided已经提供，仅在编译、测试程序运行范围内有效，不参与打包（servlet-api）；runtime仅主程序、测试程序运行时有效，编译时无效，参与打包（jdbc驱动）；test仅在测试程序运行时有效，不参与编译和打包（junit）；（了解）system表示不用仓库中的，用本地磁盘上的jar包依赖，需要再加<systemPath>标签指定jar包在本地磁盘的路径；编译、测试运行环境有效，不参与打包；import后面再说
  > # 依赖冲突：如果多个依赖之间有冲突，那么默认会使用离自己近的，如果一样近，pom中先声明的有优先；
  > 
  > --------------------------------------------------------------------------------------------------------------------------------------
  > # 生命周期：maven对项目构建的生命周期分为3套，clean清理工作，default核心工作（编译、测试、打包、安装、部署），site产生报告发布站点等；（同一生命周期内，执行后面的命令，前面的所有命令会自动执行）
  > 	clean：包含pre-clean清理之前做的事、clean进行清理、post-clean清理之后做的事；
  > 	default：从validate校验、compile编译，一直到deploy部署阶段，每一个阶段都对应做一些事（中间还有很多阶段这里不一一列举），我们指定执行某个阶段会将前面的阶段都执行，这些事都是通过maven的插件来做的；我们也可以通过配置插件，指定它在哪个阶段上执行；
  > 	site：presite、site生成站点文档、post-site、site-deploy文档部署到指定服务器上；
  > 
  > # 插件：它和生命周期的阶段绑定，在执行对应的生命周期时会执行对应的一些插件，也可以自定义在某个生命阶段上执行指定插件；
  > 	pom中配置插件需要在<build>标签中的<plugins>中配置插件：
  > -------------------------------
  > <build>
  > 	<plugins>
  > 		<!-- 对源码打包的插件 -->
  > 		<plugin>
  > 			<groupId>org.apache.maven.plugins</groupId>
  > 			<artifactId>maven-source-plugin</artifactId>
  > 			<version>3.1.1</version>
  > 
  > 			<!-- 该标签用于指定，插件的执行阶段 -->
  > 			<executions>
  > 				<execution>
  > 					<!-- 执行目标 -->
  > 					<goals>
  > 						<!-- 对主程序打包 -->
  > 						<goal>jar</goal>
  > 						<!-- 对测试程序打包 -->
  > 						<goal>test-jar</goal>
  > 					</goals>
  > 					<!-- 阶段名 -->
  > 					<phase>generate-test-resources</phase>
  > 				</execution>
  > 				...
  > 			</executions>
  > 		</plugin>
  > 		...
  > 	</plugins>
  > 	...
  > </build>
  > -------------------------------
  > 
  > # maven的属性<properties>：maven的<properties>中的属性有5大类；（该标签不只可以定义在pom中，还可以在pom的某些标签内）
  > 	1、自定义属性：就是在<properties>自定义一个标签，然后pom文件后使用${标签名}来引用；
  > 	2、内置属性：有一些内置的属性直接用project.来调用，${project.version}就是使用<version>标签的内容；${basedir}是当前pom所在目录的绝对路径，project可省；
  > 	3、Settings属性：获取maven的配置文件settings.xml中的值，用${settings.localRepository}；
  > 	4、Java系统属性：Java可以System.getProperties()获取的属性名，都可以在${}中获取；
  > 	5、环境变量属性：也可以在${env.环境变量名}中获取windows系统的环境变量；系统属性也可以在cmd中通过mvn help:system来拿，里面有环境变量和Java系统属性；
  > 	例如：
  > ------------------------
  > <properties>
  > 	<!-- 设置当前maven，构建项目采用的编码 -->
  > 	<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  > 
  > 	<!-- 告诉mavenjdk的版本 -->
  > 	<maven.compiler.source>17</maven.compiler.source>
  > 	<maven.compiler.target>17</maven.compiler.target>
  > 
  > 	<!-- 自定义的全局属性，在后面的junit依赖中，版本号可以用${junit.version}来指定 -->
  > 	<junit.version>5.0.1</junit.version>
  > </properties>
  > ------------------------
  > 
  > # maven的构建<build>：它本身有默认的配置，也可以通过在build标签中，自定义构建过程；
  > 
  > 	*指定打包文件的文件名：我们打包的jar和war包都有默认的文件名是【artifactId-version.jar】，我们也可以通过<final-name>标签指定，注意加文件后缀；
  > 
  > 	*指定配置文件路径：<resources>标签用于配置项目的资源文件（如配置文件、静态资源文件等）的位置和处理方式。这个标签通常用于指定项目构建时需要复制到输出目录的资源文件。
  > 		就是告诉maven，主程序配置文件和测试程序配置文件去哪里找。
  > 		当你显式地指定了<resource>标签时，Maven会只处理你指定的资源文件，就不会再去默认的src/main/resources目录下复制文件了。
  > ------------------------主程序配置在哪里：
  > <resources>
  > 	<!-- resource是告诉maven主程序配置文件在哪个目录去找 -->
  > 	<resource>
  > 		<directory>配置文件目录，如【${project.basedir}/src/main/resources】</directory>
  > 
  > 		<!-- 指定包含的文件；excludes是不包含的文件。如果没有指定<includes>或<excludes>，那么<resources>标签中指定的<directory>目录下的
  > 			所有文件都会被复制到输出目录。这意味着所有文件都会被处理，包括子目录中的文件。-->
  > 		<includes>
  > 			<!-- 表示java目录下的，所有的xml和properties文件。，路径支持ant风格 -->
  > 			<include>**/*.properties</include>
  > 			<include>**/*.xml</include>
  > 		</includes>
  > 		<!--指定复制到哪里，不指定的话默认复制到生成的classes类路径下-->
  > 		<targetPath>xx/xx</targetPath>
  > 		<!-- 启用过滤器，表示该配置文件中启用占位符，${}在其中可以用了 -->
  > 		<filtering>true</filtering>
  > 	</resource>
  > </resources>
  > ------------------------类似的，指定测试程序的配置文件位置：
  > <testResources>
  > 	<!-- testResource是告诉maven测试程序配置文件在哪个目录去找 -->
  > 	<testResource>
  > 		<directory>配置文件目录，如【${project.basedir}/src/test/resources】</directory>
  > 
  > 		<!-- 启用过滤器，表示该文件启用占位符，${}在其中可以用了 -->
  > 		<filtering>true</filtering>
  > 	</testResource>
  > </testResources>
  > ------------------------
  > 
  > 	*设置插件：使用<plugins>标签，在其中可以自定义使用的插件，以及对插件进行配置；
  > ------------------------
  > <plugins>
  > 	<!-- 对源码打包的插件 -->
  > 	<plugin>
  > 		<groupId>org.apache.maven.plugins</groupId>
  > 		<artifactId>maven-compiler-plugin</artifactId>
  > 		<version>3.1.1</version>
  > 
  > 		<!-- 对插件进行配置 -->
  > 		<configuration>
  > 			<!-- 源码是Java17，目标class也是Java17，编码采用UTF-8 -->
  > 			<source>17</source>
  > 			<target>17</target>
  > 			<encoding>UTF-8</encoding>
  > 		</configuration>
  > 	</plugin>
  > </plugins>
  > ------------------------
  > 
  > # 多环境配置：你的程序在测试时要用a配置文件，在运行时要用b配置文件，那么换环境还得改怎么办，我们在maven的pom中配置多环境；
  > ------------------------
  > <!-- 配置多环境 -->
  > <profiles>
  > 	<!-- 环境1 -->
  > 	<profile>
  > 		<id>env-1</id>
  > 		<!-- 环境1的属性 -->
  > 		<properties>
  > 			<jdbc-dev>jdbc:mysql://localhost:3306/crm</jdbc-dev>
  > 		</properties>
  > 
  > 		<!-- 配置默认启动，没有参数运行的话默认用该环境 -->
  > 		<activation>
  > 			<activeByDefault>true</activeByDefault>
  > 		</activation>
  > 	</profile>
  > 	<!-- 环境2 -->
  > 	<profile>
  > 		<id>env-2</id>
  > 		<!-- 环境2的属性 -->
  > 		<properties>
  > 			<jdbc-dev>jdbc:mysql://localhost:3306/test</jdbc-dev>
  > 		</properties>
  > 	</profile>
  > </profiles>
  > ------------------------
  > 	环境怎么用呢，在idea的启动项里配置maven运行的参数【clean -P 环境id】就行了；idea里maven命令不用写前面的【mvn】
  > 
  > # 跳过测试：idea中mvn面板可以直接点击跳过，也可以用命令【mvn install -D skipTests】跳过所有测试；
  > 	还可以在<build>中通过配置测试插件：<plugin>中除了测试插件surefire的坐标，再加<configuration>，里面写<skipTests>true</skipTests>即可；
  > 	我们常用的是，指定执行哪些测试类，或不测试哪类，在<configuration>中写<includes>或<excludes>，如：
  > ------------------------
  > <includes>
  > 	
  > </includes>
  > 
  > <plugins>
  > 	<!-- 测试的插件 -->
  > 	<plugin>
  > 		<groupId>org.apache.maven.plugins</groupId>
  > 		<artifactId>maven-surefire-plugin</artifactId>
  > 		<version>3.1.1</version>
  > 
  > 		<!-- 对插件进行配置 -->
  > 		<configuration>
  > 			<skipTests>true</skipTests>
  > 			<includes>
  > 				<include>**/UserServiceTest.java</include>
  > 			</includes>
  > 		</configuration>
  > 	</plugin>
  > </plugins>
  > ------------------------
  > ```

- 3

  > ```tex
  > ================================================maven继承和聚合=====================================================================
  > 
  > # 我们之前的项目都是一个模块下多个包，pojo、service、dao、controller每层都是一个包，但是我们多模块开发的场景下，每一层都是一个module，
  >     模块之间的调用通过接口；这种非整合开发情况下，依赖和构建工作就更复杂了，所以我们通过maven来管理整个工程的所有模块的依赖和构建；
  > 
  > # pom文件（项目对象模型）是可以被继承的，maven多模块管理其实就是让子模块的pom来继承父工程的pom，以此来完成模块之间继承关系；（注意：父子模块必是maven模块）
  > 
  > # maven的继承是指，在maven项目中，让一个模块使用另一个模块中的配置和依赖信息。继承可以让我们在多个模块中，共享同一个配置和依赖，简化项目的管理和维护；
  >     多个模块中，我们只在父模块pom中统一配置依赖信息，子模块继承父模块，它的pom就会继承父模块的pom，这样子模块就可以用父模块的依赖和配置了，方便管理了；
  > 
  > # 被继承的maven父工程/模块必须满足2点：1、<packaging>标签设置为pom；2、src必须删除掉，父模块没有src；
  > 
  > # 如何继承：创建一个maven工程，然后src删掉，在父工程的pom中指定<packaging>为pom；然后创建子模块（也是maven模块），添加maven子模块时指定parent为父工程；
  >     而且此时只能设置项目名artifactId，且不能和其他模块重复；
  >         *父工程的pom中多了一个<modules>标签，里面一个个的<module>是父工程包含的所有的“直接子模块”，父工程聚合了子模块；
  >         *此时子模块的pom文件会多一个<parent>标签，里面是父maven工程的gav坐标，子模块继承了父工程；
  >     这样父工程的pom就被子模块的pom所继承了，父子模块之间就完成了模块之间的继承关系，此时对父工程进行构建，其下的所有子模块都会依次进行构建，这就是聚合和继承；
  > （子模块也可以有子模块，仍然要遵循以上2点要求；层级可以无穷多，但一般开发中就2层）
  > （如果忘了给子模块设置parent，那就手动在子模块pom中添加<parent>标签，然后手动在父模块中添加<modules>标签就行；子模块的坐标只留artifactId）
  > （同级结构的maven模块之间也可以继承，方式和上面类似；只是子模块的<parent>标签中需要加<relativePath>标签，指明父模块pom文件的相对路径）
  > 
  > # 如果每个模块都用到了同一个依赖，但各自的版本不同，怎么办？子模块的依赖去掉，直接在父工程的pom中添加依赖就行了；此时子模块无条件地继承父工程的所有依赖；
  >     但是子模块并不是需要父工程中所有的依赖，这样打的包很冗余，怎么办？此时父工程就要对依赖进行加强管理了；
  >     *在父工程中写<dependencyManagement>标签，将<dependencies>依赖标签放在这个标签里，所有的依赖加强管理，此时子模块全都用不了这些依赖了；
  >         如果哪个子模块需要用某个依赖，在它自己的pom中配置依赖即可，别写<version>版本标签，相当于告诉父工程我需要哪个依赖，此时子模块中才会出现这些依赖；
  >         如果子模块依赖中指定了版本号，那么相当于就没用父工程的依赖，这个依赖没受到父工程的管理；
  >     *插件<plugins>也应该被父工程管理起来，类似的用<pluginManagement>来将它包起来；
  >     *依赖里面也可以写此时自己正在写的某个模块的坐标，同样在其他模块中可以被使用；
  > 
  > # 通常我们在父工程的pom中的<properties>标签中定义“全局属性”，比如：某个依赖的版本号，定义为依赖的【artifactId-version】；
  >     如果artifactId就一个单词，也可以【artifactId.version】；
  > 
  > （可以被继承的标签有：<groupId>、<version>、<url>、<properties>、<dependencies>、<dependencyManagement>、<repositories>、<build>）
  > 
  > 
  > 
  > ================================================maven私服=====================================================================
  > 
  > 
  > # maven私服是特殊的服务器，它除了maven官方服务器、其他大型maven云之外的，私人用的服务器软件；它是架设在局域网中的个人maven服务器软件，
  >     用来代理位于外部的中央仓库；maven私服不仅可以在局域网中使用，也可以部署在外网上供其他人使用；
  > 
  > # 常见的maven私服有：apache的Archiva，jFrog的Artifactory，Sonatype的Nexus；（当下最流行，使用最广泛的，阿里云就是Nexus）
  > 
  > # 安装即解压，解压后有两个文件夹【nexus-3.54.1-01】私服的程序目录和【sonatype-work】私服的工作目录；以管理员运行cmd，cd进入安装目录下的bin，
  >     执行【nexus.exe /run】启动nexus服务器然后等待，最后的参数是服务器名；当看到Started Sonatype Nexus OSS 3.54.1-01则表示成功；
  >     否则就需要删掉这俩目录重新解压执行；etc目录下nexus的默认配置文件中可以看到默认的配置，访问的端口号等信息，可以修改；服务器性能相关的配置
  >     在bin中nexus.vmoptions中可以设置；
  > （私服仓库里的jar包都是二进制文件，别想着在windows目录中找到jar，只能通过私服网页去看）
  > 
  > -----------------------------start------------------------------------------------------------------------------
  > 
  > # nexus本质上是一个web工程，所以我们在浏览器中输入【http://localhost:8081/】就可以了，如果访问很慢，可以在命令行【Ctrl+c】关闭服务然后重启；
  >     *对nexus进行初始化：在主页右上角登录，用户名是admin，密码看提示；然后提示设置新密码(a123456789)，下一步是设置匿名访问，我们这里禁止匿名访问；
  >     *完成后推出重新登录，左边的search是查找jar的，upload是上传下载用的，browse是浏览全部的仓库资源；上边六边形盒子是浏览用的，旁边的齿轮是设置服务器的；
  >     *首先在主页点browse浏览就可以看到当前私服上的各种仓库，默认有7个；我们主要用4个maven开头的仓库，剩下的3个是.net的仓库不用管；
  > 
  > # maven-central：它的类型是proxy远程仓库的代理，本质上就是一个链接，它帮我们去中央仓库下载jar包的，我们可以通过设置该仓库的链接地址，将链接的中央仓库地址
  >     改成阿里云的镜像；在设置中找到仓库，双击进去设置，找到Remote Storage，将里面的【https://repo1.maven.org/maven2/】中央仓库改成阿里云镜像地址即可；
  >     maven-public：它的类型是group组仓库，它默认存放从中央仓库下载的jar包；组的意思就是，拿东西就直接从类型是group的“组仓库”中拿，别管具体是哪个仓库的，都在组里；
  >     maven-releases：它的类型是hosted本地仓库，我们自己开发的稳定版项目jar包放在这里；hosted类型的仓库是我们上传jar包的仓库，或者不开源的jar包；
  >     maven-snapshots：同样类型是hosted本地仓库，但是它是存放我们的测试版jar包；这俩仓库都默认在maven-public的组中，所以拿东西也会在这两个仓库中找；
  > （现在这几个仓库中没有任何内容）
  > 
  > # 但是现在还不行，我们本地仓库配置的中央仓库镜像是阿里云的，也就是说本地没有的直接去阿里云远程下载了，我们想让它链接到私服下载；在maven的settings.xml中配置：
  > 
  >     *我们私服有密码，所以先在<servers>中配置私服的密码：
  > -------------
  > <!-- 访问该id的仓库时，使用的用户名和密码；server可以有多个 -->
  > <server>
  >     <!-- 设置访问某个私服的id和密码，id自定义，下面上传要用 -->
  >     <id>mynexus</id>
  >     <username>admin</username>
  >     <password>密码</password>
  > </server>
  > -------------
  > 
  >     *找到mirrors标签，将中央仓库镜像改成我们私服的，访问资源先去私服找：
  > -------------
  > <mirror>
  >     <!-- id要和上面server中id保持一致，用于携带，访问私服的密码 -->
  > 	<id>mynexus</id>
  > 	<mirrorOf>central</mirrorOf>
  > 	<name>Nexus central</name>
  > 	<url>http://localhost:8081/repository/maven-public/</url><!-- 找的话就到该url对应仓库的组中找，找这一组仓库看有没，没有再去中央仓库下载 -->
  > </mirror>
  > -------------此时私服中没有，私服会通过之前设置过的，中央仓库的镜像去找；这样就完成了仓库的3级查找；
  > 
  > 
  > # 我们怎么将自己的项目install安装到私服上呢？在maven项目的pom文件中设置：
  >     <distributionManagement>
  >         <snapshotRepository>
  >             <id>写私服的id，发布到哪个私服上</id>
  >             <name>随意</name>
  >             <!-- 写发布到私服的哪个仓库的url -->
  >             <url>http://localhost:8081/repository/maven-snapshots/</url>
  >         </snapshotRepository>
  >         <repository>
  >             <id>mynexus</id>
  >             <name>Nexus Release</name>
  >             <url>http://localhost:8081/repository/maven-releases/</url>
  >         </repository>
  >     </distributionManagement>
  > 
  > # 除了默认的中央仓库，我们还可以配置多个远程仓库；<repositories>标签就是设置多个远程仓库的地址的：
  >     <repositories>
  >         <!- 私服依赖 -->
  >         <repository>
  >             <!-- 私服的id，需要和<server>的保持一致，用于给私服出示密码 -->
  >             <id>mynexus</id>
  >             <name>随意</name>
  >             <url>私服中某个仓库的url</url>
  >         </repository>
  > 
  >         <!-- 我们能否使用正式/测试的依赖 -->
  >         <snapshots>
  >             <enabled>false</enabled>//如果是true就表示，我们如果用了snapshot型的jar包，那么每次使用都会去远程仓库看看版本更新了没，更新就下载最新版本用
  >         </snapshots>
  >         <releases>
  >             <enabled>true</enabled>
  >         </releases>
  >     </repositories>
  > ```

