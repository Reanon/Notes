#  五、spring 事务

事务原本是数据库中的概念，在 Dao 层。但一般情况下，需要将事务提升到业务层， 即 Service 层。这样做是为了能够使用事务的特性来管理具体的业务。

在 Spring 中通常可以通过以下两种方式来实现对事务的管理：

1. 使用 Spring 的事务注解管理事务
2. 使用 AspectJ 的 AOP 配置管理事务

## 5.1 事务的概念

事务是指一组 sql 语句的集合， 集合中有多条sql语句可能是insert、update、select、delete， 希望这些多个sql语句都能成功或者都失败， 这些sql语句的执行是一致的，作为一个整体执行。

```mysql
-- mysql 执行事务
beginTransaction 开启事务
insert into student() values.....
select * from student where id=1001
update school set name=xxx where id=1005
endTransaction   -- 事务结束
```



**什么情况下需要使用事务？** 

一个操作需要多条（2条或2条以上的sql）sql语句一起完成， 操作才能成功。

- 当操作涉及得到多个表或者是多个sql语句，需要保证这些语句都是成功才能完成功能
- 或者都失败保证操作是符合要求的。

**在程序中事务在哪说明？**

应该声明在 service 类的业务方法上，因为业务方法会调用多个dao方法，执行多个sql语句。

- 表示业务方法执行时，需要事务的支持。

```java
public class AccountService{
    private AccountDao dao; 
    private MoneyDao dao2;
    
    // 在 service（业务类）的public方法上面，需要说明事务。
    public void trans(String a, String b, Integer money){
        dao.updateA();
        dao.updateB();
        dao2.insertA();
    }
}

public class AccountDao{
    public void updateA(){}
    public void updateB(){}
}

public class MoneyDao{
    public void insertA(){}
    public void deleteB(){}
}
```

### 数据库访问技术

#### JDBC

使用jdbc访问数据库，事务处理。

jdbc访问数据库，处理事务 Connection conn ; conn.commit(); conn.rollback();

```java
public void updateAccount(){
    Connection conn = ...
    conn.setAutoCommit(false);
    stat.insert()
    stat.update();
    conn.commit();
    con.setAutoCommit(rue)
}
```

#### MyBatis

mybatis执行数据库，处理事务

mybatis访问数据库，处理事务， SqlSession.commit(); SqlSession.rollback();

```java
public void updateAccount(){
    SqlSession session = SqlSession.openSession(false);
    try{
        session.insert("insert into student...");
    	session.update("update school ...");
    	session.commit(); 
    }catch(Exception e){
        session.rollback();
    } 
}
```

### Spring 统一管理事务

Spring 统一管理事务把不同的数据库访问技术的事务处理统一起来。

- 使用 Spring 的**事务管理器**，管理不同数据库访问技术的事务处理。
- 开发人员只需要掌握 Spring 的事务处理一个方案，就可以实现使用不同数据库访问技术的事务管理。

Spring框架使用**事务管理器对象**管理所有的事务。

- 事务管理器接口：PlatformTransactionManager
  - 作用：定义了事务的操作，用于完成事务的提交、回滚，及获取事务的状态信息
  - 主要是 commit()、rollback()

## 5.2 Spring 事务管理 API

Spring 的事务管理，主要用到两个事务相关的接口。

### 事务管理器

**事务管理器**是 `PlatformTransactionManager` 接口对象。

- 其主要用于完成事务的提交、回滚，及获取事务的状态信息。

#### 常用的两个实现类

事务管理器有很多实现类： 一种数据库的访问技术有一个实现类，由实现类具体完成事务的提交，回滚

- DataSourceTranactionManager：使用 JDBC 或 MyBatis 进行数据库 操作时使用。
- HibernateTransactionManager：使用 Hibernate 进行持久化数据时使用。

事务管理器工作方式：

![image-20201026102015375](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210330210928.png)



