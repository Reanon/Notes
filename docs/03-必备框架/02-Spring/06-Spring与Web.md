# 六、Spring 与 Web

在 Web 项目中使用 Spring 框架，首先要解决在 web 层（这里指 Servlet）中获取到 Spring 容器的问题。只要在 web 层获取到了 Spring 容器，便可从容器中获取到 Service 对象。

## 6.1 Spring 创建 Web 项目

项目需求：完成学生注册功能

具体步骤

1. 新建 maven
2. 修改pom.xml
   - spring、mybatis以外的依赖，servlet、jsp依赖
3. 创建实体类 Student， 对应Student2表
4. 创建dao接口和mapper文件
5. 创建mybatis主配置文件
6. 创建service和实现类
7. 创建servlet，接收请求的参数，调用service对象
8. 创建jsp，提交请求参数
9. 创建jsp，作为视图，显示请求的处理结果
10. 创建spring配置文件

### 1、新建 Maven 项目

新建一个 Maven Project，类型为 maven-archetype-webapp

![image-20210401100835739](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210401100835.png)

在 main 包下创建 java 和 resources 目录

![image-20210401101228522](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210401101228.png)

### 2、配置 pom.xml 文件

#### (1) Spring、mybatis 依赖

Spring 依赖

```xml
<!--spring依赖-->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.2.5.RELEASE</version>
</dependency>

<!--spring事务依赖-->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-tx</artifactId>
    <version>5.2.5.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.2.5.RELEASE</version>
</dependency>

```

MyBatis 依赖

```xml
<!--mybatis依赖-->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.1</version>
</dependency>

<!--mybatis和spring集成-->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis-spring</artifactId>
    <version>1.3.1</version>
</dependency>
```

#### (2) 数据库依赖

```xml
<!--mysql驱动-->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.9</version>
</dependency>

<!--阿里的连接池-->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.12</version>
</dependency>
```

#### (3) 加入 servlet、jsp 依赖

```xml
<!-- servlet依赖 -->
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>3.1.0</version>
    <scope>provided</scope>
</dependency>

<!-- jsp依赖 -->
<dependency>
    <groupId>javax.servlet.jsp</groupId>
    <artifactId>jsp-api</artifactId>
    <version>2.2.1-b03</version>
    <scope>provided</scope>
</dependency>
```

#### (4) 单元测试依赖

```xml
<!-- 单元测试 -->
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.11</version>
    <scope>test</scope>
</dependency>
```

#### (5)  插件配置

```xml
<build>
    <!--使得目录下的类型可以被扫描到-->
    <resources>
        <resource>
            <directory>src/main/java</directory>
            <!--包括目录下的.properties,.xml 文件都会扫描到-->
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>false</filtering>
        </resource>
    </resources>
    <!--配置编译环境为 JDK 1.8-->
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
```



#### 完整版本 pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.xxxx</groupId>
    <artifactId>ch18-spring-web</artifactId>
    <version>1.0.0</version>
    <packaging>war</packaging>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>

    <dependencies>
        <!--单元测试-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.11</version>
            <scope>test</scope>
        </dependency>

        <!--spring依赖-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.2.5.RELEASE</version>
        </dependency>

        <!--spring事务依赖-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-tx</artifactId>
            <version>5.2.5.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.2.5.RELEASE</version>
        </dependency>

        <!--mybatis依赖-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.1</version>
        </dependency>

        <!--mybatis和spring集成-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>1.3.1</version>
        </dependency>

        <!--mysql驱动-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.9</version>
        </dependency>

        <!--阿里的连接池-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.1.12</version>
        </dependency>

        <!-- servlet依赖 -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>
            <scope>provided</scope>
        </dependency>

        <!-- jsp依赖 -->
        <dependency>
            <groupId>javax.servlet.jsp</groupId>
            <artifactId>jsp-api</artifactId>
            <version>2.2.1-b03</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>

    <build>
        <!--使得目录下的类型可以被扫描到-->
        <resources>
            <resource>
                <directory>src/main/java</directory>
                <!--包括目录下的.properties,.xml 文件都会扫描到-->
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>false</filtering>
            </resource>
        </resources>
        <!--配置编译环境为 JDK 1.8-->
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



