# 一、Spring 介绍

官网：https://spring.io/

## 1.1 框架介绍

> Spring 是于 2003 年兴起的一个轻量级的 Java 开发框架，它是为了解决企业应用开发的复杂性而创建的。
>
> Spring 的核心是**控制反转（IoC）**和**面向切面编程（AOP）**。能实现模块之间，类之间的解耦合。Spring 是可 以在 Java SE/EE 中使用的轻量级开源框架。

Spring 的主要作用就是为代码**解耦**，降低代码间的耦合度。就是让对象和对象（模块和模块）之间关系不是使用代码关联，而是通过配置来说明。即在 Spring 中说明对象（模块）的关系。

Spring作为容器， 装的是java对象， 可以让spring创建java对象， 给属性赋值。

- spring 可以实现解耦合， 解决java对象之间的耦合， 解决模块之间的耦合。

tomcat也是容器：管理的是servlet、 listener、 filter等对象。

- 创建HelloServlet 类， 写web.xml

spring：创建 SomeServiceImpl,  写spring的配置文件

## 1.2 Spring 优点

1、 轻量

Spring 框架使用的 jar 都比较小，一般在 1M 以下或者几百 kb。Spring 核心功能的所需 的 jar 总共在 3M 左右。 Spring 框架运行占用的资源少，运行效率高。不依赖其他 jar

2、针对接口编程，解耦合

Spring 提供了 **Ioc 控制反转**，由容器管理对象，对象的依赖关系。原来在程序代码中的 对象创建方式，现在由容器完成。对象之间的依赖解耦合。

3、AOP 编程的支持

通过 Spring 提供的**面向切面编程（AOP）**功能，方便进行面向切面的编程，许多不容易用传统 OOP 实现 的功能可以通过 AOP 轻松应付 在 Spring 中，开发人员可以从繁杂的事务管理代码中解脱出来，通过声明式方式灵活地 进行事务的管理，提高开发效率和质量。

4、方便集成各种优秀框架

Spring 不排斥各种优秀的开源框架，相反 Spring 可以降低各种框架的使用难度，Spring 提供了对各种优秀框架（如 Struts、Hibernate、MyBatis）等的直接支持，简化了框架的使用。 

Spring 像插线板一样，其他框架是插头，可以容易的组合到一起。需要使用哪个框架，就把 这个插头放入插线板。不需要可以轻易的移除。

## 1.3 体系结构图

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210306104940372.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0MTU1ODIy,size_16,color_FFFFFF,t_70)

Spring 由 20 多个模块组成，它们可以分为

1. 数据访问/集成（Data Access/Integration）
2. Web
3. 面向切面编程（AOP，Aspects）
4. 提供JVM的代理（Instrumentation）
5. 消息发送（Messaging）
6. 核心容器（Core Container）
7. 测试（Test）

**Spring 工作原理**

![image-20201023090043961](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210329142833.png)

## 1.4  Spring 入门案例

使用spring：spring作为容器管理对象， 开发人员从spring中获取要使用的对象。

实现步骤：

1. 新建maven项目
2. 加入依赖，修改pom.xml
   1. spring-context ： spring依赖
   2. junit： 单元测试
3. 开发人员定义类： 接口和实现类
   1. 类也可以没有接口。
   2. 接口和实现类定义：和没有spring一样。
4. 创建spring的配置文件。 作用：声明对象。
   1. 把对象交给spring创建和管理。
   2. 使用<bean>表示对象声明，一个bean表示一个java对象。
5. 使用容器中的对象。
   1. 创建一个表示spring容器的对象 ApplicationContext
   2. 从容器中，根据名称获取对象，使用getBean("对象名称")

>  具体步骤

### 1.新建maven项目

1、创建一个空项目，然后再空项目里新建 Maven 模块

![image-20210327193510560](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210327193510.png)

- 配置项目名称和包

![image-20210327193606760](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210327193606.png)

- Maven 环境

![image-20210327193654893](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210327193655.png)

2、配置工程环境

- 设置字符集为 UTF-8

![image-20210327193827261](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210327193827.png)

- 设置工程的 SDK 和 JDK 版本

![image-20210327193959582](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210327193959.png)

![image-20210327194034793](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210327194034.png)

### 2.依赖 / 修改pom.xml

1、初始化 pom.xml

- 去掉`<name>xxxx</name>`、`<url>...</url>` 和 `<build>`里的 `<pluginManagement>...</pluginManagement>` 里的所有内容

- 将  `<maven.compiler.source>` 和 `<maven.compiler.target>` 改为 1.8

![image-20210327194523705](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210327194523.png)

2、加入依赖

Spring Core Container 依赖 `spring-beans`、`spring-context`、`spring-core`、`spring-expression` 四个依赖

```xml
<dependencies>
    <!-- spring-context 依赖 -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.2.5.RELEASE</version>
    </dependency>
    <!--单元测试-->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.11</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

![image-20210327195943495](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210327195943.png)



### 3.定义类/接口

- 删除初始的 APP.java
- 在com.xxxx.service 包下 新建接口 SomeService 

```java
package com.xxxx.service;

public interface SomeService {
    void doSome();
}
```

- 在 com.xxxx.service.impl 包下 生成接口实现类（可以在 接口名上使用 ALT + Enter 快速生成实现类）

![image-20210327200451837](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210327200451.png)

```java
package com.xxxx.service.impl;

import com.xxxx.service.SomeService;