#### Spring  提交/回滚时机

**什么时候提交事务、回滚事务 ？**

- 当业务方法正常执行时、没有异常，则事务提交。
- 业务方法抛出了==运行时异常==， 则事务回滚。

Spring 事务的默认回滚方式是：发生运行时异常和 error 时回滚，发生受查(编译)异常时提交。

- 对于受查异常，程序员也可以手工设置其回滚方式。

简而言之：方法中抛出了**运行时异常则事务回滚**；其他情况（正常执行方法、受查异常）就是提交事务。

---

#### 错误与异常

Throwable 类是 Java 语言中所有错误或异常的超类。只有当对象是此类 (或其子类之一)的实例时，才能通过 Java 虚拟机或者 Java 的 throw 语句抛出。

**Error**：程序在运行过程中出现的无法处理的错误，比如 OutOfMemoryError、ThreadDeath、NoSuchMethodError 等。

- 当这些错误发生时，程序是无法处理（捕获或抛出）的，JVM 一般会终止线程。

**Exception**：可以处理的异常情况，异常分为运行时异常与受查异常。

1. RuntimeException 类或其子类是 ==运行时异常==，只有在运行时才出现的异常。
   - 如 NullPointerException、ArrayIndexOutOfBoundsException、illegalArgumentException、IndexOutOfBoundsException等均属于运行时异常。
   - 这些异常由 JVM 抛出，在编译时不要求必须处理（捕获或抛出）
   - 只要代码编写足够仔细，程序足够健壮，运行时异常是可以避免的
2. ==受查异常==，也叫编译时异常，即在代码编写时要求必须捕获或抛出的异常。若不处理，则无法通过编译。
   - 如 IOException、SQLException、ClassNotFoundException、FileNotFoundException 等都属于受查异常。
   - RuntimeException 及其子类以外的异常，均属于受查异常。
   - 用户自定义的 Exception 的子类，即用户自定义的异常也属受查异常。程序员在定义异常时，只要未明确声明定义的为 RuntimeException 的子类，那么定义的就是受查异常。

#### 事务与AOP

事务使用的 AOP 的环绕通知：环绕通知可以在目标方法的前和后都能增强功能，不需要修改代码代码

```java
// Spring 给业务方法在执行时, 增加上事务的切面功能
@Around("execution(*  所有的业务类中的方法)")
public Object myAround(ProceedingJoinPoint pjp) {
    try{
        // 使用Spring的事务管理器，开启事务
        PlatformTransactionManager.beginTransaction();
         // 执行目标方法 doSome()
        pjp.proceed();
        // 业务方法正常执行, 提交事务
        PlatformTransactionManager.commit();
    }catch(Exception e){
        // 业务方法抛出异常, 则回滚事务
        PlatformTransactionManager.rollback();
    }
}
```



### 事务定义接口

事务定义接口 TransactionDefinition 中定义了事务描述相关的三类常量： 事务隔离级别、事务传播行为、事务默认超时时限及对它们的操作。

#### 隔离级别

隔离级别：控制事务之间影响的程度。定义了五个事务隔离级别常量却只有四个隔离级别，这些常量均是以 `ISOLATION_` 开头，即形如 ISOLATION_XXX。

- DEFAULT：采用DB默认的事务隔离级别。
  - MySql 的默认为 REPEATABLE_READ；
  - Oracle 默认为 READ_COMMITTED。
- READ_UNCOMMITTED：读未提交。未解决任何并发问题。
- READ_COMMITTED：读已提交。解决脏读但**存在不可重复读与幻读**。
- REPEATABLE_READ：可重复读。解决脏读、不可重复读，**存在幻读**
- SERIALIZABLE：串行化。不存在并发问题，效率很低。

#### 超时时间

常量 TIMEOUT_DEFAULT 定义了事务底层默认的超时时限，sql 语句的执行时长。超时时间以秒为单位，整数值，默认是 -1