### 3、实体类和表

创建实体类 Student 和 对应 Student 表

（1）创建表

```mysql
CREATE TABLE `student` (
  `id` int(20) NOT NULL AUTO_INCREMENT,
  `name` varchar(80) DEFAULT NULL,
  `age` int(20) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

- 插入数据

![image-20210401102318174](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210401102318.png)

（2）定义实体类

- 注意：实体类的属性与表的列名不相同

```java
package com.xxxx.domian;

public class Student {
    private Integer stuId;
    private String stuName;
    private Integer stuAge;

    // get、set、toString...
}

```



### 4、Dao接口和mapper文件

#### Dao 接口

```java
package com.xxxx.dao;

import com.xxxx.domian.Student;

public interface StudentDao {
    int insertStudent(Student student);

    Student selectById(@Param("studentId") Integer id);
}
```



#### Mapper文件

##### resultType

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.xxxx.dao.StudentDao">
    <!-- 使用 insert, update, delete, select 标签写 sql -->
    <insert id="insertStudent">
        insert into student(name, age)
        values (#{stuName}, #{stuAge})
    </insert>

    <select id="selectById" resultType="com.xxxx.domian.Student">
        select id as stuID, name as stuName, age as stuAge
        from student
        where id = #{studentId}
    </select>
</mapper>
```

##### resultMap

对于列名与属性名不同，还可以采用 resultMap 方式来定义

```xml
<!--定义列和属性对应关系-->
<resultMap id="studentMap" type="com.xxxx.domian.Student">
    <id column="id" property="stuId"/>
    <result column="name" property="stuName"/>
    <result column="age" property="stuAge"/>
</resultMap>

<select id="selectById" resultMap="studentMap">
    select id, name, age
    from student
    where id = #{studentId}
</select>
```



### 5、MyBatis 主配置文件

- 如果拷贝其他的 mybatis.xml 配置文件，需要重新拷贝一下包，最好不要直接输入。

![image-20210401104514001](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210401104521.png)

 ```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--设置日志-->
    <settings>
        <setting name="logImpl" value="STDOUT_LOGGING"/>
    </settings>

    <!-- 为实体类设置别名（类名首字母小写） -->
    <typeAliases>
        <package name="com.xxxx.domian"/>
    </typeAliases>

    <!--指定其他mapper文件的位置：其他mapper文件目的是找到其他文件的sql语句-->
    <mappers>
        <package name="com.xxxx.dao" />
    </mappers>
</configuration>
 ```



### 6、service 和实现类

#### service 接口

```java
package com.xxxx.service;

import com.xxxx.domian.Student;

public interface StudentService {
    int addStudent(Student student);

    // query, search, get...
    Student findStudentById(Integer id);
}
```

#### service 实现类

```java
package com.xxxx.service.impl;

import com.xxxx.dao.StudentDao;
import com.xxxx.domian.Student;
import com.xxxx.service.StudentService;

public class StudentServiceImpl implements StudentService {

    private StudentDao dao;

    public void setDao(StudentDao dao) {
        this.dao = dao;
    }

    @Override
    public int addStudent(Student student) {
        // 具体的业务逻辑，一般都会限制输入
        int rows = dao.insertStudent(student);
        return rows;
    }

    @Override
    public Student findStudentById(Integer id) {
        Student student = dao.selectById(id);
        return student;
    }
}
```