public class SomeServiceImpl implements SomeService {
    @Override
    public void doSome() {
        System.out.println("执行了业务方法 doSome()");
    }
}
```



### 4.Spring的配置文件

1、新建 Spring 配置文件

- 在 resources 包下点击鼠标右键，选择【New】–>【XML Configuration File】–>【Spring Config】
- 也在 resources 目录 点击 「Alt + Insert 」 使用首字母进行搜索

注：resource 包下的配置文件在执行时会被拷贝至类路径的根目录

![image-20210327200835370](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210327200835.png)

bean.xml

- 根标签是 beans
- beans 后面的是约束文件说明
- beans 里面是bean声明

什么是bean： bean 就是 java 对象；

- **spring容器管理的java对象叫做bean**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

</beans>
```

2、声明对象

使用bean元素定义一个由IOC容器创建的对象

- `id` 属性指定用于引用bean实例的标识
- `class` 属性指定用于创建bean的全类名
- 使用 `property` 子元素为bean的属性赋值

```xml
<!--声明对象
        id:自定义对象名称，唯一值。(可以没有，spring可以提供默认名称)
        class：类的全限定名称，spring通过反射机制创建对象，不能是接口
        spring根据id，class创建对象， 把对象放入到spring的一个map对象。
            map.put(id,对象)
 -->
<bean id="someService" class="com.xxxx.service.impl.SomeServiceImpl"/>
```



### 5.使用容器对象

- 先创建 `ClassPathXmlApplicationContext` 对象
- XML 配置文件的路径为**类路径 classpath**下的 `bean.xml`；
- 然后获取容器中的 SomeService 对象，

```java
package com.xxxx.service;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class AppMain {

    public static void main(String args[]){
        // 传统 正转 使用对象的方式
        // SomeService service  = new SomeServiceImpl();
        // service.doSome();

        // 1.指定spring配置文件： 从类路径(classpath)之下开始的路径
        String config="beans.xml";

        // 2.创建容器对象， ApplicationContext 表示spring容器对象。 通过ctx获取某个java对象
        ApplicationContext ctx = new ClassPathXmlApplicationContext(config);

        // 3.从容器中获取指定名称的对象， 使用getBean("id")
        // 需要进行强制类型转换
        SomeService service = (SomeService) ctx.getBean("someService");

        // 4.调用对象的方法，接口中的方法
        service.doSome();
    }
}
```

## 1.5 Spring 构造

1、spring默认使用的无参数构造方法，创建对象。

- 如果定义了有参数构造方法， 应该在定义无参数构造方法

```java
public class SomeServiceImpl implements SomeService {

    /**
     * spring默认使用的无参数构造方法，创建对象。
     * 如果定义了有参数构造方法， 应该在定义无参数构造方法
     */
    public SomeServiceImpl() {
        System.out.println("SomeServiceImpl的无参数构造方法");
    }

    @Override
    public void doSome() {
        System.out.println("执行了业务方法doSome()");
    }
}
```

新增测试方法

- `Object getBean(String var1)`：通过 id 来获取 bean 对象
- `<T> T getBean(Class<T> var1)`：可以免得进行类型转换

```java
package com.xxxx;

import com.xxxx.service.SomeService;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MyTest {
    @Test
    public void test01() {
        String config = "beans.xml";
        // ApplicationContext 是一个接口
        ApplicationContext context = new ClassPathXmlApplicationContext(config);

        // <T> T getBean(Class<T> var1) 可以免得进行类型转换
        SomeService service = context.getBean(SomeService.class);
        
        // Object getBean(String var1) 
        // SomeService someService = (SomeService) context.getBean("someService");

        service.doSome();
    }
}
```

2、 spring是在什么时候创建的对象？

创建spring容器对象的时候会读取配置文件，创建文件中声明的java对象。

- 优点：获取对象的速度快， 因为对象已经创建好了
- 缺点：占用内存

```java
public class MyTest {
    @Test
    public void test01() {
        String config = "beans.xml";
        // 生成ApplicationContext 时就会创建 bean.xml 文件中声明的对象
        ApplicationContext context = new ClassPathXmlApplicationContext(config);

        // <T> T getBean(Class<T> var1) 可以免得进行类型转换
        // SomeService service = context.getBean(SomeService.class);
    }
}
```

3、spring容器创建对象， 一次创建几个 ？

在创建容器（ApplicationContext）对象时，会把配置文件中的**所有对象都创建出来**（spring的默认规则）

4、spring创建非自定义类的对象

- 有class就能让spring创建对象

在bean.xml 文件中添加

```xml
<!--创建非自定义对象：Data类-->
<bean id="mydate" class="java.util.Date" />
```

测试一下

```java
@Test
public void test02(){
    String config="beans.xml";
    ApplicationContext context = new ClassPathXmlApplicationContext(config);

    // 创建非自定义类的对象
    Date date = (Date) context.getBean("myDate");
    // date==Sat Mar 27 20:56:05 CST 2021
    System.out.println("date==" + date);
}
```

5、也可以创建没有接口的类



## 1.6 获取容器中对象的信息

- `int getBeanDefinitionCount()`：获取容器中定义对象的数量

- `String[] getBeanDefinitionNames()`：获取容器中定义的对象名称

```java
/**
* 获取容器中对象的信息
*/
@Test
public void test04(){
    String config="beans.xml";
    ApplicationContext context = new ClassPathXmlApplicationContext(config);

    // 获取容器中定义对象的数量
    int nums = context.getBeanDefinitionCount();
    System.out.println("容器中定义对象的数量=="+nums); // 1

    // 获取容器中定义的对象名称
    String names[] = context.getBeanDefinitionNames();
    for(String name:names){
        System.out.println("容器中对象的名称=="+name); // someService
    }
}
```