- 超时时间：表示一个业务方法最长的执行时间，没有到达时间没有执行完毕，spring回滚事务。

#### 事务传播行为

事务传播行为是指，处于不同事务中的方法在相互调用时，执行期间事务的维护情况。

事务传播行为常量都是以 PROPAGATION_ 开头，形如 PROPAGATION_XXX。

- **PROPAGATION_REQUIRED**
- **PROPAGATION_REQUIRES_NEW**
- **PROPAGATION_SUPPORTS**

以上三个需要掌握的。

- PROPAGATION_MANDATORY
- PROPAGATION_NESTED
- PROPAGATION_NEVER
- PROPAGATION_NOT_SUPPORTED

REQUIRED：Spring默认传播行为， 方法在调用的时候：

- 如果存在事务就使用当前的事务
- 如果没有事务则新建事务， 方法在新事务中执行。

SUPPORTS： 指定的方法支持当前事务。但若当前没有事务，也可以以非事务方式执行。

- 方法有事务可以正常执行
- 没有事务也可以正常执行。

REQUIRES_NEW：方法需要一个新事务。

- 如果调用方法时，存在一个事务，则原来的事务暂停， 直到新事务执行完毕。  
- 如果方法调用时没有事务，则新建一个事务，在新事务执行代码。

## 5.3 程序举例环境搭建

举例：购买商品 trans_sale 项目

- 本例要实现购买商品，模拟用户下订单，向订单表添加销售记录，从商品表减少库存。

### 1、创建数据库表

创建两个数据库表 sale、 goods

sale 销售表