### 7、Spring 配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

    <!--加载外部的属性配置文件-->
    <context:property-placeholder location="classpath:jdbc.properties"/>

    <!--声明数据源DataSource-->
    <bean id="myDataSource" class="com.alibaba.druid.pool.DruidDataSource" init-method="init" destroy-method="close">
        <property name="url" value="${jdbc.url}"/>
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>

    <!--声明SqlSessionFactoryBean, 在这个类的内部创建SqlSessionFactory-->
    <bean id="factory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <!--指定数据源-->
        <property name="dataSource" ref="myDataSource"/>

        <!-- 指定mybatis主配置文件-->
        <property name="configLocation" value="classpath:mybatis.xml"/>
    </bean>

    <!-- 声明 MapperScannerConfigurer -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <!--指定SqlSessionFactory对象的名称-->
        <property name="sqlSessionFactoryBeanName" value="factory"/>
        <!--指定基本包，dao接口所在的包名-->
        <property name="basePackage" value="com.xxxx.dao"/>
    </bean>

    <!--声明service-->
    <bean id="studentService" class="com.xxxx.service.impl.StudentServiceImpl">
        <property name="dao" ref="studentDao"/>
    </bean>
</beans>
```

### 8、测试

- 注意，这里需要配置Spring，在步骤九中配置

```java
public class MyTest {
    @Test
    public void test01() {
        String config = "applicationContext.xml";
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext(config);

        StudentService service = (StudentService) context.getBean("studentService");
        Student student = service.findStudentById(2);

        System.out.println(student);
    }
}
```

![image-20210401110415638](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210401110415.png)





### 9、创建 servlet 

创建servlet接收请求的参数，来调用service对象

#### Servlet 实现类

- 在 controller 包下创建 Servlet，选择不要注解的模式

![image-20210401110752510](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210401110752.png)

```java
package com.xxxx.controller;

import com.xxxx.domian.Student;
import com.xxxx.service.StudentService;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.IOException;

public class AddStudentServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException,
            IOException {
        String strName = request.getParameter("name");
        String strAge = request.getParameter("age");

        // 调用service
        // 创建容器获取service
        String config ="applicationContext.xml";
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext(config);

        StudentService service = (StudentService) context.getBean("studentService");
        Student student = new Student();
        student.setStuName(strName);
        student.setStuAge(Integer.valueOf(strAge));
        service.addStudent(student);

        // 给用户显示请求的处理结果
        request.getRequestDispatcher("/show.jsp")
                .forward(request, response);
    }
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException,
            IOException {
    }
}
```



#### web.xml

生成 servlet 的瞬间就会需要配置一下 web.xml

![image-20210401112503972](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210401112504.png)

```xml
<!DOCTYPE web-app PUBLIC
        "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
        "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
    <display-name>Archetype Created Web Application</display-name>
    <servlet>
        <servlet-name>AddStudentServlet</servlet-name>
        <servlet-class>com.xxxx.controller.AddStudentServlet</servlet-class>
    </servlet>

    <servlet-mapping>
        <servlet-name>AddStudentServlet</servlet-name>
        <url-pattern>/add</url-pattern>
    </servlet-mapping>
</web-app>

```





### 10、创建jsp

#### index.jsp

创建 index.jsp 提交请求参数

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>添加学生</title>
</head>
<body>
<div align="center">
    <p>添加学生</p>
    <form action="add" method="post">
        姓名：<input type="text" name="name"> <br/>
        年龄：<input type="text" name="age"> <br/>
        <input type="submit" value="注册学生">
    </form>

    <br/>
    <br/>
    <p>查询学生</p>
    <form action="queryStudent" method="get">
        学生id：<input type="text" name="stuid"> <br/>
        <input type="submit" value="查询学生">
    </form>
</div>
</body>
</html>
```

#### show.jsp

创建 show.jsp 作为视图，显示请求的处理结果

![image-20210401111738154](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210401111738.png)

```jsp	
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
    <head>
        <title>Title</title>
    </head>
    <body>
        /show.jsp 注册成功
    </body>
</html
```

### 11、Tomcat 配置

#### 配置 Tomcat

想要运行 sevlet 需要配置一个 tomcat 服务器

![image-20210401112659563](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210401112659.png)

配置 Tomcat 服务器

![image-20210401112912000](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210401112912.png)

配置部署

![image-20210401113039678](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210401113039.png)

#### 启动tomcat

![image-20210401113212782](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210401113212.png)

输入数据，点击 「注册学生」

