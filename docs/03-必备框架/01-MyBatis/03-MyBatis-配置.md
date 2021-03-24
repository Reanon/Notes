## 五、MyBatis 配置文件

mybatis配置文件两大类：

1. mybatis 主配置文件：提供mybatis全局设置的，包含的内容日志、数据源、mapper文件位置
2. mybatis 的 mapper文件：写sql语句的，一个表一个mapper文件

### 5.1 主配置文件

前项目中使用的 mybatis.xml 是主配置文件

主配置文件特点：

1、 xml 文件，需要在头部使用约束文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
```

2、根元素：`<configuration>`

3、主要包含内容：

- 定义别名
- 数据源
- mapper 文件

#### settings部分

settings是 MyBatis 中全局的调整设置，它们会改变 MyBatis 的运行时行为，应谨慎设置。

```xml

<settings>
    <!--设置日志显示 -->
    <setting name="logImpl" value="STDOUT_LOGGING"/>
    <!-- 该配置影响的所有映射器中配置的缓存的全局开关。默认值true -->
    <setting name="cacheEnabled" value="true"/>
    <!--延迟加载的全局开关。当开启时，所有关联对象都会延迟加载。特定关联关系中可通过设置fetchType属性来覆盖该项的开关状态。默认值false  -->
    <setting name="lazyLoadingEnabled" value="true"/>
    <!-- 是否允许单一语句返回多结果集（需要兼容驱动）。 默认值true -->
    <setting name="multipleResultSetsEnabled" value="true"/>
    <!-- 使用列标签代替列名。不同的驱动在这方面会有不同的表现， 具体可参考相关驱动文档或通过测试这两种不同的模式来观察所用驱动的结果。默认值true -->
    <setting name="useColumnLabel" value="true"/>
    <!-- 允许 JDBC 支持自动生成主键，需要驱动兼容。 如果设置为 true 则这个设置强制使用自动生成主键，尽管一些驱动不能兼容但仍可正常工作（比如 Derby）。 默认值false  -->
    <setting name="useGeneratedKeys" value="false"/>
    <!--  指定 MyBatis 应如何自动映射列到字段或属性。 NONE 表示取消自动映射；PARTIAL 只会自动映射没有定义嵌套结果集映射的结果集。 FULL 会自动映射任意复杂的结果集（无论是否嵌套）。 -->   
    <!-- 默认值PARTIAL -->  
    <setting name="autoMappingBehavior" value="PARTIAL"/>  
    <setting name="autoMappingUnknownColumnBehavior" value="WARNING"/>  
    <!--  配置默认的执行器。SIMPLE 就是普通的执行器；REUSE 执行器会重用预处理语句（prepared statements）； BATCH 执行器将重用语句并执行批量更新。默认SIMPLE  --> 
    <setting name="defaultExecutorType" value="SIMPLE"/>  
    <!-- 设置超时时间，它决定驱动等待数据库响应的秒数。 -->  
    <setting name="defaultStatementTimeout" value="25"/>  

    <setting name="defaultFetchSize" value="100"/>  
    <!-- 允许在嵌套语句中使用分页（RowBounds）默认值False -->  
    <setting name="safeRowBoundsEnabled" value="false"/>  
    <!-- 是否开启自动驼峰命名规则（camel case）映射，即从经典数据库列名 A_COLUMN 到经典 Java 属性名 aColumn 的类似映射。  默认false -->  
    <setting name="mapUnderscoreToCamelCase" value="false"/>  
    <!-- MyBatis 利用本地缓存机制（Local Cache）防止循环引用（circular references）和加速重复嵌套查询。默认值为 SESSION，这种情况下会缓存一个会话中执行的所有查询。若设置值为 STATEMENT，本地会话仅用在语句执行上，对相同 SqlSession 的不同调用将不会共享数据。  -->  
    <setting name="localCacheScope" value="SESSION"/>  
    <!-- 当没有为参数提供特定的 JDBC 类型时，为空值指定 JDBC 类型。 某些驱动需要指定列的 JDBC 类型，多数情况直接用一般类型即可，比如 NULL、VARCHAR 或 OTHER。  -->  
    <setting name="jdbcTypeForNull" value="OTHER"/>  
    <!-- 指定哪个对象的方法触发一次延迟加载。  -->  
    <setting name="lazyLoadTriggerMethods" value="equals,clone,hashCode,toString"/>  
