# 四、Spring 集成 MyBatis

Spring 能集成很多的框架，这是spring一个优势功能。 通过集成功能，让开发人员使用其他框架更方便。

## 4.1 集成思路

将 MyBatis 与 Spring 进行整合，主要解决的问题就是将 SqlSessionFactory 对象交由 Spring 来管理。所以该整合只需要将 SqlSessionFactory 的对象生成器 SqlSessionFactoryBean 注册在 Spring 容器中，再将其注入给 Dao 的实现类即可完成整合。

集成使用的是Spring **IoC 核心技术**：

- Ioc（Inversion of Control） 可以把mybatis框架中的对象交给spring统一 **创建**， 开发人员从spring中获取对象，开发人员就不用同时面对两个或多个框架了，就面对一个spring。

实现 Spring 与 MyBatis 的整合常用的方式：扫描的 Mapper 动态代理

### 分析 MyBatis

MyBatis 执行 sql 语句，需要使用那些对象：

1. 需要有Dao接口的代理对象， 例如StudentDao接口，需要一个它的代理对象
   - 使用 `SqlSession.getMapper(StudentDao.class)` 可以得到dao代理对象
2. 需要有 SqlSessionFactory 来 创建 SqlSessionFactory 对象，才能使用 `openSession()` 得到SqlSession对象
3. 数据源 DataSource 对象。使用一个更强大、功能更多的连接池对象代替mybatis自己的PooledDataSource。

![image-20201024145315985](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210330163009.png)

## 4.2 实现步骤

### 1、MySQL 创建数据库



在 MySQL 中 springDatebase 的 创建学生表 student

student2(id int 主键列， 自动增长， name varchar(80),
                                    age int)

```mysql
CREATE DATABASE springDatebase;
USE springDatebase; -- 使用数据库
-- 创建表
CREATE TABLE `student` (
  `id` INT (20)  NOT NULL  AUTO_INCREMENT, 
  `name` VARCHAR (80) DEFAULT NULL,
  `age` INT (20) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE = INNODB DEFAULT CHARSET = utf8 ;
```

![image-20210330171459649](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210330171459.png)



### 2、maven 依赖 pom.xml

```xml
<dependencies>
    <!-- 单元测试 --> 
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
</dependencies>
```

插件：

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



### 3、定义实体类 

```java
package com.xxxx.domain;

public class Student {
    private Integer id;
    private String name;
    private Integer age;
    // 省略 get、set、toString...
}
```

### 4、定义 Dao 接口

```java
package com.xxxx.dao;

import com.xxxx.domain.Student;

import java.util.List;

public interface StudentDao {
    // 插入数据
    int insertStudent(Student student);
    // 查询学生
    List<Student> selectStudents();
}
```

### 5、定义映射文件 mapper

在 Dao 接口的包中创建 MyBatis 的映射文件 mapper，**命名与接口名相同**

- 本例为 StudentDao.xml，mapper 中的 namespace 取值也为 Dao 接口的全限定性名。

![image-20210330173012137](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210330173012.png)