![image-20210401114125066](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210401114125.png)

查看数据库

- 可能是页面编码的问题。

![image-20210401114144220](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210401114144.png)

### Web 项目使用 Spring 的问题

- 创建容器对象次数多
- 在多个servlet中，分别创建容器对象

需要一个什么样的容器对象

- 容器对象只有一个， 创建一次就可以了
- 容器对象应该在整个项目中共享使用。 多个servlet都能使用同一个容器对象

解决办法：

1. 使用监听器 ServletContextListener
2. 在监听器中创建好的容器对象，应该放在 web 应用中的 ServletContext 作用域中。

## 6.2 Spring  监听器 ContextLoaderListener 

ContextLoaderListener 监听器作用：

1. 创建容器对象，执行 ApplicationContext context = new ClassPathXmlApplicationContext(“applicationContext.xml”);
2. 把容器对象放入到 ServletContext，ServletContext.setAttribute(key, context)

ContextLoaderListener 使用步骤：

1. pom.xml 加入依赖Spring-web
2. web.xml 声明监听器

## 6.3 监听器使用步骤

### 1、maven 依赖 pom.xml

```xml
<!--监听器依赖-->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-web</artifactId>
    <version>5.2.5.RELEASE</version>
</dependency>
```

#### 完整 pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.xxxx</groupId>
    <artifactId>ch18-spring-web</artifactId>
    <version>1.0.0</version>
    <packaging>war</packaging>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>

    <dependencies>
        <!--单元测试-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.11</version>
            <scope>test</scope>
        </dependency>

        <!--spring依赖-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.2.5.RELEASE</version>
        </dependency>

        <!--spring事务依赖-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-tx</artifactId>
            <version>5.2.5.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.2.5.RELEASE</version>
        </dependency>

        <!--mybatis依赖-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.1</version>
        </dependency>

        <!--mybatis和spring集成-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>1.3.1</version>
        </dependency>

        <!--mysql驱动-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.9</version>
        </dependency>

        <!--阿里的连接池-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.1.12</version>
        </dependency>

        <!-- servlet依赖 -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>
            <scope>provided</scope>
        </dependency>

        <!-- jsp依赖 -->
        <dependency>
            <groupId>javax.servlet.jsp</groupId>
            <artifactId>jsp-api</artifactId>
            <version>2.2.1-b03</version>
            <scope>provided</scope>
        </dependency>

        <!--监听器依赖-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
            <version>5.2.5.RELEASE</version>
        </dependency>
    </dependencies>

    <build>
        <!--使得目录下的类型可以被扫描到-->
        <resources>
            <resource>
                <directory>src/main/java</directory>
                <!--包括目录下的.properties,.xml 文件都会扫描到-->
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>false</filtering>
            </resource>
        </resources>
        <!--配置编译环境为 JDK 1.8-->
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



### 2、注册监听器 ContextLoaderListener

若要在 ServletContext 初始化时创建 Spring 容器，就需要使用监听器接口ServletContextListener 对 ServletContext 进行监听，即在 **web.xml 中注册该监听器**。

```xml
<!-- 声明监听器
 	默认监听器：创建容器对象时，读取的配置文件： /WEB-INF/applicationContext.xml-->
<listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
```

Spring 为该监听器接口定义了一个实现类 ContextLoaderListener，完成了两个很重要的工作：

1. 创建容器对象
2. 将容器对象放入到了 ServletContext 的空间中。

### 3、指定 Spring 配置文件的位置

ContextLoaderListener 在对 Spring 容器进行创建时，需要加载 Spring 配置文件。

- 其默认的 Spring 配置文件位置与名称为：WEB-INF/applicationContext.xml。
- 但一般会将 Spring 配置文件放置于项目的 classpath 下，即 resources 下。
- 所以需要在 web.xml 中对 Spring 配置文件的位置及名称进行指定。