</settings>
```



### 5.2 dataSource 标签

Mybatis 中访问数据库，可以连接池技术，但它采用的是自己的连接池技术。在 Mybatis 的 mybatis.xml 配置文件中，通过 `<dataSource type="pooled">` 来实现 Mybatis 中连接池的配置。

#### dataSource 类型

Mybatis 将数据源分为三类：

- UNPOOLED：不使用连接池的数据源 
- POOLED：使用连接池的数据源 
- JNDI：使用 JNDI 实现的数据源

![image-20210219135756929](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210219135757.png)

其中 UNPOOLED，POOLED 数据源实现了 javax.sq.DataSource 接口， JNDI 和前面两个实现方式不同，了解可以。

![image-20210219135807628](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210219135807.png)

#### dataSource 配置

在 MyBatis.xml 主配置文件，配置 dataSource：

- MyBatis 在初始化时，根据 `<dataSource>` 的 type 属性来创建相应类型的的数据源 DataSource，即：
  - type="POOLED"：MyBatis 会创建 PooledDataSource 实例
  - type="UNPOOLED" ： MyBatis 会创建 UnpooledDataSource 
  - type="JNDI"：MyBatis 会从 JNDI 服务上查找 DataSource 实例，然后返回使用

```xml
<dataSource type="POOLED">
    <!--连接数据库的四个要素-->
    <property name="driver" value="com.mysql.jdbc.Driver"/>
    <property name="url" value="jdbc:mysql://localhost:3306/ssm?charset=utf-8"/>
    <property name="username" value="root"/>
    <property name="password" value="123456"/>
</dataSource>
```

### 5.3 事务

#### 默认需要手动提交事务

Mybatis 框架是对 JDBC 的封装，所以 Mybatis 框架的事务控制方式，本身也是用 JDBC 的 Connection 对象的 commit()、rollback() 。

- Connection 对象的 setAutoCommit()方法来设置事务提交方式：自动提交和手工提交

`<transactionManager>`标签用于指定 MyBatis所使用的事务管理器：

```java
<transactionManager type="JDBC"/>
```



MyBatis 支持两种事务管理器类型：**JDBC** 与 **MANAGED**

**JDBC**：使用 JDBC 的事务管理机制。即即，通过 Connection 的 commit()方法提交，通过 rollback()方法 回滚。

- 但默认情况下，MyBatis 将**自动提交功能关闭**了，改为了手动提交。
- 即程序中需要**显式的对事务进行提交或回滚**。从日志的输出信息中可以看到。

![image-20210219140335291](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210219140335.png)

==MANAGED==：由容器来管理事务的整个生命周期（如 Spring 容器）。

#### 自动提交事务

设置自动提交的方式，factory 的 openSession() 分为有参数和无参数的。

```java
sqlsession openSession ();
sqlsession openSession(boolean autoCommit) ;
```

有参数为 true，使用自动提交，可以修改 MyBatisUtil 的 getSqlSession()方法。 

```java
session = factory.openSession(true); 
```

- 再执行 insert 操作，无需执行 session.commit()，事务是自动提交的

### 5.4 * 使用数据库属性配置文件

为了方便对数据库连接的管理，DB 连接四要素数据一般都是存放在一个专门的属性文件中的。MyBatis 主配置文件需要从这个属性文件中读取这些数据。

使用步骤：

1. 在resources目录中，创建  `xxxx.properties`
2. 在文件中，使用 key=value的格式定义数据。
   - 例如 jdbc.url=jdbc:mysq://localhost:3306/springdb
3. 在mybatis主配置文件， 使用properties标签引用外部的属性配置文件
4. 在使用值的位置， 使用 `${key}` 获取 key 对应的 value（等号右侧的值）

![image-20210324201325118](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210324201325.png)

1、在 classpath 路径下，创建 `properties` 文件

- 在 resources 目录创建 jdbc.properties 文件，文件名称自定义。
- `&amp;` 替代 `&`

```properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/mybatis?useUnicode=true&amp;characterEncoding=utf-8
jdbc.username=root
jdbc.password=123456
```

2、使用 properties 标签

修改主配置文件，文件开始位置加入：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd"> 
<configuration>
    <!--使用外部属性配置文件
        resource:指定类路径下的某个属性配置文件
    -->
    <properties resource="jdbc.properties" />
```

3、使用 key 指定值

```xml
<environments default="development">
    <environment id="development">
        <transactionManager type="JDBC"/>
        <!--配置数据源： 创建Connection对象。-->
        <dataSource type="POOLED">
            <!--driver:驱动的内容-->
            <property name="driver" value="${jdbc.driver}"/>
            <!--连接数据库的url-->
            <property name="url" value="${jdbc.url}"/>
            <!--用户名-->
            <property name="username" value="${jdbc.username}"/>
            <!--密码-->
            <property name="password" value="${jdbc.password}"/>
        </dataSource>
    </environment>
</environments>
```

