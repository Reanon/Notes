# MyBatis  入门

## 一、框架概述

### 1.1 软件开发常用结构

#### 三层架构

三层架构包含的三层： 

1、界面层（User Interface layer），也叫表示层或视图层

- 主要功能是接受用户的数据，调用service，显示请求的处理结果。 包含了jsp，html，servlet等对象。 对应的包controller；
- 使用 web 页面和 用户交互，手机 app 也就是表示层的，用户在 app 中操作，业务逻辑在服务器端处理。 

2、业务逻辑层（Business Logic Layer）

- 接收表示传递过来的数据，检查数据，计算业务逻辑，调用数据访问层获取数据。
- 处理业务逻辑， 使用算法处理数据的。 把数据返回给界面层。 
- 对应的是==service包==，和包中的很多的XXXService类。 例如：  StudentService ， OrderService, ShopService 

3、持久层（数据访问层）（Data access layer）

- 与数据库打交道。主要实现对数据的增、删、改、查。将存储在数据库中的数据提交给业务层，同时将业务层处理的数据保存到数据库。
- 访问数据库，或者读取文件，访问网络，获取数据。 
- 对应的包是==dao==。 dao包中很多的StudentDao,  OrderDao,  ShopDao等等。

三层的处理请求的交互：

- 用户 ---> 界面层 ---> 业务逻辑层 ---> 数据访问层（持久层） ---> DB 数据库

![image-20210219102256852](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210219102256.png)

每一层对应着一个框架

- 界面层---SpringMVC框架
- 业务层---Spring框架
- 持久层---MyBatis框架

MVC：web开发中，使用 MVC 架构模式，m：数据， v：视图， c：控制器。

- m数据： 来自数据库mysql， 来自文件，来自网络
- v视图： 现在使用jsp， html，css，js。 显示请求的处理结果，把m中数据显示出来。
- c控制器： 接收请求，调用service对象，显示请求的处理结果。 当前使用servlet作为控制器



#### 三层架构优点

- 结构清晰、耦合度低，各层分工明确 
- 可维护性高，可扩展性高 
- 有利于标准化 
- 开发人员可以只关注整个结构中的其中某一层的功能实现 
- 有利于各层逻辑的复用

#### 框架

常见的 J2EE 中开发框架：

1、**MyBatis 框架**

MyBatis 是一个优秀的基于 java 的==持久层框架==，内部封装了 jdbc，开发者只需要关注 sql 语句本身，而不需要处理加载驱动、创建连接、创建 statement、关闭连接，资源等繁杂的过程。

MyBatis 通过 `xml` 或 注解两种方式将要执行的各种 sql 语句配置起来，并通过 java 对象和 sql 的动态参数进行映射生成最终执行的 sql 语句，最后由 mybatis 框架执行 sql 并将结果映射为 java 对象并返回。

2、**Spring 框架**

Spring 框架为了解决软件开发的复杂性而创建的。Spring 使用的是基本的 JavaBean 来完成以前非常复杂的企业级开发。

Spring 解决了业务对象，功能模块之间的耦合，不仅在 javase，web 中使用， 大部分 Java 应用都可以从 Spring 中受益。 Spring 是一个轻量级==控制反转(IoC)==和==面向切面(AOP)==的容器。

3、**SpringMVC 框架** 

Spring MVC 属于 SpringFrameWork 3.0 版本加入的一个模块，为 Spring 框架提供了构建 Web 应用程序的能力。

现在可以用 Spring 框架提供的 SpringMVC 模块实现 web 应用开发，在 web 项目中可以无缝使用 Spring 和 Spring MVC 框架。

### 1.2 框架是什么

#### 框架定义

框架（Framework）是整个或部分系统的可重用设计，表现为一组抽象构件及构件实例间交互的方法；

- 另一种认为，框架是可被应用开发者定制的应用骨架、模板。 简单的说，框架其实是半成品软件，就是一组组件，供你使用完成你自己的系统。
- 从另一个角度来说框架一个舞台，你在舞台上做表演。在框架基础上加入你要完成的功能。 框架安全的，可复用的，不断升级的软件。 

简而言之：框架（framework）就是一个软件， 完成了部分的功能。  软件中的类和类之间的方法调用都已经规定好了。 通过这些可以完成某些功能。  框架看做是模版。   

- 框架是可以升级的，改造的，框架是安全的。
- 框架是对某一个方面有用的，不是全能的。 

#### 框架解决的问题

框架要解决的最重要的一个问题是**技术整合**， J2EE 的框架中，有着各种各样的技术，不同的应用，系统使用不同的技术解决问题。能够**提供开发的效率**， 降低难度

需要从 J2EE 中选择不同的技术，而技术自身的复杂性，有导致更大的风险。企业在开发软件项目时，主要目的是解决业务问题。 即要求企业负责技术本身，又要求解决业务问题。这是大多数企业不能完成的。

框架把相关的技术融合在一起，企业开发可以集中在业务领域方面。 另一个方面可以提供开发的效率。

###  1.3 JDBC 编程

#### JDBC 回顾