```xml
<!-- 自定义容器使用的配置文件路径
	context-param:叫做上下文参数, 用于给监听器提供参数
-->
<context-param>
    <!-- contextConfigLocation：名称是固定的, 表示自定义Spring配置文件的路径-->
    <param-name>contextConfigLocation</param-name>
    <!--自定义配置文件的路径, Spring 文件名可以修改的-->
    <param-value>classpath:applicationContext.xml</param-value>
</context-param>
<!-- 声明监听器 -->
<listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
```

#### web.xml

![image-20210401155159271](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210401155159.png)

```xml
<!DOCTYPE web-app PUBLIC
        "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
        "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <display-name>Archetype Created Web Application</display-name>
    <servlet>
        <servlet-name>AddStudentServlet</servlet-name>
        <servlet-class>com.xxxx.controller.AddStudentServlet</servlet-class>
    </servlet>
    <servlet>
        <servlet-name>QueryStudentServlet</servlet-name>
        <servlet-class>com.xxxx.controller.QueryStudentServlet</servlet-class>
    </servlet>

    <!--添加的servlet-->
    <servlet-mapping>
        <servlet-name>AddStudentServlet</servlet-name>
        <url-pattern>/add</url-pattern>
    </servlet-mapping>

    <!--查询的servlet-->
    <servlet-mapping>
        <servlet-name>QueryStudentServlet</servlet-name>
        <url-pattern>/queryStudent</url-pattern>
    </servlet-mapping>

    <!-- 自定义容器使用的配置文件路径 -->
    <context-param>
        <!-- contextConfigLocation名称是固定的, 表示自定义Spring配置文件的路径-->
        <param-name>contextConfigLocation</param-name>
        <!--自定义配置文件的路径, Spring 文件名可以修改的-->
        <param-value>classpath:applicationContext.xml</param-value>
    </context-param>
    <!--声明监听器
       默认监听器：创建容器对象时，读取的配置文件： /WEB-INF/spring-beans.xml
    -->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
</web-app>
```



#### 4、ContextLoaderListener 源代码

ContextLoaderListener 的源码中有四个方法，两个是构造方法，一个初始化方法，一个销毁方法。

```java
package org.springframework.web.context;

import javax.servlet.ServletContextEvent;
import javax.servlet.ServletContextListener;

public class ContextLoaderListener extends ContextLoader implements ServletContextListener {
    // 两个构造方法
    public ContextLoaderListener() {
    }
    public ContextLoaderListener(WebApplicationContext context) {
        super(context);
    }
	// 初始化方法
    public void contextInitialized(ServletContextEvent event) {
        this.initWebApplicationContext(event.getServletContext());
    }
	// 销毁方法
    public void contextDestroyed(ServletContextEvent event) {
        this.closeWebApplicationContext(event.getServletContext());
        ContextCleanupListener.cleanupAttributes(event.getServletContext());
    }
}
```

#### contextInitialized()

在这四个方法中较重要的方法应该就是 contextInitialized()， context 初始化方法。

![image-20210401155440954](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210401155441.png)

跟踪 initWebApplicationContext()方法，可以看到，在其中创建了容器对象

![image-20210401155741890](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210401155742.png)

并且，将创建好的容器对象放入到了 ServletContext 的空间中，key 为一 个常量：WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE。

![image-20210401155836422](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210401155836.png)



```java
public WebApplicationContext initWebApplicationContext(ServletContext servletContext) {
    // ...
    try {
        if (this.context == null) {
            //创建srping的容器对象
            this.context = this.createWebApplicationContext(servletContext);
        }

        // 把容器对象放入的ServletContext作用域
        // key=WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE
        // value=容器对象
        servletContext.setAttribute(
            WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE,                             this.context);
    } catch (Error | RuntimeException var8) {

    }
}
```

#### WebApplicationContext

WebApplicationContext 也是继承于ApplicationContext

```java
// WebApplicationContext是web项目中使用的容器对象
public interface WebApplicationContext extends ApplicationContext 
```



### 5、获取 Spring 容器对象

在 Servlet 中获取容器对象的常用方式有两种：

1. 直接从 ServletContext 中获取
2. 通过 WebApplicationContextUtils 获取