### 5.5 typeAliase 类型别名 

Mybatis 支持默认别名，也可以采用自定义别名方式来开发，主要使用在 mybatis.xml 主配置文件定义别名：

```xml
<typeAliases>
    <!--第一种语法格式
            type:java类型的全限定名称（自定义类型）
            alias:自定义别名

            优点： 别名可以自定义
            缺点： 每个类型必须单独定义
        -->
    <typeAlias type="com.bjpowernode.domain.Student" alias="stu" />
    <typeAlias type="com.bjpowernode.vo.QueryParam" alias="qp" />

    <!--第二种方式
            name:包名， mybatis会把这个包中所有类名作为别名（不用区分大小写）
            优点：使用方便，一次给多个类定义别名
            缺点: 别名不能自定义，必须是类名。
        -->
    <package name="com.bjpowernode.domain" />
    <package name="com.bjpowernode.vo" />
</typeAliases>
```

mapper.xml 文件，使用别名表示类型

```xml
<select id="selectStudents" resultType="mystudent">
    select id,name,email,age from student
</select>
```

### 5.6 *mappers 标签

主配置文件中使用 映射器mapper 指定其他 mapper 文件的位置，mapper标签使用的格式有两个常用的方式：

1. resources="mapper文件的路径
2. 使用`<package>`

#### `<mapper resource=" " />` 

使用相对于类路径的资源，从 `classpath` 路径查找文件

例如：

```xml
<mappers>
    <!--第一种方式， resources="mapper文件的路径"
        优点：文件清晰。 加载的文件是明确的。
              文件的位置比较灵活。
        缺点：文件比较多， 代码量会比较大， 管理难度大
    -->
    <mapper resource="com/bjpowernode/dao/StudentDao.xml" /> 
</mappers>
```

#### `<package name=""/>` 

指定包下的所有 Dao 接口

- 注意：此种方法要求 Dao 接口名称和 mapper 映射文件名称相同，且在同一个目录中。

```xml
<mappers>
    <!--
       第二种方式，使用<package>
       name:包名， mapper文件所在的包名。
       特点： 把这个包中的所有mapper文件，一次加载。

       使用要求：
        1. mapper文件和dao接口在同一目录
        2. mapper文件和dao接口名称完全一样。
    -->
    <package name="com.bjpowernode.dao"/>
</mappers>
```

### 5.7 环境标签

`environments`: 环境标签， 在他里面可以配置多个environment

environments 属性：

- default ，必须是某个environment的id属性值，表示 mybatis **默认连接的数据库**

environment：表示一个数据库的连接信息。

- id 自定义的环境的标识。 唯一值。
- transactionManager：事务管理器
  - type：表示事务管理器的类型.
    - JDBC：使用Connection对象， 由 mybatis自己完成事务的处理。
    - MANAGED：管理，表示把事务的处理交给容器实现（由其他软件完成事务的提交，回滚）
- dataSource： 数据源，创建的Connection对象，连接数据库。
  - type：数据源的类型
    - `POOLED`：mybatis会在内存中创建 PooledDataSource 类，管理多个Connection连接对象，**使用的连接池**，主要使用这个。
    - UNPOOLED：**不使用连接池**， mybatis创建一个 UnPooledDataSource 这个类， 每次执行sql语句先创建Connection对象，再执行sql语句，最后关闭Connection
    - JNDI：java的命名和目录服务。

```xml
<!-- 默认使用 online 数据库 -->
<environments default="online">
    <environment id="development">
        <transactionManager type="JDBC"/>
        <!--配置数据源： 创建Connection对象。-->
        <dataSource type="POOLED">
            <!--driver:驱动的内容-->
            <property name="driver" value="${jdbc.driver}"/>
            <!--连接数据库的url-->
            <property name="url" value="${jdbc.url}"/>
            <!--用户名-->
            <property name="username" value="${jdbc.username}"/>
            <!--密码-->
            <property name="password" value="${jdbc.password}"/>
        </dataSource>
    </environment>
    
    <!-- 项目上线后使用的数据库 -->
    <environment id="online">
        <transactionManager type="JDBC"/>
        <!--配置数据源： 创建Connection对象。-->
        <dataSource type="POOLED">
            <!--driver:驱动的内容-->
            <property name="driver" value="com.mysql.jdbc.Driver"/>
            <!--连接数据库的url-->
            <property name="url"
                      value="jdbc:mysql://localhost:3306/springdb"/>
            <!--用户名-->
            <property name="username" value="admin"/>
            <!--密码-->
            <property name="password" value="123456"/>
        </dataSource>
    </environment>
</environments>
```