```java
public void findStudent() {
    Connection conn = null;
    Statement stmt = null;
    ResultSet rs = null;
    try {
        // 1、注册 mysql 驱动
        Class.forName("com.mysql.jdbc.Driver");
        // 2、连接数据的基本信息url、username、password
        String url = "jdbc:mysql://localhost:3306/springdb";
        String username = "root";
        String password = "123456";
        // 3、创建连接对象
        conn = DriverManager.getConnection(url, username, password);
        // 4、保存查询结果
        List<Student> stuList = new ArrayList<>();
        // 5、创建 Statement, 用来执行 sql 语句
        stmt = conn.createStatement();
        // 6、执行查询，创建记录集，
        rs = stmt.executeQuery("select * from student");
        while (rs.next()) {
            Student stu = new Student();
            stu.setId(rs.getInt("id"));
            stu.setName(rs.getString("name"));
            stu.setAge(rs.getInt("age"));
            // 7、从数据库取出数据转为 Student 对象，封装到 List 集合
            stuList.add(stu);
        }
    } catch (Exception e) {
        e.printStackTrace();
    } finally {
        try {
            // 8、关闭资源
            if (rs != null) ;
            {
                rs.close();
            }
            if (stmt != null) {
                stmt.close();
            }
            if (conn != null) {
                conn.close();
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

#### JDBC 优缺点

优点

- 直观，好理解

缺点

1. 代码比较多，开发效率低 
2. 需要关注 Connection、Statement、ResultSet 对象创建和销毁 
3. 对 ResultSet 查询的结果，需要自己封装为 List
4. 重复的代码比较多些 
5. 业务代码和数据库的操作混在一起

### 1.4 MyBatis 框架概述

MyBatis 本是 apache 的一个开源项目 iBatis，2010 年这个项目由 apache software foundation 迁移到了 google code，并且改名为 MyBatis 。2013 年 11 月迁移到 Github。

iBATIS 一词来源于"internet"和"abatis"的组合，是一个基于 Java 的持久层框架。iBATIS 提供的持久层框架包括 SQL Maps 和 Data Access Objects（DAOs）。

#### MyBatis 解决的主要问题

1. 减轻使用 JDBC 的复杂性，不用编写重复的创建 Connetion、Statement；
2. 不用编写关闭资源代码。 直接使用 java 对象，表示结果数据。
3. 让开发者**专注 SQL 的处理**。 其他分心的工作由 MyBatis 代劳。

MyBatis 可以完成： 

1. 注册数据库的驱动，例如 `Class.forName("com.mysql.jdbc.Driver"));` 
2. 创建 JDBC 中必须使用的 Connection、Statement、ResultSet 对象 
3. 从 xml 中获取 sql，并执行 sql 语句，把 ResultSet 结果转换 java 对象
4. 关闭资源：ResultSet.close()、Statement.close() 、Conenection.close()
5. 实现sql语句和java代码的解耦合

```java
List<Student> list = new ArrayLsit<>();	
// 执行查询
ResultSet rs = state.executeQuery("select * from student");
while(rs.next){
    Student student = new Student();
    student.setName(rs.getString("name"));
    student.setAge(rs.getInt("age"));
    list.add(student);
}
```

## 二、MyBatis 快速入门

> mybatis的文档： https://mybatis.org/mybatis-3/zh/index.html

### 2.1 入门案例

实现步骤：

1. 创建student表（id，name，email，age）
2. 新建maven项目
3. 修改pom.xml 
   - 加入依赖 mybatis依赖， mysql驱动， junit
   - 在`<build>`加入资源插件
4. 创建实体类Student，定义属性， 属性名和列名保持一致
5. 创建 Dao 接口， 定义操作数据库的方法。
6. 创建xml文件（==mapper 文件==）， 写sql语句。 
   - mapper 文件：定义和 dao 接口在同一目录， **一个表对应一个mapper文件**。
   - mybatis 框架推荐是把 sql 语句和 java 代码分开
7. 创建 mybatis 的**主配置文件**（xml文件）：有一个， 放在 resources 目录下
   - 定义创建连接实例的数据源（DataSource）对象
   - 指定其他 mapper 文件的位置
8. 创建测试的内容。
   - 使用main方法，测试mybatis访问数据库
   - 也可以使用junit 访问数据库

#### MyBatis 环境配置

环境配置：

- 前提知识：JDBC、Mysql、Java基础、Maven、Junit
- 学习环境配置(2021.2.19)：JDK 1.8、Mysql 5.7、maven 3.6.3、IDEA

**获取 Mybatis**

1、Github仓库下载：https://github.com/mybatis/mybatis-3/releases

2、maven仓库：https://mvnrepository.com/artifact/org.mybatis/mybatis

![image-20210219094410288](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210219103544.png)

```xml
<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.6</version>
</dependency>
```

#### 创建 mysql 数据库和表

数据库名 ssm ；表名 student

![image-20210219103929727](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210219103929.png)

```mysql
CREATE TABLE `student` (
    `id` int(11) NOT NULL ,
    `name` varchar(255) DEFAULT NULL,
    `email` varchar(255) DEFAULT NULL,
    `age` int(11) DEFAULT NULL,
    PRIMARY KEY (`id`) -- 指定主键
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

插入数据

![image-20210317154551021](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210317154551.png)

#### 创建 maven 项目

文件结构

- domin 下的 Student.java 对应数据库中的 Student 表
- dao 包下的 StudentDao.java 接口定义操作 定义操作数据库的方法
- dao 包下的 StudentDao.xml 定义操作数据库 Student 表的 sql 语句

![image-20210317152332027](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210317152332.png)

1、创建 maven 工程，首先新建一个空项目

- 设置项目名称

![image-20210317114848664](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210317114848.png)



2、配置工程环境

- 检查Maven 环境

![image-20210317115041896](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210317115042.png)

- 修改 IDEA 中 Maven VM Option 的值
  - 感觉可以不用设置

```bash
# 使其不再远程下载所需的 xml 文件
-DarchetypeCatalog=internal
```

![image-20210317115515748](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210317115515.png)

- 设置编码方式

![image-20210317115606967](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210317115607.png)

3、创建Module

![image-20210317115805698](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210317115805.png)



- 设置Module 名
  - 上面的是项目名
  - 下面的是包名

![image-20210317115937479](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210317115937.png)

4、设置 Module 环境

![image-20210317120233573](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210317120233.png)

![image-20210317120322144](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210317120322.png)

#### 配置 Maven 环境

1、新建 resource 文件夹，删除包下的 APP.java

![image-20210317120521705](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210317120521.png)



2、初始化 pom.xml

- 删除 `<name> .. /name>`，和`<build>` 下的 `<pluginManagement>`
- 修改 `<maven.compiler.source>` 为 1.8

![image-20210317120926966](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210317120927.png)

3、加入依赖 mybatis依赖、mysql驱动、 junit

```xml
<dependencies>
    <!-- junit -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.11</version>
        <scope>test</scope>
    </dependency>
     <!-- mybatis 对应的包 -->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.1</version>
    </dependency>
     <!-- mysql 的包-->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.9</version>
    </dependency>
</dependencies>
```

4、加入 maven 插件

```xml
<build>
    <resources>
        <resource>
            <directory>src/main/java</directory><!--所在的目录-->
            <includes>
                <!--包括目录下的.properties,.xml 文件都会扫描到-->
                <!--不加的话无法生成 .xml 文件-->
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>false</filtering>
        </resource>
    </resources>
    
    <plugins>
        <!-- 定义maven的编译环境为 Java 1.8 -->
        <plugin>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.1</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
            </configuration>
        </plugin>
    </plugins>
</build>
```

> 完全体的 pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <!-- 当前项目的坐标 -->
    <groupId>com.xxxx</groupId>
    <artifactId>ch01-frist</artifactId>
    <version>1.0</version>
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>
    
    <!--依赖列表-->
    <dependencies>
        <!--mybatis依赖-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.1</version>
        </dependency>
        <!--mysql驱动-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.9</version>
        </dependency>
        
        <!--单元测试-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.11</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <!--资源插件：处理src/main/java 中的xml 文件-->
        <resources>
            <resource>
                <directory>src/main/java</directory><!--所在的目录-->
                <includes><!--包括目录下的.properties,.xml 文件都会扫描到-->
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>false</filtering>
            </resource>
        </resources>
        <!--指定编译器的版本，也可以不用加-->
        <plugins>
            <plugin>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.1</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```



#### 编写程序主体

1、编写 Student 实体类，定义属性， 属性名和列名保持一致

![image-20210317141926347](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210317141926.png)

- 创建包 com.xxxx.domain，包中创建 Student 类

```java
package com.xxxx.domain;
/*
 * Description: 实体类
 */
public class Student {
    // 属性名和列名一样
    private Integer id;
    private String name;
    private String email;
    private Integer age;
    // 省略 set ,get , toString
}
```

2、编写 Dao 接口， 定义操作数据库的方法。

- 创建 com.xxxx.dao 包，创建 StudentDao 接口

```java
package com.bjpowernode.dao;
import com.bjpowernode.domain.Student;
import java.util.List;
/*
* <p>Description: Dao 接口 </p>
* <p>Company: http://www.bjpowernode.com
*/
public interface StudentDao {
    /*查询所有数据*/
    List<Student> selectStudents();
}
```

3、编写 Dao 接口 Mapper 映射文件 StudentDao.xml

- mybatis框架推荐是把sql语句和java代码分开
- ==mapper文件==：定义和dao接口在同一目录， 一个表对应一个mapper文件。

如何编写 Mapper 映射文件：

- 可以参考：[MyBatis 3 入门](https://mybatis.org/mybatis-3/zh/getting-started.html)

![image-20210317142911448](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210317142911.png)

约束文件：http://mybatis.org/dtd/mybatis-3-mapper.dtd

- ==约束文件==作用： 定义和限制当前文件中可以使用的标签和属性，以及标签出现的顺序。

mapper是 **根标签**

- namespace： 命名空间保证是唯一值，必须有值且不能为空。推荐使用 **Dao 接口的全限定名称**。
  - 作用：参与识别sql语句的作用。

在 mapper 里面可以写 `<insert>`,`<update>`,`<delete>`,`<select>`等标签。

- `<insert>`里面是 insert语句，表示执行的insert操作
-  `<update>`里面是 update语句
- `<delete>`里面是 delete语句
- `<select>` 里面是 select语句

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="org.mybatis.example.BlogMapper">
    <select id="selectBlog" resultType="Blog">
        select *
        from Blog
        where id = #{id}
    </select>
</mapper>
```

mapper 文件编写要求：

- 在 dao 包中创建文件 StudentDao.xml 
- 要 StudentDao.xml 文件名称和接口 StudentDao 一样，区分大小写的一样。

查询一个学生Student 语句，**语句末尾没有分号**。

- `<select>`：表示查询操作， 里面是select 语句
- id： 要执行的sql语句的唯一标识， 是一个自定义字符串。
  - 推荐使用 **dao接口中的方法名称**
- resultType：告诉mybatis，执行sql语句，把数据赋值给那个类型的java对象。
  - resultType 的值现在使用的 **java对象的全限定名称** 
- `#{studentId}`：占位符， 表示从java程序中传入过来的数据

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!--命名空间 namespace 建议使用对应 dao 接口的全限定名-->
<mapper namespace="com.xxxx.dao.StudentDao">
    <!--查询一个学生Student-->
    <!--
        <select>: 查询数据， 标签中必须是 select 语句
        id: sql 语句的自定义名称，推荐使用 dao 接口中方法名称；表示要执行的 sql 语句
        resultType: 查询语句的返回结果数据类型，使用全限定类名
    -->
    <select id="selectStudentById" resultType="com.xxxx.domain.Student">
        <!--要执行的 sql 语句-->
        select id,name,email,age from student where id= 1001   <!--句尾无分号-->
    </select>
</mapper>
```



#### 创建 MyBatis 主配置文件

项目 src/main 下创建 resources 目录，设置 resources 目录为 resources root 

创建主配置文件（xml文件）：有一个， 放在resources目录下

- 定义创建连接实例的数据源（DataSource）对象
- ==指定其他mapper文件的位置==
- 主配置文件名称是自定义的，这里名称为 mybatis.xml 

如何写主配置文件

![image-20210317145406340](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210317145406.png)

数据库的路径：`jdbc:mysql://local:3306/[数据库名]`

支持中文的 url ：`jdbc:mysql://localhost:3306/mybatis?useUnicode=true&characterEncoding=utf-8`

- 连接名为 `mybatis` 的数据库
- 由于 xml 中不能使用 &，需要使用对应的实体符 `&amp;`，所以改为：`jdbc:mysql://local:3306/mybatis?useUnicode=true&amp;characterEncoding=utf-8`

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--配置 mybatis 环境-->
    <environments default="development">
        <!--id:数据源的名称-->
        <environment id="development">
            <!--配置事务类型：使用 JDBC 事务（使用 Connection 的提交和回滚）-->
            <transactionManager type="JDBC"/>
            <!--配置数据源：创建数据库 Connection 对象
                type: POOLED 使用数据库的连接池-->
            <dataSource type="POOLED">
                <!--driver:驱动的内容-->
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <!--连接数据库的url-->
                <!--? 之后的内容指定编码格式和字符集 utf-8-->
                <property name="url" value="jdbc:mysql://local:3306/mybatis?useUnicode=true&amp;characterEncoding=utf-8"/>
                <!--用户名-->
                <property name="username" value="root"/>
                <!--密码-->
                <property name="password" value="root123"/>
            </dataSource>
        </environment>
    </environments>

    <!--指定其他mapper文件的位置：其他mapper文件目的是找到其他文件的sql语句-->
    <mappers>
        <!--
           使用mapper的resource属性指定mapper文件的路径,这个路径是从target/classes路径开启的
           使用注意：
              1. resource="mapper文件的路径，使用 / 分割路径"
              2. 一个mapper resource 指定一个mapper文件
        -->
        <!--告诉 mybatis 要执行的 sql 语句的位置-->
        <mapper resource="com/xxxx/dao/StudentDao.xml"/>

        <!--多个mapper 文件，一个一个指定-->
        <!--<mapper resource="com/bjpowernode/dao/OrderDao.xml"/>
        <mapper resource="com/bjpowernode/dao/UserDao.xml"/>-->
    </mappers>
</configuration>
```



#### 创建测试类 MyBatisTest

每个基于 MyBatis 的应用都是以一个 SqlSessionFactory 的实例为核心的。

- SqlSessionFactory 的实例可以通过 `SqlSessionFactoryBuilder` 获得。
- 而 SqlSessionFactoryBuilder 则可以从 XML 配置文件或一个预先配置的 Configuration 实例来构建出 SqlSessionFactory 实例。

在 src/test/java/com/xxxx/ 下创建 MyBatisTest.java 文件

```java
package com.xxxx;

import com.xxxx.domain.Student;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.Test;

import java.io.IOException;
import java.io.InputStream;

/*
 * mybatis 入门
 */
public class MyBatisTest {
    // 测试 Mybatis 执行 sql 语句
    @Test
    public void testSelectStudentById() throws IOException {
        // 调用mybatis某个对象的方法，执行mapper文件中的sql语句
        // mybatis核心类: SqlSessionFactory

        // 1.定义mybatis主配置文件的位置, 从类路径开始的相对路径
        String config = "mybatis.xml";

        // 2.读取主配置文件, 使用mybatis框架中的Resources类
        // 需要抛出异常
        InputStream inputStream = Resources.getResourceAsStream(config);

        // 3.创建 SqlSessionFactory 对象,使用SqlSessionFactoryBuilder类
        // 目的是获取 SqlSession
        SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(inputStream);

        // 4.获取SqlSession对象, SqlSession 能执行 sql 语句
        SqlSession session = factory.openSession();

        // 5.指定要执行的 sql语句的 id
        // sql的 id = namespace+"."+ select|update|insert|delete 标签的id属性值
        String sqlId = "com.xxxx.dao.StudentDao" + "." + "selectStudentById";

        // 6.通过SqlSession的方法，执行sql语句
        Student student = session.selectOne(sqlId);
        System.out.println("使用mybatis查询一个学生：" + student);

        // 7.关闭SqlSession对象，释放资源
        session.close();
    }
}
```

注意：

原本sql 语句为：`select id,name,email,age from student where id= 1001` 比较僵硬

修改为可以参数的类型

- `#{studentId}`：占位符， 表示从java程序中传入过来的数据

```xml
<select id="selectStudentById" resultType="com.xxxx.domain.Student">
    <!--要执行的 sql 语句-->
    select id,name,email,age from student where id= #{studentId}
</select>
```

测试方法中也需要修改

- `<T> T selectOne(String var1, Object var2);`

```java
// 传入参数
Student student2 = session.selectOne(sqlId,1002);
// {id=1002, name='张三',email='zhangsan@qq.com', age=24}
System.out.println("使用mybatis查询一个学生：" + student2);
```



####  配置日志功能

Mybatis 通过使用内置的日志工厂提供日志功能。内置日志工厂将会把日志工作委托给下面的实现之一：

- SLF4J
- Apache Commons Logging
- Log4j 2
- Log4j
- JDK logging

mybatis.xml 文件加入日志配置，可以在控制台输出执行的 sql 语句和参数

![image-20210317154929784](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210317154929.png)

```xml
<!--设置日志-->
<settings>
    <setting name="logImpl" value="STDOUT_LOGGING" />
</settings>
```

##### 概念

- 自动提交：当你的 sql语句执行完毕后， 提交事务。 数据库更新操作之间保存到数据
- 手动（手工）提交事务：在你需要提交事务的位置， 执行方法，提交事务或者回顾事务。

设置日志后再运行：

![image-20210317155336124](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210317155336.png)

### 2.2 基本的 CURD

查询一个 selectOn、insert、update、delete

#### 插入操作 insert

1、StudentDao 接口中增加方法

```java
// 添加学生
// 返回值int：表示本次操作影响的数据库的行数
int insertStudent(Student student);
```

2、StudentDao.xml 加入 sql 语句

- 如果传入给mybatis是一个java对象，使用 `#{属性名}` 获取此属性的值。
- 属性值放到 `#{}` 占位符的位置，mybatis执行此属性对应的 get 方法
  - 例如 `#{id}` ，会执行Student 类中的 getId() 方法，将返回的 id 值传递出来。

```xml
<!--添加insert
	insert into student values(1003,"李峰","lifeng@qq.com",26)
-->
<insert id="insertStudent">
	insert into student(id, name, email, age)
	values (#{id}, #{name}, #{email}, #{age})
</insert>
```

3、增加测试方法

- mybatis默认执行sql语句是 **手工提交事务** 模式
- 在做insert、update、delete 后需要提交事务。

```java
// 测试插入方法
@Test
public void testInsertStudent() throws IOException {
    // 1.mybatis 主配置文件
    String config = "mybatis.xml";
    // 2.读取配置文件
    InputStream in = Resources.getResourceAsStream(config);
    // 3.创建 SqlSessionFactory 对象
    SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(in);
    // 4.获取 SqlSession
    SqlSession session = factory.openSession();

    // 5.创建保存数据的对象
    Student student = new Student();
    student.setId(1003);
    student.setName("李峰");
    student.setEmail("lifeng@qq.com");
    student.setAge(26);

    // 6.执行插入 insert
    // sql的id = namespace+"."+ select|update|insert|delete标签的id属性值
    int rows = session.insert(
        "com.xxxx.dao.StudentDao.insertStudent", student);
    
    // 7.提交事务
    session.commit();
    System.out.println("增加记录的行数:" + rows);

    // 8.关闭 SqlSession 对象
    session.close();
}
```

![image-20210317162135776](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210317162135.png)

执行结果：

![image-20210317161815302](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210317161815.png)

#### 更新操作 update

1、StudentDao 接口中增加方法

```java
int updateStudent(Student student);
```

2、StudentDao.xml 增加 sql 语句

```xml
<update id="updateStudent">
    update student set age = #{age} where id=#{id}
</update>
```

3、增加测试方法

```java
@Test
public void testUpdate() throws IOException {
    // 1.mybatis 主配置文件
    String config = "mybatis-config.xml";
    // 2.读取配置文件
    InputStream in = Resources.getResourceAsStream(config);
    // 3.创建 SqlSessionFactory 对象
    SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(in);
    // 4.获取 SqlSession
    SqlSession session = factory.openSession();
    // 5.创建保存数据的对象
    Student student = new Student();
    student.setId(1005);//要修改的 id
    student.setAge(30); //要修改的年龄值
    // 6.执行更新 update
    int rows = session.update(
        "com.bjpowernode.dao.StudentDao.updateStudent",student);
    // 7.提交事务
    session.commit();
    System.out.println("修改记录的行数:"+rows);
    // 8.关闭 SqlSession
    session.close();
}
```



#### 更新操作 delete

1、StudentDao 接口中增加方法

```java
int deleteStudent(int id);
```

2、StudentDao.xml 增加 sql 语句

```xml
<delete id="deleteStudent">
    delete from student where id=#{studentId}
</delete>
```

3、增加测试方法

```java
@Test
public void testDelete() throws IOException {
    // 1.mybatis 主配置文件
    String config = "mybatis-config.xml";
    // 2.读取配置文件
    InputStream in = Resources.getResourceAsStream(config);
    // 3.创建 SqlSessionFactory 对象
    SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(in);
    // 4.获取 SqlSession
    SqlSession session = factory.openSession();
    // 5.删除的 id
    int id = 1001;
    // 6.执行删除 delete
    int rows = session.delete(
        "com.bjpowernode.dao.StudentDao.deleteStudent",id);
    // 7.提交事务
    session.commit();
    System.out.println("修改记录的行数:"+rows);
    // 8.关闭 SqlSession
    session.close();
}
```

### 2.3 MyBatis 对象分析

**对象使用**：SqlSession、SqlSessionFactory 等

#### Resources 类

Resources ，顾名思义就是资源，用于读取资源文件。

- mybatis框架中的对象， 一个作用读取主配置信息。
- 其有很多方法通过加载并解析资源文件，返回不同类型的 IO 流对象。

```java
// 2.读取配置文件
InputStream inputStream = Resources.getResourceAsStream("mybatis.xml");
```

#### SqlSessionFactoryBuilder 类

`SqlSessionFactoryBuilder`：负责创建SqlSessionFactory对象，

- SqlSessionFactoryBuilder 对象的 `build()` 方法创建SqlSessionFactory

```java
// 3.创建 SqlSessionFactory 对象
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(inputStream);
```

由于 SqlSessionFactoryBuilder 对象在创建完工厂对象后，就完成了其历史使命，即可被销毁。所以，一般会将该 SqlSessionFactoryBuilder 对象创建为一个方法内的**局部对象**，方法结束，对象销毁。

#### SqlSessionFactory 接口

SqlSessionFactory 接口对象是一个**重量级对象**（系统开销大的对象），是线程安全的；

- 作用是SqlSession的工厂， 就是创建SqlSession对象。
- 创建此对象需要使用更多的资源和时间，在项目中有一个就可以了。

IDEA 中 Ctrl + H 查看实现类

![image-20210317164611661](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210317164611.png)

DefaultSqlSessionFactory实现类

```java
public class DefaultSqlSessionFactory implements SqlSessionFactory { } 
```

SqlSessionFactory接口中的方法：

- `openSession()`：获取一个默认的SqlSession对象， 默认是需要**手工提交事务**的。
- openSession(boolean autoCommit)：autoCommit 参数表示是否自动提交事务
  - openSession(true)：创建一个自动提交事务的SqlSession
  - openSession(false)：等同于没有参数的openSession。

#### SqlSession 接口

SqlSession对象是通过 SqlSessionFactory 获取的，SqlSession 本身是接口

- SqlSession 接口对象用于执行持久化操作。
- 一个 SqlSession 对应着一次数据库会话；一次会话以 SqlSession 对象的创建开始，以 SqlSession 对象的关闭结束。 

DefaultSqlSession:：实现类

```java
public class DefaultSqlSession implements SqlSession { }
```

SqlSession 作用：提供了大量的执行sql语句的方法：

- `T selectOne(String var1)`：执行sql语句，最多得到**一行记录**，多余1行是错误。
- `List<E> selectList(String var1,...)`：执行sql语句，返回多行数据
- `Map<K, V> selectMap(...)`：执行sql语句的，得到一个Map结果
- int insert(...)：执行insert语句
- `int update(...)`：执行update语句
- `int delete(...)`：执行delete语句
- `void commit()`：提交事务
- `void rollback()`：回滚事务

注意SqlSession对象不是线程安全的， 使用的步骤：

1. 在方法的内部，执行sql语句之前，先获取 SqlSession 对象
2. 调用 SqlSession 的方法，执行sql语句
3. 关闭 SqlSession 对象，执行 `SqlSession.close()`

```java
// 4.获取 SqlSession
SqlSession session = factory.openSession();

//...

// 7.提交事务
session.commit();

// 8.关闭 SqlSession 对象
session.close();
```

### 2.4 * MyBatisUtil 工具类

#### *创建 MyBatis 模版

1、创建==mapper文件模版==：

![image-20210317173418853](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210317173419.png)

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--命名空间 namespace 建议使用对应 dao 接口的全限定名-->
<mapper namespace="com.xxxx.dao.StudentDao">
    <!-- 使用 insert, update, delete, select 标签写 sql -->
    <!-- id: 要执行的sql语句的唯一标识, 推荐使用 dao 接口中的方法名称-->
    <!--  resultType: 告诉mybatis 执行sql语句要将数据赋值给的java对象的类型, 使用 java 对象的全限定名称 -->
    <!-- #{?}: 占位符, 表示从java程序中传入过来的数据  -->
    <select id="selectStudentById" resultType="com.xxxx.domain.Student">
         select id,name,email,age from student where id = #{studentId}
    </select>
</mapper>
```

使用模板，可以使用快捷键 ALT+ Insert，输入首字母查找

![image-20210317173907262](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210317173907.png)



2、创建 ==mybatis主配置文件模版==

![image-20210317174711501](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210317174711.png)

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--设置启用日志功能-->
    <settings>
        <setting name="logImpl" value="STDOUT_LOGGING"/>
    </settings>
    <!--配置 mybatis 环境-->
    <environments default="development">
        <!--id:数据源的名称-->
        <environment id="development">
            <!--配置事务类型：使用 JDBC 事务（使用 Connection 的提交和回滚）-->
            <transactionManager type="JDBC"/>
            <!--配置数据源：创建数据库 Connection 对象
                type: POOLED 使用数据库的连接池-->
            <dataSource type="POOLED">
                <!--driver:驱动的内容-->
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <!--连接数据库的url
                    mybatis 是数据库名, 可以自定义修改
                    ? 之后的内容指定编码格式和字符集 utf-8 -->
                <property name="url"
                          value="jdbc:mysql://localhost:3306/mybatis?useUnicode=true&amp;characterEncoding=utf-8"/>
                <!--用户名-->
                <property name="username" value="root"/>
                <!--密码-->
                <property name="password" value="root123"/>
            </dataSource>
        </environment>
    </environments>

    <!--指定其他mapper文件的位置：其他mapper文件目的是找到其他文件的sql语句-->
    <mappers>
        <!--
           使用mapper的resource属性指定mapper文件的路径,这个路径是从target/classes路径开启的
           使用注意：
              1. resource="mapper文件的路径，使用 / 分割路径"
              2. 一个mapper resource 指定一个mapper文件
        -->
        <!--告诉 mybatis 要执行的 sql 语句的位置-->
        <mapper resource="com/xxxx/dao/StudentDao.xml"/>
    </mappers>
</configuration>
```

使用主配置模板

- 全限定名称

![image-20210317175518800](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210317175519.png)

#### 创建工具包

mybatis 的全局配置文件

![image-20210317181000692](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210317181000.png)

```java
package com.xxxx.utils;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import java.io.IOException;
import java.io.InputStream;
public class MyBatisUtil {
    // 1、定义 SqlSessionFactory
    private static SqlSessionFactory factory = null;
    // 2、使用 静态块 创建一次 SqlSessionFactory
    static {
        try {
            // 传入主配置文件名
            String config = "mybatis.xml";
            // 读取配置文件
            InputStream inputStream = Resources.getResourceAsStream(config);
            // 创建 SqlSessionFactory 对象
            factory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    /* 获取 SqlSession 对象 */
    public static SqlSession getSqlSession() {
        SqlSession session = null;
        if (factory != null) {
            // 默认为 openSession(false),手动提交事务
            session = factory.openSession();
        }
        return session;
    }
}
```

#### 使用 MyBatisUtil 

一定要十分注意 sqlId 的值是 xxxDao.xml 中 `namespace+"."+ select|update|insert|delete 标签的id属性值`

- namespace 一般是 Dao 接口文件名
- 标签 id 一般是 Dao 接口的方法名

![](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210317183948.png)

```java
package com.xxxx;

import com.xxxx.domain.Student;
import com.xxxx.utils.MyBatisUtil;
import org.apache.ibatis.session.SqlSession;
import org.junit.Test;

public class MyBatisTest {
    @Test
    public void testSelectById() {
        // 1.获取SqlSession
        SqlSession sqlSession = MyBatisUtil.getSqlSession();


        // 2.指定要执行的 sql语句的 id
        // sql的 id = namespace+"."+ select|update|insert|delete 标签的id属性值
        String sqlId = "com.xxxx.dao.StudentDao" + "." + "selectStudentById";

        // 3.执行SqlSession的方法，表示执行sql语句
        Student student = sqlSession.selectOne(sqlId, 1001);
        System.out.println("查询结果:" + student);

        // 4.关闭SqlSession对象
        sqlSession.close();
    }
}
```

##### 查询多个数据

1、在 StudentDao 接口中新建方法

- 查询多个学生

```java
public interface StudentDao {
    // 查询一个学生的方法
    Student selectStudentById(Integer id);

    // 查询多个学生
    List<Student> selectStudents();
}
```

2、在 StudentDao.xml 中指定sql 语句

```xml
 <!--返回值还是 Student 类-->
 <!--id 是抽象方法的名称，查询多个学生 -->
<select id="selectStudents" resultType="com.xxxx.domain.Student">
    select id,name,email, age from student
</select>
```

3、新建测试方法

```java
@Test
public void testSelectStudents() {
    // 1.获取SqlSession
    SqlSession sqlSession = MyBatisUtil.getSqlSession();

    // 2.指定要执行的 sql语句的 id
    // sql的 id = namespace+"."+ select|update|insert|delete 标签的id属性值
    String sqlId = "com.xxxx.dao.StudentDao" + "." + "selectStudents";

    // 3.执行SqlSession的selectList方法
    List<Student> students = sqlSession.selectList(sqlId);

    for (Student student : students) {
        System.out.println(student);
    }
    // 4.关闭SqlSession对象
    sqlSession.close();
}
```

4、执行结果

![image-20210318141133033](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210318141140.png)

##### 插入数据

1、在 StudentDao 接口中新建方法

```java
public interface StudentDao {
    // ...
    // 插入数据
    int insertStudent(Student student);
}
```

2、在 StudentDao.xml 中指定sql 语句

```xml
 <!--insert 没有返回值-->
<insert id="insertStudent">
    insert into student values (#{id}, #{name}, #{email}, #{age})
</insert>
```

3、新建测试方法

- `int insert(String var1, Object var2);`

```java
@Test
public void testInsertStudents() {
    // 1.获取SqlSession
    SqlSession sqlSession = MyBatisUtil.getSqlSession();

    // 2.指定要执行的 sql语句的 id
    // sql的 id = namespace+"."+ select|update|insert|delete 标签的id属性值
    String sqlId = "com.xxxx.dao.StudentDao" + "." + "insertStudent";

    Student student = new Student();
    student.setId(1008);
    student.setName("东皇");
    student.setEmail("donghuang@qq.com");
    student.setAge(28);
    // 3.执行SqlSession的方法, 需要传入对象
    int rows = sqlSession.insert(sqlId,student);
    System.out.println("insert 的行数" + rows);

    // 4.提交事务
    sqlSession.commit();
    // 5.关闭SqlSession对象
    sqlSession.close();
}
```

4、执行结果

![image-20210318142009778](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210318142009.png)

插入成功

![image-20210318142208683](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210318142208.png)

### 2.5 MyBatis 传统 Dao 开发

使用 Dao 的实现类，操作数据库

![image-20210318144542727](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210318144542.png)

Dao 开发

1、在dao.impl 包下创建 Dao 接口实现类

```java
public class StudentDaoImpl implements StudentDao
```

2、实现接口中 selectStudentById 方法

```java
@Override
    public Student selectStudentById(Integer id) {
        // 1.获取SqlSession
        SqlSession sqlSession = MyBatisUtil.getSqlSession();
        // 2.指定要执行的 sql语句的 id
        // sql的 id = namespace+"."+ select|update|insert|delete 标签的id属性值
        String sqlId = "com.xxxx.dao.StudentDao" + "." + "selectStudentById";
        // 3.执行SqlSession的方法,传入查询id
        Student student = sqlSession.selectOne(sqlId, id);
        // 4.关闭SqlSession对象
        sqlSession.close();
        // 返回查询结果
        return student;
    }
```

测试查询操作：MyBatisTest 类中创建 StudentDaoImpl 对象

```java
public class MyBatisTest2 {
    @Test
    public void testSelectOne() {
        // 接口类型 变量 =  new 接口的实现类();
        StudentDao dao = new StudentDaoImpl();
        Student student = dao.selectStudentById(1001);
        System.out.println(student);
    }
}
```

![image-20210318142950576](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210318143011.png)

3、实现接口中 selectStudents 方法

```java
@Override
public List<Student> selectStudents() {
    SqlSession sqlSession = MyBatisUtil.getSqlSession();
    String sqlId = "com.xxxx.dao.StudentDao" + "." + "selectStudents";
    List<Student> students = sqlSession.selectList(sqlId);
    sqlSession.close();
    return students;
}
```

测试 sertStudents

```java
@Test
public void testSelectStudents() {
    StudentDao dao = new StudentDaoImpl();
    List<Student> students = dao.selectStudents();
    // lambda 表达式
    students.forEach(student -> System.out.println("stu" + student));
}
```

![image-20210318144001672](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210318144001.png)

4、实现接口中 insert 方法

```java
@Override
public int insertStudent(Student student) {
    SqlSession sqlSession = MyBatisUtil.getSqlSession();
    String sqlId = "com.xxxx.dao.StudentDao.insertStudent";
    int rows = sqlSession.insert(sqlId, student);
    sqlSession.commit();
    sqlSession.close();
    return rows;
}
```

2、测试 insert 

```java
@Test
public void testInsertStudent() {
    StudentDao dao = new StudentDaoImpl();
    Student student = new Student();
    student.setId(1006);
    student.setName("林浩");
    student.setEmail("linhao@163.com");
    student.setAge(26);
    int rows = dao.insertStudent(student);
    System.out.println("使用 Dao 添加数据:" + rows);
}
```

![](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210318144707.png)

#### 实现类

```java
package com.xxxx.dao.impl;
import com.xxxx.dao.StudentDao;
import com.xxxx.domain.Student;
import com.xxxx.utils.MyBatisUtil;
import org.apache.ibatis.session.SqlSession;
import java.util.List;
public class StudentDaoImpl implements StudentDao {
    @Override
    public Student selectStudentById(Integer id) {
        // 1.获取SqlSession
        SqlSession sqlSession = MyBatisUtil.getSqlSession();
        // 2.指定要执行的 sql语句的 id
        // sql的 id = namespace+"."+ select|update|insert|delete 标签的id属性值
        String sqlId = "com.xxxx.dao.StudentDao" + "." + "selectStudentById";
        // 3.执行SqlSession的方法,传入查询id
        Student student = sqlSession.selectOne(sqlId, id);
        // 4.关闭SqlSession对象
        sqlSession.close();
        // 返回查询结果
        return student;
    }
    @Override
    public List<Student> selectStudents() {
        SqlSession sqlSession = MyBatisUtil.getSqlSession();
        String sqlId = "com.xxxx.dao.StudentDao" + "." + "selectStudents";
        List<Student> students = sqlSession.selectList(sqlId);
        sqlSession.close();
        return students;
    }
    @Override
    public int insertStudent(Student student) {
        SqlSession sqlSession = MyBatisUtil.getSqlSession();
        String sqlId = "com.xxxx.dao.StudentDao.insertStudent";
        int rows = sqlSession.insert(sqlId, student);
        sqlSession.commit();
        sqlSession.close();
        return rows;
    }
}
```

测试类

```java
package com.xxxx;

import com.xxxx.dao.StudentDao;
import com.xxxx.dao.impl.StudentDaoImpl;
import com.xxxx.domain.Student;
import org.junit.Test;

import java.util.List;

public class MyBatisTest2 {
    @Test
    public void testInsertStudent() {
        StudentDao dao = new StudentDaoImpl();
        Student student = new Student();
        student.setId(1004);
        student.setName("林浩");
        student.setEmail("linhao@163.com");
        student.setAge(26);
        int rows = dao.insertStudent(student);
        System.out.println("使用 Dao 添加数据:" + rows);
    }
    @Test
    public void testSelectStudents() {
        StudentDao dao = new StudentDaoImpl();
        List<Student> students = dao.selectStudents();
        students.forEach(student -> System.out.println("stu" + student));
    }

    @Test
    public void testSelectOne() {
        // 接口类型 变量 =  new 接口的实现类();
        StudentDao dao = new StudentDaoImpl();
        Student student = dao.selectStudentById(1001);
        System.out.println(student);
    }
}
```



#### Dao 开发方式分析

在前面例子中自定义Dao 接口实现类时发现一个问题： Dao 的实现类其实并没有干什么实质性的工作

- 它仅仅就是通过 SqlSession 的相关 API 定位到映射文件 mapper 中相应 id 的 SQL 语句
- 真正对 DB 进行操作的工作其实是由框架通过 mapper 中的 SQL 完成的。

在实现类中

```java
String sqlId="com.xxxx.dao.StudentDao.selectById";
Student student  = sqlSession.selectOne(sqlId,id);
```

在测试方法中：调用dao的方法

1. dao：通过反射能够得到全限定类型名称
   - dao是 StudentDao 类型的， 全限定名称 `com.xxxx.dao.StudentDao`
2. selectById：dao中的**方法名称**，方法名称就是 ==mapper文件== 中标签的id
   - 通过 dao.selectById() 能得到  `sqlId="com.xxxx.dao.StudentDao.selectById"`;
3. 确定调用SqlSession的那个方法
   - 如果dao接口中的方法返回 List，  调用SqlSession的 selectList();
   - 根据mapper文件中的标签，如果标签是`<insert>`，调用SqlSession.insert()方法

```java
// 接口类型    变量 =  new 接口的实现类();
StudentDao  dao  = new StudentDaoImpl();
Student student  = dao.selectById(1002);
```

### 2.6 Mybatis框架

mybatis框架发现使用dao的方法调用能确定执行sql语句的必要信息， mybatis简化dao对象的实现。

1. 由 mybatis 框架在程序执行期间， 根据你的Dao接口创建一个**内存中的接口的实现类对象**。
2. mybatis把这个技术叫做 **dao技术**（动态代理，dao的动态代理）。

#### Dao 动态代理技术

由 mybatis 创建 StudentDao 接口的实现类 Proxy(StudentDaoImpl)

- 使用框架创建的 StudentDaoImpl 代替 "自己手工实现的StudentDaoImpl类的功能"，不用开发人员写 dao接口的实现类。


使用dao的代理要求：
 1. mapper 文件中的 namespace 必须是 dao 接口的全限定名称
 2. mapper 文件中标签的 id 是 dao 接口中的方法名称（一模一样）

所以，MyBatis 框架就抛开了 Dao 的实现类，直接定位到映射文件 mapper 中的相应 SQL 语句，对 DB 进行操作。这种对 Dao 的实现方式称为 Mapper 的==动态代理方式==。

Mapper 动态代理方式无需程序员实现 Dao 接口。接口是由 MyBatis 结合映射文件自动生成的动态代理实现的。

----

本文参考：

> B 站动力节点SSM框架全套教程：https://www.bilibili.com/video/BV1Tv411b7Dp
>