#### 从 ServletContext 中获取

从对监听器 ContextLoaderListener 的源码分析可知，容器对象在 ServletContext 的中存放的 key 为 WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE。

- 所以，可以直接通过 ServletContext 的 `getAttribute()` 方法，按照指定的 key 将容器对象获取到。

```java
// 使用了监听器已经创建好了容器对象， 从ServletContext作用域获取容器
WebApplicationContext context  = null;
String  key  = WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE;
// ServletContext, servlet中的方法
ServletContext servletContext = getServletContext();

Object attribute = servletContext.getAttribute(key);
if (attribute != null) {
    context = (WebApplicationContext) attribute;
}
```

相当于

```java
// 调用service
// 创建容器获取service
String config = "applicationContext.xml";
ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext(config);
```



测试一下：

````java
package com.xxxx.controller;

import com.xxxx.domian.Student;
import com.xxxx.service.StudentService;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.springframework.web.context.WebApplicationContext;

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.IOException;

public class QueryStudentServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException,
    IOException {
        String stuId = request.getParameter("stuId");

        // 调用service
        // 创建容器获取service
        // String config = "applicationContext.xml";
        // ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext(config);

        // 使用了监听器已经创建好了容器对象， 从ServletContext作用域获取容器
        WebApplicationContext context  = null;
        String  key  = WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE;
        // ServletContext, servlet中的方法
        ServletContext servletContext = getServletContext();

        Object attribute = servletContext.getAttribute(key);
        if (attribute != null) {
            context = (WebApplicationContext) attribute;
        }
        
        // Root WebApplicationContext
        System.out.println("在servlet中创建的对象容器==="+context);
        StudentService service = (StudentService) context.getBean("studentService");
        Student student = service.findStudentById(Integer.valueOf(stuId));

        System.out.println(student);

        request.setAttribute("stu", student);
        request.getRequestDispatcher("/show.jsp").forward(request, response);
    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException,
    IOException {

    }
}
````

![image-20210401161604433](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210401161604.png)

执行两次对象都是一样的

![image-20210401161635385](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210401161635.png)



#### 通过 WebApplicationContextUtils 获取

工具类 WebApplicationContextUtils 有一个方法专门用于从 ServletContext 中获取 Spring 容器对象： `getRequiredWebApplicationContext(ServletContext sc)`

调用 Spring 提供的方法获取容器对象：

```java
// 使用spring提供的工具方法，获取容器对象
ServletContext servletContext = getServletContext();
WebApplicationContext context =
    WebApplicationContextUtils.getRequiredWebApplicationContext(servletContext);
```

查其源码，看其调用关系，就可看到其是从 ServletContext 中读取的属性值，即 Spring 容器。

![image-20210401162644660](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210401162644.png)

##### 测试一下

```java
package com.xxxx.controller;

import com.xxxx.domian.Student;
import com.xxxx.service.StudentService;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.springframework.web.context.WebApplicationContext;
import org.springframework.web.context.support.WebApplicationContextUtils;

import javax.servlet.*;
import javax.servlet.http.*;
import java.io.IOException;

public class AddStudentServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException,
    IOException {
        String strName = request.getParameter("name");
        String strAge = request.getParameter("age");

        // 使用spring提供的工具方法，获取容器对象
        ServletContext servletContext = getServletContext();
        WebApplicationContext context =
            WebApplicationContextUtils.getRequiredWebApplicationContext(servletContext);
	    // Root WebApplicationContext
        System.out.println("在servlet中创建的对象容器==="+context);
        // 调用service
        // 创建容器获取service
        StudentService service = (StudentService) context.getBean("studentService");
        Student student = new Student();
        student.setStuName(strName);
        student.setStuAge(Integer.valueOf(strAge));
        service.addStudent(student);

        // 给用户显示请求的处理结果
        request.getRequestDispatcher("/show.jsp")
            .forward(request, response);
    }
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException,
    IOException {
    }
}
```

可以看出对象还是一致的。

![image-20210401162922911](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210401162923.png)