完整环境配置

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <!--使用外部属性配置文件
        resource:指定类路径下的某个属性配置文件
    -->
    <properties resource="jdbc.properties" />

    <!--设置日志-->
    <settings>
        <setting name="logImpl" value="STDOUT_LOGGING"/>
    </settings>
    <!--声明别名-->
    <typeAliases>
        <!--第一种语法格式
            type:java类型的全限定名称（自定义类型）
            alias:自定义别名

            优点： 别名可以自定义
            缺点： 每个类型必须单独定义
        -->
        <!--<typeAlias type="com.bjpowernode.domain.Student" alias="stu" />
        <typeAlias type="com.bjpowernode.vo.QueryParam" alias="qp" />-->

        <!--第二种方式
            name:包名， mybatis会把这个包中所有类名作为别名（不用区分大小写）
            优点：使用方便，一次给多个类定义别名
            缺点: 别名不能自定义，必须是类名。
        -->
        <package name="com.bjpowernode.domain" />
        <package name="com.bjpowernode.vo" />

    </typeAliases>

    <!--声明插件-->
    <plugins>
        <plugin interceptor ="com.github.pagehelper.PageInterceptor" />
    </plugins>

    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <!--配置数据源： 创建Connection对象。-->
            <dataSource type="POOLED">
                <!--driver:驱动的内容-->
                <property name="driver" value="${jdbc.driver}"/>
                <!--连接数据库的url-->
                <property name="url" value="${jdbc.url}"/>
                <!--用户名-->
                <property name="username" value="${jdbc.username}"/>
                <!--密码-->
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>

    <!--指定其他mapper文件的位置：
        其他其他mapper文件目的是找到其他文件的sql语句
    -->
    <mappers>
        <!--第一种方式， resources="mapper文件的路径"
            优点：文件清晰。 加载的文件是明确的。
                  文件的位置比较灵活。
            缺点：文件比较多， 代码量会比较大， 管理难度大
        -->
        <!--<mapper resource="com/bjpowernode/dao/StudentDao.xml"/>-->

        <!--
           第二种方式，使用<package>
           name:包名， mapper文件所在的包名。
           特点： 把这个包中的所有mapper文件，一次加载。

           使用要求：
            1. mapper文件和dao接口在同一目录
            2. mapper文件和dao接口名称完全一样。
        -->
        <package name="com.bjpowernode.dao" />

    </mappers>
</configuration>
```



## 六、扩展

### 6.1  PageHelper

PageHelper 做数据分页。 在你的select语句后面加入分页的 sql 内容， 如果你使用的mysql数据库， 它就是在select * from student 后面**加入 limit 语句**。

Mybatis 通用分页插件：https://github.com/pagehelper/Mybatis-PageHelper

PageHelper 支持多种数据库： 

1. Oracle 
2. Mysql 
3. MariaDB 
4. ...

#### PageHelper 分页步骤

1、在maven 加入pagehelper 依赖

```xml
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>5.1.10</version>
</dependency>
```

2、在mybatis主配置文件， 加入plugin声明

```xml
<!--在<environments>之前加入-->
<plugins>
    <plugin interceptor="com.github.pagehelper.PageInterceptor" />
</plugins>
```

3、定义接口方法

```java
List<Student> selectAllStudents();
```

4、在 mapper 文件中定义

```xml
<select id="selectAllStudents" resultType="com.bjpowernode.domain.Student">
    select * from student order by id
</select>
```

5、定义测试方法

查询语句之前调用 `PageHelper.startPage(int pageNum, int pageSize)` 静态方法。

- 除了 PageHelper.startPage 方法外，还提供了类似用法的 PageHelper.offsetPage 方法。
- 在你需要进行分页的 MyBatis 查询方法前调用 PageHelper.startPage 静态方法即可
- 紧跟在这个方法后的第一个 **MyBatis 查询方法**会被进行分页。

```java
@Test
public void testSelect() throws IOException {
    // PageHelper.startPage(页码, 每页大小)
    // 获取第 1 页，3 条内容
    PageHelper.startPage(1,3);
    List<Student> studentList = studentDao.selectStudents();
    studentList.forEach( stu -> System.out.println(stu));
}
```



#### PageHelper 原理

首先其会调用 `SELECT count(0) FROM student` 计算表的全部行数，然后进行使用 Limit 进行分页操作。

#### ![image-20210324203047909](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210324203048.png)