```mysql
CREATE TABLE `sale` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `gid` int(11) NOT NULL,
  `num` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

![image-20210331162746792](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210331162746.png)

goods 商品表

```mysql
CREATE TABLE `goods` (
  `id` int(11) NOT NULL,
  `name` varchar(80) DEFAULT NULL,
  `amount` int(11) DEFAULT NULL,
  `price` float(10,2) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

![image-20210331163334413](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210331163334.png)

- goods 表数据

```mysql
INSERT INTO `goods` (`id`, `name`, `amount`, `price`) 
VALUES
  (1001, '笔记本', 100, 6000),
  (1002, '手机', 50, 3000) ;
```

![image-20210331163257459](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210331163257.png)

### 2、配置  pom.xml

（1）maven 依赖

- spring依赖、mybatis依赖、mysql驱动、junit依赖
- mybatis-spring依赖：mybatis网站上提供的，用来在spring项目中，创建mybatis对象
- spring有关事务的依赖

```xml
<dependencies>
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

（2）插件

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

全部 pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.xxxx</groupId>
    <artifactId>ch16-trans-sale-annotation</artifactId>
    <version>1.0.0</version>


    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>

    <dependencies>
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



### 3、创建实体类

实体类 Sale、Goods

```java
package com.xxxx.domain;

public class Goods {
    // 主键
    private Integer id;
    // 商品名称
    private String name;
    //库存
    private Integer amount;
    //单价
    private Float price;
    
    // setter、getter、toString...
}
```



```java
package com.xxxx.domain;
public class Sale {
    // 主键
    private Integer id;
    // 购买商品的id
    private Integer gid;
    // 购买商品的数量
    private Integer num;

    public Integer getId() {
        return id;
    }
    
    // setter、getter、toString...
}
```



### 4、定义 dao 接口

- 创建 dao 接口和mapper文件
  - SaleDao
  - GoodsDao

```java
package com.xxxx.dao;

import com.xxxx.domain.Sale;

public interface SaleDao {
    int insertSale(Sale sale);
}
```



```java
package com.xxxx.dao;

import com.xxxx.domain.Goods;

public interface GoodsDao {
    Goods selectById(Integer id);

    // goods:表示是本次购买的商品 id 和 购买数量
    // id:     商品id
    // amount: 本次购买的此商品数量
    int updateGoods(Goods goods);
}
```

### 5、Mapper 文件

定义 dao 接口对应的 sql 映射文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--命名空间 namespace 建议使用对应 dao 接口的全限定名-->
<mapper namespace="com.xxxx.dao.GoodsDao">
    <!-- 根据 id 来查询商品 -->
    <select id="selectById" resultType="com.xxxx.domain.Goods">
        select id, name, amount, price
        from goods
        where id = #{id}
    </select>
    <!-- 更新商品的数量 -->
    <update id="updateGoods">
        update goods
        set amount = amount - #{amount}
        where id = #{id}
    </update>
</mapper>
```



```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.xxxx.dao.SaleDao">
    <!-- 使用 insert, update, delete, select 标签写 sql -->
    <insert id="insertSale">
        insert into sale(gid, num)
        values (#{gid}, #{num})
    </insert>
</mapper>
```



### 6、MyBatis 主配置文件

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
        <package name="com.xxxx.domain"/>
    </typeAliases>

    <!--指定其他mapper文件的位置：其他mapper文件目的是找到其他文件的sql语句-->
    <mappers>
        <package name="com.xxxx.dao" />
    </mappers>
</configuration>
```



### 7、定义 Service 接口 / 实现类

（1）Service 接口 BuyGoodsService

```java
package com.xxxx.service;

public interface BuyGoodsService {
    /***
     * 定义购买商品的方法
     * @param goodsId 购买的商品id
     * @param num 购买数量
     * @return void
     */
    void buy(Integer goodsId, Integer num);
}

```

（2）service 层接口的实现类 BuyGoodsServiceImpl 

```java
package com.xxxx.service.impl;

import com.xxxx.dao.GoodsDao;
import com.xxxx.dao.SaleDao;
import com.xxxx.domain.Goods;
import com.xxxx.domain.Sale;
import com.xxxx.exception.NotEnoughException;
import com.xxxx.service.BuyGoodsService;

public class BuyGoodsServiceImpl implements BuyGoodsService {
    private SaleDao saleDao;
    private GoodsDao goodsDao;

    public void setSaleDao(SaleDao saleDao) {
        this.saleDao = saleDao;
    }

    public void setGoodsDao(GoodsDao goodsDao) {
        this.goodsDao = goodsDao;
    }

    @Override
    public void buy(Integer goodsId, Integer num) {
        System.out.println("buy 方法开始执行...");

        // 生成销售记录
        Sale sale = new Sale();
        sale.setGid(goodsId);
        sale.setNum(num);
        saleDao.insertSale(sale);

        // 查询商品
        Goods good = goodsDao.selectById(goodsId);
        if (good == null) {
            throw new NullPointerException(goodsId + ", 商品不存在");
        } else if (good.getAmount() < num) {
            // 抛出自定义的异常
            throw new NotEnoughException(goodsId + ", 库存不足");
        }

        // 更新库存, 方法传入Goods对象
        Goods buyGoods = new Goods();
        buyGoods.setId(goodsId);
        buyGoods.setAmount(num);
        // 库存商品数量 减去 购买商品的数量
        goodsDao.updateGoods(buyGoods);

        System.out.println("buy 方法执行完毕...");
    }
}
```

### 8、定义异常类

定义 service 层可能会抛出的异常类 NotEnoughException

```java
package com.xxxx.exception;

/**
 * 运行时异常
 */
public class NotEnoughException extends RuntimeException {
    // 无参数构造方法
    public NotEnoughException() {
        super();
    }

    // 有参数构造方法
    public NotEnoughException(String message) {
        super(message);
    }
}
```





### 9、 Spring 配置文件

（1）声明数据源

```xml
<!--加载外部的属性配置文件-->
<context:property-placeholder location="classpath:jdbc.properties"/>

<!--声明数据源DataSource-->
<bean id="myDataSource" class="com.alibaba.druid.pool.DruidDataSource" init-method="init" destroy-method="close">
    <property name="url" value="${jdbc.url}"/>
    <property name="username" value="${jdbc.username}"/>
    <property name="password" value="${jdbc.password}"/>
</bean>
```

外部数据

```properties
jdbc.url=jdbc:mysql://localhost:3306/springdatabase?useUnicode=true&amp;characterEncoding=utf-8
jdbc.username=root
jdbc.password=root123
```



（2）声明SqlSessionFactoryBean

```xml
<!--声明SqlSessionFactoryBean, 在这个类的内部创建SqlSessionFactory-->
<bean id="factory" class="org.mybatis.spring.SqlSessionFactoryBean" >
    <!--指定数据源-->
    <property name="dataSource" ref="myDataSource"/>
    <!-- 指定mybatis主配置文件-->
    <property name="configLocation" value="classpath:mybatis.xml"/>
</bean>
```

（3）声明dao代理的对象

```xml
<!-- 声明 MapperScannerConfigurer -->
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    <!--指定SqlSessionFactory对象的名称-->
    <property name="sqlSessionFactoryBeanName" value="factory"/>
    <!--指定基本包，dao接口所在的包名-->
    <property name="basePackage" value="com.xxxx.dao"/>
</bean>
```

（4）声明自定义service对象

```xml
<!--声明service-->
<bean id="buyGoodsService" class="com.xxxx.service.impl.BuyGoodsServiceImpl">
    <property name="saleDao" ref="saleDao"/>
    <property name="goodsDao" ref="goodsDao"/>
</bean>
```

#### 全部Spring 配置文件

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
    <bean id="buyGoodsService" class="com.xxxx.service.impl.BuyGoodsServiceImpl">
        <property name="saleDao" ref="saleDao"/>
        <property name="goodsDao" ref="goodsDao"/>
    </bean>
</beans>
```



### 10、定义测试类

定义测试类 MyTest，现在就可以在无事务代理的情况下运行了

不同的数据库访问技术，处理事务是不同的

```java
@Test
public void test01() {
    String config = "applicationContext.xml";
    ApplicationContext ctx = new ClassPathXmlApplicationContext(config);

    BuyGoodsService service = (BuyGoodsService) ctx.getBean("buyGoodsService");

    service.buy(1001, 200);
}
```

![image-20210331200958696](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210331200958.png)

## 5.4  Spring 事务注解管理事务

Spring 框架使用自己的注解 @Transactional 控制事务，可将事务织入到相应 public 方法中，实现事务管理。

@Transactional 的所有可选属性如下所示：

1. propagation：事务的传播行为。使用的 Propagation 类的枚举值，默认值为Propagation.REQUIRED
2. isolation：表示隔离级别， 使用 Isolation类的枚举值表示隔离级别。 默认 Isolation.DEFAULT
3. readOnly：用于设置该方法对数据库的操作是否是只读的。该属性为 boolean，默认值为 false。
4. timeout： 用于设置本操作与数据库连接的超时时限。单位为秒，类型为 int， 默认值为-1，即没有时限。 例如 timeout=20
5. rollbackFor：指定需要回滚的异常类。类型为 Class[]，默认值为空数组
   1. 每个值是异常类型的class
   2. 当然，若只有一个异常类时，可以不使用数组。。 
6. rollbackForClassName：指定需要回滚的异常类类名。类型为 String[]，默 认值为空数组。
   1. 值是异常类名称是String类型的值
   2. 若只有一个异常类时，可以不使用数组
7. noRollbackFor：指定不需要回滚的异常类。类型为 Class[]，默认值为空数组。 
   1. class类型的。
8. noRollbackForClassName： 指定不需要回滚的异常类类名。类型为 String[]， 默认值为空数组。

使用位置：

1. 在业务方法的上面
2. 在类的上面

需要注意的是

1. @Transactional 若用在方法上，**只能用于 public 方法上**。
   - 对于其他非 public 方法，如果加上了注解@Transactional，虽然 Spring 不会报错，但不会将指定事务织入到该方法中。
   - 因为 Spring 会忽略掉所有非 public 方法上的 @Transaction 注解。 
2. 若 @Transaction 注解在类上，则表示该类上所有的方法均将在执行时织入事务。

### 实现注解的事务步骤

复制 trans_sale 项目，新项目 trans_sale_annotation

#### 1、 Spring 配置文件声明事务

##### （1）声明事务管理器

- 说明使用哪个事务管理器对象

```xml
<!--声明事务的控制-->
<!--1、声明事务管理器-->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <!--指定数据源DataSource-->
    <property name="dataSource" ref="myDataSource"/>
</bean>
```



##### （2）开启注解驱动

- 声明使用注解管理事务
- transaction-manager：事务管理器 bean 的 id

```xml
<!--2、开启事务注解驱动： 告诉框架使用注解管理事务
	transaction-manager:指定事务管理器的id
-->
<tx:annotation-driven transaction-manager="transactionManager"/>
```

![image-20210331203024635](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210331203024.png)

修改好之后Spring 配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context" xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd">

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
    <bean id="buyGoodsService" class="com.xxxx.service.impl.BuyGoodsServiceImpl">
        <property name="saleDao" ref="saleDao"/>
        <property name="goodsDao" ref="goodsDao"/>
    </bean>

    <!--声明事务的控制-->
    <!--1、声明事务管理器-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <!--指定数据源DataSource-->
        <property name="dataSource" ref="myDataSource"/>
    </bean>

    <!--2、开启事务注解驱动： 告诉框架使用注解管理事务
        transaction-manager:指定事务管理器的id
    -->
    <tx:annotation-driven transaction-manager="transactionManager"/>
</beans>
```



#### 2、业务层 public 方法加入事务属性

- 在类的源代码中加入@Transactional

```java
package com.xxxx.service.impl;

// ...
import org.springframework.transaction.annotation.Transactional;

public class BuyGoodsServiceImpl implements BuyGoodsService {
	// ...
    
    // 第一种设置方式
    // @Transactional 放在public方法的上面, 表示方法有事务功能
    @Transactional(propagation = Propagation.REQUIRED,
            isolation = Isolation.DEFAULT,
            readOnly = false, 
            timeout = 20,
            rollbackFor ={NullPointerException.class,NotEnoughException.class}
    )
    @Override
    public void buy(Integer goodsId, Integer num) {
        // ...
    }
}
```



**第二种设置方式**

rollbackFor 的使用；

1. 框架首先检查方法抛出的异常是不是在 rollbackFor 的数组中， 如果在一定回滚。
2. 如果方法抛出的异步不在 rollbackFor 数组， 框架会继续检查抛出的异常是不是 RuntimeException
   1. 如果是RuntimeException， 一定回滚。
   2.  如果抛出 SQLException、IOException，需要指明`rollbackFor={SQLException.class, IOException.class}`

```java
@Transactional(
    propagation = Propagation.REQUIRED,
    isolation = Isolation.DEFAULT,
    readOnly = false, timeout = 20
)
```

第三种方式

- 使用默认值 REQUIRED ， 发生运行时异常回滚。

```java
@Transactional
```



#### 3、测试方法

```java
@Test
public void test01() {

    String config = "applicationContext.xml";
    ApplicationContext ctx = new ClassPathXmlApplicationContext(config);

    BuyGoodsService service = (BuyGoodsService) ctx.getBean("buyGoodsService");
    // service=com.sun.proxy.$Proxy16
    System.out.println("service=" + service.getClass().getName());
    // 不存在的商品id
    service.buy(1005, 10);
}
```

抛出异常

![image-20210331204029255](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210331204029.png)

发现数据库中表并未被修改，说明事务进行了回退

![image-20210331204402279](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210331204402.png)

销售表中缺了id =7 ，说明 id = 7 被撤销

![image-20210331204546986](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210331204547.png)



#### @Transactional使用的特点

1. Spring框架自己提供的事务控制
2. 适合中小型项目。
3. 使用方便，效率高

## 5.5 Aspectj 的AOP配置管理事务

使用 Aspectj 的AOP，声明事务控制叫做**声明式事务**

使用 XML 配置事务代理的方式的不足是每个目标类都需要配置事务代理。

- 当目标类较多，配置文件会变得非常臃肿。 
- 使用 XML 配置顾问方式可以自动为每个符合切入点表达式的类生成事务代理。
- 其用法很简单，只需将前面代码中关于事务代理的配置删除，再替换为如下内容即可。

声明式事务优缺点：

1. 缺点： 理解难， 配置复杂。

2. 优点： 代码和事务配置是分开的。 控制事务源代码不用修改。能快速的了解和掌控项目的全部事务，**适合大型项目**。


**使用步骤**：

1. pom.xml加入 Spring-aspects 的依赖
2. 在Spring的配置文件声明事务的内容
   1. 声明事务管理器
   2. 声明业务方法需要的事务属性
   3. 声明切入点表达式

### 实现AOP 配置事务步骤

复制 trans_sale 项目，并重命名为 trans_sal_aspectj。

#### 1、 maven 依赖 pom.xml

新加入 aspectj 的依赖坐标

```xml
<!--aspectj 的依赖坐标-->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aspects</artifactId>
    <version>5.2.5.RELEASE</version>
</dependency>
```

全部的 pom.xml 文件

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.xxxx</groupId>
    <artifactId>ch17-trans-sale-aspectj</artifactId>
    <version>1.0.0</version>
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>

    <dependencies>
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
        <!--aspectj 的依赖坐标-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aspects</artifactId>
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



#### 2、Spring的配置文件声明事务

##### （1）在容器中添加事务管理器

```xml
<!--声明式事务：不用写代码 -->
<!--1. 声明事务管理器-->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="myDataSource"/>
</bean>
```



##### （2）声明业务方法的事务属性

 给具体的业务方法需要的事务属性：隔离级别、传播行为、超时

- name: 业务方法名称。配置name的值有三种方法
  - 业务方法的名称
  - 带有部分通配符(*)的方法名称
  - 使用 `*`
- propagation：指定传播行为的值
- isolation：隔离级别
- read-only：是否只读，默认是false
- timeout：超时时间
- rollback-for：指定回滚的异常类列表，使用的异常全限定名称

1. 给具体的业务方法配置事务

```xml
<!--2.声明业务方法的事务属性（隔离级别，传播行为，超时）
	id: 给业务方法配置事务段代码起个名称，唯一值
	transaction-manager:事务管理器的id
-->
<tx:advice id="serviceAdvice" transaction-manager="transactionManager">
    <!--给具体的业务方法增加事务的说明-->
    <tx:attributes>
        <tx:method name="buy"
                   propagation="REQUIRED"
                   isolation="DEFAULT"
                   read-only="false"
                   timeout="20"
                   rollback-for="java.lang.NullPointerException,com.xxxx.exception.NotEnoughException"/>
    </tx:attributes>
</tx:advice>
```

2. 当业务方法有命名规则后， 可以对一些方法使用事务
   - 例如：添加方法都是以 add 开头，修改方法都是以 modify 开头

```xml
<tx:attributes>
    <!--在业务方法有命名规则后， 可以对一些方法使用事务-->
    <tx:method name="add*" propagation="REQUIRES_NEW" rollback-for="java.lang.Exception" />
    <tx:method name="modify*"  propagation="REQUIRED" rollback-for="java.lang.Exception" />
    <tx:method name="remove*"  propagation="REQUIRED" rollback-for="java.lang.Exception" />

    <!--以上方法以外的 * :querySale, findSale, searchSale -->
    <tx:method name="*" propagation="SUPPORTS" read-only="true" />
</tx:attributes>
```

3. 给剩余没有具体名称和命名规则的业务方法声明事务

```xml
<tx:attributes>
    <!--以上方法以外的 * :querySale, findSale, searchSale -->
    <tx:method name="*" propagation="SUPPORTS" read-only="true" />
</tx:attributes>
```



##### （3）声明切入点表达式

声明业务方法的事务属性只是指定了方法名，并没有指定到具体包中的类。

声明切入点表达式可以指定那些包中的类的方法参与事务。

- `*..service..*.*(..)` 可以用来指定任意包下的 service 包和service 子包下的任意类的任意方法。
  - 例如 com.xxx.servcie、com.service、com.order.servcie、com.xxxx.service.good 包下的类的任意方法都可以被包括。

```xml
<!--3、声明切入点表达式： 表示那些包中的类，类中的方法参与事务-->
<aop:config>
    <!--声明切入点表达式
            expression：切入点表达式， 表示那些类和类中的方法要参与事务
            id：切入点表达式的名称，唯一值
        -->
    <aop:pointcut id="servicePointcut" expression="execution(* *..service..*.*(..))" />
    <!--4、关联切入点表达式和事务通知-->
    <aop:advisor advice-ref="serviceAdvice" pointcut-ref="servicePointcut" />
</aop:config>
```

关联切入点表达式和事务通知，注意两者间的对应。

![image-20210401093135267](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210401093135.png)

#### 3、测试方法

```java
@Test
public void test01() {
    String config = "applicationContext.xml";
    ApplicationContext ctx = new ClassPathXmlApplicationContext(config);
    BuyGoodsService service = (BuyGoodsService) ctx.getBean("buyGoodsService");
    service.buy(1001, 12);
}
```



![image-20210401093450557](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210401093450.png)

错误执行的 id 被撤销了，说明执行成功

![image-20210401093527070](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210401093527.png)

### 完整 Spring 配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context" xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">

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
    <bean id="buyGoodsService" class="com.xxxx.service.impl.BuyGoodsServiceImpl">
        <property name="saleDao" ref="saleDao"/>
        <property name="goodsDao" ref="goodsDao"/>
    </bean>

    <!--声明式事务：不用写代码 -->
    <!--1. 声明事务管理器-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="myDataSource"/>
    </bean>

    <!--2.声明业务方法的事务属性（隔离级别，传播行为，超时）
        id:给业务方法配置事务段代码起个名称，唯一值
        transaction-manager:事务管理器的id
    -->
    <tx:advice id="serviceAdvice" transaction-manager="transactionManager">
        <!--给具体的业务方法增加事务的说明-->
        <tx:attributes>
            <tx:method name="buy"
                       propagation="REQUIRED"
                       isolation="DEFAULT"
                       read-only="false"
                       timeout="20"
                       rollback-for="java.lang.NullPointerException,com.xxxx.exception.NotEnoughException"/>
        </tx:attributes>
    </tx:advice>

    <!--3、声明切入点表达式： 表示那些包中的类，类中的方法参与事务-->
    <aop:config>
        <!--声明切入点表达式
            expression：切入点表达式， 表示那些类和类中的方法要参与事务
            id：切入点表达式的名称，唯一值
        -->
        <aop:pointcut id="servicePointcut" expression="execution(* *..service..*.*(..))"/>
        <!--关联切入点表达式和事务通知-->
        <aop:advisor advice-ref="serviceAdvice" pointcut-ref="servicePointcut"/>
    </aop:config>
</beans>
```



### 声明式事务优缺点

1. 缺点： 理解难， 配置复杂。
2. 优点： 
   - 代码和事务配置是分开的，控制事务源代码不用修改。
   - 能快速的了解和掌控项目的全部事务。 适合大型项目。