使用之前定义好的 mybatis-mapper 模板

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--命名空间 namespace 建议使用对应 dao 接口的全限定名-->
<mapper namespace="com.xxxx.dao.StudentDao">
    <!-- 使用 insert, update, delete, select 标签写 sql -->

    <insert id="insertStudent">
        insert into student(name, age)
        values (#{name}, #{age})
    </insert>

    <select id="selectStudents" resultType="com.xxxx.domain.Student">
        select id, name, age
        from student
    </select>
</mapper>
```

### 6、 定义 MyBatis 主配置文件

在 resource 下定义 MyBatis 的主配置文件，命名为 mybatis.xml。 这里有两点需要注意：

1. 主配置文件中不再需要数据源的配置了。因为数据源要交给 Spring 容器 来管理了。
2. 这里对 mapper 映射文件的注册使用标签，即只需给出 mapper 映射文件所在的包即可。
   - 因为 mapper 的名称与 Dao 接口名相同， 可以使用这种简单注册方式。
   - 这种方式的好处是，若有多个映射文件，这里的配置也是不用改变的。
   - 当然，也可使用原来的 `<resource/>` 标签方式

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
    <!-- 别名 -->
    <typeAliases>
        <!--
            package：把包下面的所有类名作为别名
            name：实体类的包名
        -->
        <package name="com.xxxx.domain"/>
    </typeAliases>

    <!-- sql 映射文件的位置：mapper文件位置-->
    <mappers>
        <!-- <mapper resource="com/xxxx/dao/StudentDao.xml"/>-->
        <!--
          package:指定Dao接口的包的位置，表示把包下面的sql映射文件找到
          name: Dao接口的包名
          要求：
            1. mapper文件和 dao 接口在同一目录
            2. mapper文件和 dao 接口名称一致
        -->
        <package name="com.xxxx.dao" />
    </mappers>
</configuration>
```

### 7、定义 Service 接口和实现类

接口定义：

```java
package com.xxxx.service;

import com.xxxx.domain.Student;

import java.util.List;

public interface StudentService {
    // 添加学生
    int addStudent(Student student);
	// 查询学生
    List<Student> queryStudents();
}
```

实现类定义：

```java
package com.xxxx.service.impl;

import com.xxxx.dao.StudentDao;
import com.xxxx.domain.Student;
import com.xxxx.service.StudentService;

import java.util.List;

public class StudentServiceImpl implements StudentService {
    // 定义 Dao 的引用类型的属性
    private StudentDao studentDao;
    
    public void setStudentDao(StudentDao studentDao) {
        this.studentDao = studentDao;
    }

    @Override
    public int addStudent(Student student) {
        int rows = studentDao.insertStudent(student);
        return rows;
    }

    @Override
    public List<Student> queryStudents() {
        List<Student> students = studentDao.selectStudents();
        return students;
    }
}
```

### 8、修改 Spring 配置文件

在 resources 下创建Spring 配置文件，名称为applicationContext.xml

![image-20210330175222569](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210330175222.png)

#### (1) 数据源 Druid 配置

使用 JDBC 模板，首先需要配置好数据源，数据源直接以 Bean 的形式配置 在 Spring 配置文件中。根据数据源的不同，其配置方式不同：

1、Druid 数据源 DruidDataSource

> 官网：https://github.com/alibaba/druid 
> 使用地址：[常见问题](https://github.com/alibaba/druid/wiki/常见问题)
> 通用配置：[DruidDataSource配置](https://github.com/alibaba/druid/wiki/DruidDataSource配置)

Druid 是阿里的开源数据库连接池。是 Java 语言中最好的数据库连接池。Druid 能够提供强大的监控和扩展功能。

官方参考配置连接池：

```xml
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource" init-method="init" destroy-method="close"> 
    <property name="url" value="${jdbc_url}" />
    <property name="username" value="${jdbc_user}" />
    <property name="password" value="${jdbc_password}" />

    <property name="filters" value="stat" />

    <property name="maxActive" value="20" />
    <property name="initialSize" value="1" />
    <property name="maxWait" value="6000" />
    <property name="minIdle" value="1" />

    <property name="timeBetweenEvictionRunsMillis" value="60000" />
    <property name="minEvictableIdleTimeMillis" value="300000" />

    <property name="testWhileIdle" value="true" />
    <property name="testOnBorrow" value="false" />
    <property name="testOnReturn" value="false" />

    <property name="poolPreparedStatements" value="true" />
    <property name="maxOpenPreparedStatements" value="20" />

    <property name="asyncInit" value="true" />
</bean>
```

Spring 配置文件：

- 一般只需要配置以下这几项就可以了
- 链接：`jdbc:mysql://localhost:3306/springDatebase?useUnicode=true&amp;characterEncoding=utf-8`

```xml
<!--声明数据源DataSource-->
<bean id="myDataSource" class="com.alibaba.druid.pool.DruidDataSource" init-method="init" destroy-method="close">
    <property name="url" value="jdbc:mysql://localhost:3306/springDatebase"/>
    <property name="username" value="root"/>
    <property name="password" value="root123"/>
</bean>
```

#### (2) 从属性文件读取数据库连接信息

为了便于维护，可以将数据库连接信息写入到属性文件中，使 Spring 配置 文件从中读取数据。 属性文件名称自定义，但一般都是放在 src 下。

jdbc.properties

- jdbc.url精简写法：`jdbc:mysql://localhost:3306/[数据库名]`

```properties
jdbc.url=jdbc:mysql://localhost:3306/springdatabase?useUnicode=true&amp;characterEncoding=utf-8
jdbc.username=root
jdbc.password=root123
```

该属性文件若要被 Spring 配置文件读取，其必须在配置文件中进行注册， 使用`<context:property-placeholder/>` 标签。

- 该方式要求在 Spring 配置文件头部加入 spring-context.xsd 约束文件
- `<context:property-placeholder/>`  标签中有一个属性 location，用于指定属性文件的位置。

```xml
<!--加载外部的属性配置文件-->
<context:property-placeholder location="classpath:jdbc.properties"/>
```

Spring 配置文件从属性文件中读取数据时，需要在的 value 属性中使用`${}`，将在属性文件中定义的 key 括起来，以引用指定属性的值。

```xml
<!--声明数据源DataSource-->
<bean id="myDataSource" class="com.alibaba.druid.pool.DruidDataSource" init-method="init" destroy-method="close">
    <property name="url" value="${jdbc.url}"/>
    <property name="username" value="${jdbc.username}"/>
    <property name="password" value="${jdbc.password}"/>
</bean>
```



#### (3) 注册 SqlSessionFactoryBean

声明SqlSessionFactoryBean类，在这个类内部创建的是 SqlSessionFactory 对象。

```xml
<!--声明SqlSessionFactoryBean, 在这个类的内部创建SqlSessionFactory-->
<bean id="factory" class="org.mybatis.spring.SqlSessionFactoryBean" >
    <!--指定数据源-->
    <property name="dataSource" ref="myDataSource"/>

    <!-- 指定mybatis主配置文件
             Resource可以直接使用 value 属性赋值。
    -->
    <property name="configLocation" value="classpath:mybatis.xml"/>
</bean>
```

相当于 

```java
// 1.定义mybatis主配置文件的位置
String config="mybatis.xml";
// 2.读取主配置文件
InputStream inputStream  =Resources.getResourceAsStream(config);
// 3.创建 SqlSessionFactory 对象
factory  = new SqlSessionFactoryBuilder().build(inputStream);
```

![image-20210330191048377](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210330191048.png)

测试一下生成的对象类型：

- factory 对象默认类型为：`DefaultSqlSessionFactory`

```java
public class MyTest {
    @Test
    public void test01() {
        String config = "applicationContext.xml";
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext(config);

        // 获取Spring 容器中声明的对象名称
        String[] names = context.getBeanDefinitionNames();
        for (String name : names) {
            System.out.print("容器对象的名称：" + name);
            System.out.println(" 对象的类型为：" + context.getBean(name));
        }
    }
}
```

![image-20210330191816712](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210330191816.png)



#### (4) 定义 Mapper 扫描配置器 MapperScannerConfigurer

声明MapperScannerConfiguration类，在内部创建dao代理对象，创建的对象都放在spring容器中。

- Mapper 扫描配置器 MapperScannerConfigurer 会自动生成指定的基本包中 mapper 的代理对象。
- 该 Bean 无需设置 id 属性。basePackage 使用分号或逗号设置多个包。

```xml
<!-- 声明 MapperScannerConfigurer -->
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    <!--指定SqlSessionFactory对象的名称-->
    <property name="sqlSessionFactoryBeanName" value="factory"/>
    <!--指定基本包，dao接口所在的包名-->
    <property name="basePackage" value="com.xxxx.dao"/>
</bean>
```

MapperScannerConfigurer 的作用：

1. 循环basePackage所表示的包，把包中的每个接口都找到，调用SqlSession.getMapper
2. 把每个dao接口都创建出dao对象 ，dao代理对象放在容器中。

相当于：

```java
ApplicationContext context = ....
    SqlSessionFactory sqlSessionFactory  = context.getBean("factory");
SqlSession session = sqlSessionFactory.openSession();
// 在 dao 下循环查找接口
for(接口 : com.xxxx.dao){
    接口 对象 =  session.getMapper(接口);
    // Spring 容器中添加对象
    springMap.put(接口名的首字母小写, 对象);
}
```

-----

测试一下：

```java
public class MyTest {
    @Test
    public void test01() {
        String config = "applicationContext.xml";
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext(config);

        // 获取Spring 容器中声明的对象名称
        String[] names = context.getBeanDefinitionNames();
        for (String name : names) {
            System.out.println("容器对象的名称：" + name);
            System.out.println(" 对象的类型为：" + context.getBean(name));
        }
    }
}
```

![image-20210330193929165](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210330193929.png)

测试插入数据

```java
@Test
public void test02() {
    String config = "applicationContext.xml";
    ApplicationContext context = new ClassPathXmlApplicationContext(config);

    // 接口名的首字母小写来获取对象
    StudentDao dao = (StudentDao) context.getBean("studentDao");

    // 测试插入数据
    Student student = new Student();
    student.setName("李四");
    student.setAge(20);
    dao.insertStudent(student);
}
```

![image-20210330195602581](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210330195602.png)

### 9、向 Service 注入接口名

向 Service 注入 Mapper 代理对象时需要注意，由于通过 Mapper 扫描配置器MapperScannerConfigurer 生成的 Mapper 代理对象没有名称，所以在向 Service 注入 Mapper 代理时，无法通过名称注入。

- 但可通过接口的简单类名（接口首字母小写）注入，因为生成的是这个 Dao 接口的对象。

```xml
<!--声明service-->
<bean id="studentService" class="com.xxxx.service.impl.StudentServiceImpl">
    <property name="studentDao" ref="studentDao"/>
</bean>
```



### 10、Spring 配置文件全部配置

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
    <bean id="factory" class="org.mybatis.spring.SqlSessionFactoryBean" >
        <!--指定数据源-->
        <property name="dataSource" ref="myDataSource"/>

        <!-- 指定mybatis主配置文件
             Resource可以直接使用 value 属性赋值。
        -->
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
        <property name="studentDao" ref="studentDao"/>
    </bean>
</beans>
```

### 11、测试 Dao 访问数据库

```java
@Test
public void test03() {
    String config = "applicationContext.xml";
    ApplicationContext context = new ClassPathXmlApplicationContext(config);

    StudentService service = (StudentService) context.getBean("studentService");

    // 通过 service 测试插入数据
    Student student = new Student();
    student.setName("王七");
    student.setAge(26);
    service.addStudent(student);
}
```

![image-20210330203328346](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210330203328.png)

**测试查询**

```java
@Test
public void test04() {
    String config = "applicationContext.xml";
    ApplicationContext context = new ClassPathXmlApplicationContext(config);
    StudentService service = (StudentService) context.getBean("studentService");

    // 查询
    List<Student> students = service.queryStudents();
    // forEach 方法
    students.forEach(System.out::println);
}
```



![image-20210330203501015](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210330203501.png)

