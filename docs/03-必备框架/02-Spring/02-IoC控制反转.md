# 二、Ioc 控制反转

## 2.1 Ioc 概念

控制反转Ioc (Inversion of Control) 是一个理论，一个指导思想，指导开发人员如何使用对象，管理对象的。 把对象的创建、属性赋值、对象的声明周期都交给代码之外的容器管理。

### 控制与反转

 Ioc分为控制和反转

- **控制**： 创建对象、对象的属性赋值、对象之间的关系管理。
- 正转：开发人员在代码中， 使用 new 构造方法创建对象。 开发人员掌握了对象的创建，属性赋值，对象从开始到销毁的全部过程。 开发人员有对对象全部控制。
- **反转**： 把开发人员管理对象的权限转移给了代码之外的容器实现，**由容器完成对象的管理**。

通过容器可以使用容器中的对象（容器已经创建了对象，对象属性赋值了，对象也组装好了）。

- Spring就是一个容器，**可以管理对象，创建对象，给属性赋值**。

```java
public static void main(String args[]){
    Student student = new Student(); // 在代码中， 创建对象。–正转。
}
```

### Ioc技术实现 

**依赖注入** DI（Dependency Injection ）是==IoC的一种技术实现==，程序只需要提供要使用的**对象的名称**就可以了， 对象如何创建， 如何从容器中查找，获取都由容器内部自己实现。 

- 「依赖」： 比如说ClassA类**使用**了ClassB的属性或者方法，叫做 ClassA依赖 ClassB.

```java
public class ClassB{
    public void createOrder(){}
}
public class ClassA{
    // 属性
    private ClassB  b = new ClassB();
    
    public void buy(){
        b.createOrder();
    }
}

// 执行 ClassA 的 buy()
ClassA a  = new ClassA();
a.buy();
```

### Spring  依赖 DI

Spring框架使用的 DI 实现Ioc 

- 通过spring框架， 只需要提供要使用的对象名称就可以了， 从容器中获取名称对应的对象。
- spring底层使用的**反射机制**，通过反射创建对象给属性赋值。

> spring是一个容器，管理对象，给属性赋值， 底层是**反射**创建对象。

spring-conetxt 和 spring-webmvc是spring中的两个模块

- spring-context：是Ioc功能的，创建对象的。
- spring-webmvc 做web开发使用的， 是servlet的升级。spring-webmvc 中也会用到 spring-context 中创建对象的功能的。

### Ioc 优点 

减少对代码的改动， 也能实现不同的功能。 实现解耦合。

## 2.2  Spring的配置文件

spring标准的配置文件：

- 根标签是 beans
- beans 后面的是约束文件说明
- beans 里面是bean声明。

什么是bean： bean 就是 java 对象， spring 容器管理的java对象，叫做bean

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans               http://www.springframework.org/schema/beans/spring-beans.xsd">
</beans>
```

## 2.3 spring容器创建对象

 java 中创建对象有哪些方式：

1. 构造方法：new Student()
2. 反射
3. 序列化
4. 克隆
5. Ioc：容器创建对象
6. 动态代理

spring容器创建对象的特点

1. 容器对象 ApplicationContext：接口通过 ApplicationContext 对象，获取要使用的其他java对象， 执行getBean(`<bean>`的id)

2. spring 默认是调用类的**无参数构造方法**来创建对象

3. spring读取配置文件， **一次创建好所有的java对象**， 都放到map中。

### Ioc的体现

servlet：

1. 创建类继承HttpServelt
2. 在web.xml 注册servlet ， 使用

```xml
<servlet-name> myservlet </servlet-name>				                                 
<servelt-class>xxx.xxx.controller.MyServlet1</servlet-class>
```

1. 没有创建 Servlet对象， 没有 MyServlet myservlet = new MyServlet()
2. Servlet 是Tomcat服务器创建的。 Tomcat也称为容器
   Tomcat作为容器：里面存放的有Servlet对象，Listener ，Filter对象



## 2.4 依赖注入DI 

spring 调用类的无参数构造方法，创建对象。 对象创建后给属性赋值。给属性赋值可以使用：

1. xml配置文件中的标签和属性
2. 使用注解。

DI分类：

1. set注入，也叫做设值注入 
2. 构造注入。



## 2.5 基于xml的DI

在 xml 配置文件中使用标签和属性，完成对象创建，属性赋值。

### set 注入

set 注入，也叫做设值注入

- 概念： spring调用类中的set方法，在set方法中可以完成属性赋值。 **推荐使用**。
- 执行顺序：无参构造创建对象  -> set方法完成赋值

#### 简单类型

1、**简单类型的set注入**：spring调用类的 setter 方法， 可以在set方法中完成属性赋值

- 简单类型：java中的基本数据类型和String

语法：

```xml
<bean id="xxx" class="yyy">
    <property name="属性名" value="简单类型属性值"/>
    ...
</bean>
```

- 创建实例类

```java
package com.xxxx.ba01;

public class Student {
    private  String name;
    private Integer age;
	// get and set 方法...
}
```

- 在 resource 目录下建立对应的 spring-config 文件，一般情况下Spring 配置文件都叫做 `applicationContext.xml` 

![image-20210329094103250](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210329094110.png)

- 声明 bean，给属性赋值
  - 使用 `property` 子元素为bean的属性赋值
  - set 注入找的是**类中的set方法**：即使没有声明的变量，有set方法依然可以执行。

```xml
<!--简单类型set注入-->
<bean id="myStudent" class="com.xxxx.ba01.Student">
    <!--setName("李四")-->
    <property name="name" value="李四"/>
    <!--setAge(22)-->
    <property name="age" value="22" />
    <!--setEmail("xxx)-->
    <property name="email" value="lisi@qq.com" />
</bean>

<!--声明日期类-->
<bean id="mydate" class="java.util.Date">
    <!--setTime(933295249276)-->
    <property name="time" value="933295249276"/>
</bean>
```

- 测试类

```java
package com.xxxx;

import com.xxxx.ba01.Student;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MyTest {
    @Test
    public void test01() {
        // 1. 配置文件
        String config = "ba01/applicationContext.xml";

        // 2.创建容器对象
        ApplicationContext context = new ClassPathXmlApplicationContext(config);

        // 3.从容器中获取指定名称的对象
        // Student student = (Student) context.getBean("myStudent");
        Student student = context.getBean(Student.class);

        // Student{name='李四', age=22}
        System.out.println(student);
        
        // Date date = (Date) context.getBean("mydate");
        Date date = context.getBean(Date.class);
        // date:Fri Jul 30 08:40:49 CST 1999
        System.out.println("date:" + date);
    }
}
```

#### 引用类型

2、**引用类型的 set 注入** ： spring调用类的set方法

语法：

```xml
<bean id="xxx" class="yyy">
    <property name="属性名" ref="bean的id"/>
    ...
</bean>
```

- 在 ba02 包下复制 Student 类，并且新建School 类

```java
public class Student {
    private String name;
    private Integer age;

    // 引用类型
    private School school;
    
    // set 方法...
}
```

```java
public class School {
    private String name;
    private String address;
    // set 方法...
}
```

- 建立 配置文件

```xml
<!--声明School-->
<bean id="mySchool" class="com.xxxx.ba02.School">
    <property name="name" value="东南大学"/>
    <property name="address" value="四牌楼"/>
</bean>
<!--引用类型set注入-->
<bean id="myStudent" class="com.xxxx.ba02.Student">
    <property name="name" value="李四"/>
    <property name="age" value="22"/>
    <!--引用类型的赋值-->
    <!--setSchool(mySchool)-->
    <property name="school" ref="mySchool"/>
</bean>
```

- 测试方法

```java
import com.xxxx.ba02.Student; // 引用对应包下的类
// ...
public class MyTest02 {
    @Test
    public void test01() {
        // 1. 配置文件
        String config = "ba02/applicationContext.xml";

        // 2.创建容器对象
        ApplicationContext context = new ClassPathXmlApplicationContext(config);

        // 3.从容器中获取指定名称的对象
        // Student student = (Student) context.getBean("myStudent");
        // 或者采用指定 class来获取对象, 注意这里的类需要引用正确
        Student student = context.getBean(Student.class);

        // Student{name='李四', age=22, school=School{name='东南大学', address='四牌楼'}}
        System.out.println(student);
    }
}
```



### 构造注入

构造注入：spring调用类中的有参数构造方法， 在创建对象的同时给属性赋值

语法：

```xml
<bean id="xxx" class="yyy">
    <constructor-arg>: 表示一个构造方法的形参
        标签有属性： 
        	name：构造方法形参名
        	index：构造方法的参数位置
        	value：简单类型的形参值
       		ref：引用类型的形参值
</bean>
```

- 新建 Student 类

```java
package com.xxxx.ba03;

public class Student {

    private String name;
    private int age;
    //引用类型
    private School school;

    // 创建有参数构造方法
    public Student(String myname, int myage, School mySchool) {
        System.out.println("Student有参数构造方法");
        // 给属性依次赋值
        this.name = myname;
        this.age = myage;
        this.school = mySchool;
    }
    
    // 省略 toString, 无参构造方法
}

```

- spring-config 配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <!--声明School-->
    <bean id="mySchool" class="com.xxxx.ba03.School">
        <property name="name" value="东南大学"/>
        <property name="address" value="四牌楼"/>
    </bean>

    <!--构造注入，使用name属性-->
    <bean id="myStudent01" class="com.xxxx.ba03.Student">
        <!--name属性 指的是 形参名-->
        <constructor-arg name="myage" value="22"/>
        <constructor-arg name="myname" value="李四"/>
        <constructor-arg name="mySchool" ref="mySchool"/>
    </bean>

    <!--构造注入，使用index，参数的位置，构造方法参数从左往右位置是0,1,2-->
    <bean id="myStudent02" class="com.xxxx.ba03.Student">
        <constructor-arg index="1" value="28"/>
        <constructor-arg index="0" value="张三"/>
        <constructor-arg index="2" ref="mySchool" />
    </bean>

    <!--构造注入，省略index属性-->
    <bean id="myStudent3" class="com.xxxx.ba03.Student">
        <constructor-arg  value="张峰"/>
        <constructor-arg  value="28"/>
        <constructor-arg  ref="mySchool" />
    </bean>
</beans>
```

- 测试方法

```java
public class MyTest03 {
    @Test
    public void test01() {
        // 1. 配置文件
        String config = "ba03/applicationContext.xml";

        // 2.创建容器对象
        ApplicationContext context = new ClassPathXmlApplicationContext(config);

        // 3.从容器中获取指定名称的对象
        Student student01 = (Student) context.getBean("myStudent01");
        Student student02 = (Student) context.getBean("myStudent02");
        Student student03 = (Student) context.getBean("myStudent03");
        
        // Student{name='李四', age=22,school=School{name='东南大学', address='四牌楼'}}}
        System.out.println(student01);
        // Student{name='张三', age=28, ...}}
        System.out.println(student02);
        // Student{name='张峰', age=28, ...}}
        System.out.println(student03);
    }
}
```



### 引用类型的自动注入

spring 可以根据某些规则给引用类型完成赋值， 只对引用类型有效； 规则有byName、byType

#### byName

① **byName 按名称注入** ： java 类中**引用类型属性名称和Spring容器中 bean 的id名称一样**的，且数据类型也是一样的；这些bean能够赋值给引用类型。

语法：

```xml
<bean id="xx" class="yyy" autowire= "byName">
    简单类型属性赋值
</bean>
```

具体示例：

![image-20210329105426724](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210329105426.png)

```xml
<!--声明School，要求 id 要与引用类型属性名称一致-->
<bean id="school" class="com.xxxx.ba04.School">
    <property name="name" value="南京大学"/>
    <property name="address" value="仙林"/>
</bean>

<!-- byName 自动注入 -->
<bean id="myStudent" class="com.bjpowernode.ba04.Student" autowire="byName">
    <property name="name" value="李四"/>
    <property name="age" value="22" />
    <!--引用类型的赋值-->
    <!--<property name="school" ref="school" />-->
</bean>
```



#### byType

② **byType 按类型注入**：Java类中引用类型的数据类型和 Spring 容器中 bean 的 class 值是==同源==关系的，这样的bean赋值给引用类型。 

同源就是一类的意思：

1. java类中 引用类型的数据类型和 bean 的 class 的值是一样的。
2. java类中 引用类型的数据类型和 bean 的 class 的值 **父子类**关系的。
3. java类中 引用类型的数据类型和 bean 的 class 的值 **接口和实现类**关系的

注意： 在xml配置文件中， 符合条件的对象只能有一个， 多余一个是报错的。

语法：

```xml
<bean id="xx" class="yyy" autowire="byType">
	简单类型属性赋值
</bean>
```



```xml
<!--声明School-->
<bean id="mySchool" class="com.xxxx.ba05.School">
    <property name="name" value="航空大学"/>
    <property name="address" value="北京的海淀区"/>
</bean>


<!--声明School的子类-->
<!--<bean id="primarySchool" class="com.xxxx.ba05.PrimarySchool">
	<property name="name" value="北京大兴小学" />
	<property name="address" value="北京的大兴区"/>
</bean>-->

<!-- byType 自动注入 -->
<bean id="myStudent" class="com.xxxx.ba05.Student" autowire="byType">
    <property name="name" value="张三"/>
    <property name="age" value="26" />
    <!--引用类型的赋值-->
    <!--<property name="school" ref="mySchool" />-->
</bean>
```

### 作业 Ioc

需求：模拟一用户注册操作。 

- 定义一个dao接口（UserDao）
  - 接口中的方法 insertUser(SysUser user ) ，方法不需要操作数据， 输出“使用了dao执行insert操作”。
  - 定义接口的实现类 MySqlUserDao
- 定义一个 service 接口（UserService)
  - 定义接口的实现类 UserServiceImpl，包含 UserDao 类型的属性
  - 实现类有方法 addUser(SysUser user)，其调用userDao.insertUser() 完成注册
- 定义一个实体类 SysUser， 表示用户的数据。

要求实现：

- 程序中的 UserServiceImpl、MySqlUserDao 这些类都由 Spring 容器创建和管理
- 给 UserServiceImpl 类中的 userDao 属性赋值； 
- 从Spring容器中获取  UserServiceImpl 类型的对象，调用addUser()方法，输出 "使用了dao执行insert操作"。

![image-20210329112641532](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210329112641.png)

**Dao 接口**

UserDao

```java
package com.xxxx.dao;

import com.xxxx.domain.SysUser;

public interface UserDao {
    void insertUser(SysUser user);
}
```

MySqlUserDaoImpl

```java
package com.xxxx.dao.impl;

import com.xxxx.dao.UserDao;
import com.xxxx.domain.SysUser;

public class MySqlUserDaoImpl implements UserDao {

    @Override
    public void insertUser(SysUser user) {
        System.out.println("使用了dao执行insert操作");
    }
}
```

**service**

UserService

```java
package com.xxxx.service;

import com.xxxx.domain.SysUser;

public interface UserService {
    void addUser(SysUser user);
}
```

UserServiceImpl

```java
package com.xxxx.service.impl;

import com.xxxx.dao.UserDao;
import com.xxxx.domain.SysUser;
import com.xxxx.service.UserService;

public class UserServiceImpl implements UserService {
    private UserDao userDao = null;
    @Override
    public void addUser(SysUser user) {
        userDao.insertUser(user);
    }
}

```

**domain**

```java
package com.xxxx.domain;

public class SysUser {
    private String name;
    private Integer age;
    // get、set、toString
}
```

配置文件


```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <!--把要使用对象，使用bean声明-->
    <bean id="mySqlUserDaoImpl" class="com.xxxx.dao.impl.MySqlUserDaoImpl"/>

    <!--不用声明用户对象，因为逻辑上来讲，用户应该是动态生成的-->
    <!--<bean id="sysUser" class="com.xxxx.domain.SysUser">-->
    <!--    <property name="name" value="alice"/>-->
    <!--    <property name="age" value="21"/>-->
    <!--</bean>-->

    <!-- 通过引用类型注入 -->
    <bean id="userServiceImpl" class="com.xxxx.service.impl.UserServiceImpl" autowire="byType">
    </bean>
</beans>
```

测试方法

```java
package com.xxxx;

import com.xxxx.dao.UserDao;
import com.xxxx.service.UserService;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
public class MyTest {
    @Test
    public void test01() {
        String config = "applicationContext.xml";
        ApplicationContext context = new ClassPathXmlApplicationContext(config);

        UserService service = (UserService) context.getBean("userServiceImpl");
        UserDao dao = (UserDao) context.getBean("mySqlUserDaoImpl");

        // SysUser user = new SysUser();
        // user.setName("lisi");
        // user.setAge(20);
        // serivce.addUser(user);
        dao.insertUser(null);
    }
}
```



### 项目使用多个 Spring 配置文件

在实际应用里，随着应用规模的增加，系统中 Bean 数量也大量增加，导致配置文件变得非常庞大、臃肿。为了避免这种情况的产生，提高配置文件的可读性与可维护性，**可以将 Spring 配置文件分解成多个配置文件**。

分多个配置文件的方式： 

1. 按功能模块分：一个模块一个配置文件。 
2. 按类的功能分：数据库操作相关的类在一个文件、 service类在一个配置文件、  配置redis、事务等等的一个配置文件。

Spring管理多个配置文件： 常用的是包含关系的配置文件。

- 多个配置文件中有一个总文件，**总配置文件**将各其它子文件通过引入
- 总配置文件中使用 `import ` 标签来包含其他的多个配置文件。
- 在 Java 代码中只需要使用总配置文件对容器进行初始化即可。

总配置文件（xml）语法：

- 关键字 `classpath`：表示**类路径**， 也即就是类文件(class文件)所在的目录。 
  - Spring到类路径中加载文件
- 什么时候使用 classpath： 在一个文件中要使用其他的文件， 需要使用classpath。

```xml
<import resource="其他的文件的路径1"/>
<import resource="其他的文件的路径2"/>
```

**多配置文件示例**

1、将配置文件按照功能进行拆分

- 分为 school 和 student 模块

![image-20210329145417042](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210329145417.png)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--School相关类声明-->
    <bean id="school" class="com.xxxx.ba04.School">
        <property name="name" value="南京大学"/>
        <property name="address" value="仙林"/>
    </bean>
</beans>
```


```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">


    <!-- 学生模块的bean -->
    <bean id="myStudent" class="com.xxxx.ba04.Student" autowire="byName">
        <property name="name" value="李四"/>
        <property name="age" value="22" />
        <!--引用类型的赋值-->
        <!--<property name="school" ref="mySchool" />-->
    </bean>

</beans>
```

----

**总配置文件** 

目的是包含其他的多个配置文件， 一般不声明bean

语法：

-  `classpath:` 表示类路径。类文件所在的目录， Spring通过类路径加载配置文件

```xml
 <import resource="classpath:其他文件的路径" />
```

包含关系的配置文件，可使用通配符（*：表示任意字符）

- 注意：总的文件名称不能包含在通配符范围内
- 例如：applicationContext.xml 不能叫做 spring-applicationContext.xml

![image-20210329150259041](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210329150259.png)


```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--
    <import resource="classpath:ba05/spring-school.xml"/>
    <import resource="classpath:ba05/spring-studnet.xml"/>
    -->

    <!--  包含关系的配置文件，可使用通配符（*：表示任意字符）-->
    <import resource="classpath:ba05/spring-*.xml"/>
</beans>
```



## 2.6  * 基于注解的DI

基于注解的DI：使用spring提供的注解，完成 java 对象创建，属性赋值。

注解使用的核心步骤：

1. 加入 maven 的依赖 spring-context ：在加入spring-context的同时， 间接加入spring-aop的依赖。使用**注解必须使用 spring-aop 依赖**
2. 在源代码中加入注解，例如 `@Component`
3. 需要在Spring 配置文件中配置组件扫描器，用于在指定的基本包中扫描注解。

```xml
<!--声明组件扫描器：使用注解必须加入这个语句
	component-scan:翻译过来是组件扫描器，组件是java对象。
	属性： base-package 注解在你的项目中的包名。
		框架会扫描这个包和子包中的所有类，找类中的所有注解。
		遇到注解后，按照注解表示的功能，去创建对象， 给属性赋值。
-->
<context:component-scan base-package="注解所在的包名"/>
```

指定多个包的三种方式：

1、使用多个 `context:component-scan` 指定不同的包路

```xml
<context:component-scan base-package="com.bjpowernode.ba01"/>
<context:component-scan base-package="com.bjpowernode.ba02"/>
```

2、指定 `base-package` 的值使用分隔符

- 分隔符可以使用逗号`，`、分号`；`还可以使用空格，不建议使用空格。

```xml
<context:component-scan base-package="com.bjpowernode.ba01;com.bjpowernode.ba02"/>
```

3、`base-package` 是指定到父包名

- base-package 的值表是基本包，容器启动会扫描包及其子包中的注解，当然也会扫描到子包下级的子包。所以 base-package 可以指定一个父包就可以。
- 但不建议使用顶级的父包，扫描的路径比较多，导致容器启动时间变慢。
- 指定到目标包和合适的，也就是注解所在包全路径。

例如注解的类在 com.bjpowernode.beans 包

```xml
<context:component-scan base-package="com.bjpowernode.beans"/>
```



### @Component

定义 Bean 的注解 @Component

- 需要在类上使用注解 @Component，该注解的 value 属性用于指定该 bean 的 id 值。

@Component: 表示创建对象，对象放到容器中，作用与`<bean>` 相同

- value：表示对象名称，也就是bean的id属性值

@Component 使用方法：

- 全保留：`@Component(value = "myStudent")`
- 可以省略 value：`@Component("myStudent")`，**推荐使用**
- 省略自定义对象名称：`@Component`
  - 使用框架的默认对象名称：类名首字母小写

```java
// 省略指定对象的名称
@Component
public class Student {

    private String name;
    private int age;

    public Student() {
        System.out.println("Student无参数构造方法");
    }
    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```

等同于：

```xml
<bean id="student" class="com.bjpowernode.ba01.Student" />
```

测试一下

```java
public class MyTest {
    @Test
    public void test01(){
        String config="applicationContext.xml";
        ApplicationContext ctx = new ClassPathXmlApplicationContext(config);
	    // 默认类名首字母小写
        Student student = (Student) ctx.getBean("student");
        // student==Student{name='null', age=0}
        System.out.println("student=="+student);
    }
}
```

#### @Repository

#### @Service

#### @Controller

另外，Spring 还提供了 3 个创建对象的注解：

- @Repository：用于对 DAO 实现类进行注解
- @Service ：用于对 Service 实现类进行注解
- @Controller：用于对 Controller 实现类进行注解

这三个注解与 @Component 都可以创建对象，但这三个注解还有其他的含义

- @Repository：创建持久层对象，能访问数据库
- @Service：创建业务层对象，业务层对象可以加入事务功能
- @Controller：注解创建的对象可以作为处理器接收用户的请求。 

@Repository，@Service，@Controller 是对@Component 注解的细化，标注不同层的对象。即持久层对象，业务层对象，控制层对象。

简而言之：

- @Component：普通java对象
- @Respository：dao对象，持久层对象， 表示对象能访问数据库。
- @Service：service对象， 业务层对象， 处理业务逻辑，具有事务能力
- @Controller：控制器对象， 接收请求，显示请求的处理结果。 视图层对象

### @Value

`@Value`简单类型的属性赋值

- value：String类型，表示简单类型的属性值
- 使用位置
  - 在属性上使用注解@Value，该注解的 value 属性用于指定要注入的值。 使用该注解完成属性注入时，类中无需 setter。**推荐使用**
  - 在set方法的上面使用

语法：

```java
// 1.用在属性之上
@Value(value = "xxxx")

// 2.用在setter方法之上 
@Value("22")
public void setAge(int age) {
    System.out.println("setAge===="+age);
    this.age = age;
}

// 3.使用外部属性文件中的数据，语法 @Value(${"key"})
```

例如：

```java
// 用在属性之上，value 可以省略
@Value("李四")
private String name ;
@Value(value = "22")
private int age;
```

使用外部数据

- 定义数据，在resource 包下定义 myconfig.properties

```properties
myname=张三11111
myage=28
```

- 在主配置文件中加载外部配置文件
  - `property-placeholder`：读取properties这样的文件

```xml
<!--读取外部的属性配置文件
	property-placeholder:读取properties这样的文件
-->
<context:property-placeholder location="classpath:/myconf.properties" />
```

- 使用外部属性文件中的数据

```java
// 使用外部属性文件中的数据，语法 @Value(${"key"})
@Value("${myname}")
private String name ;

@Value("${myage}")
public void setAge(int age) {
    System.out.println("setAge="+age);
    this.age = age;
}
```

### @Autowired

@Autowired 可以实现==引用类型==的赋值，使用的是自动注入原理，支持byName、byType

- 这个注解由Spring 提供。
- 需要在引用属性上使用注解@Autowired，该注解默认使用**按类型自动装 配 Bean** 的方式。

#### byType

`@Autowired`：默认使用的是 byType 自动注入

- 属性 `required`：是一个boolean类型的，**默认true**
  - required=true：表示引用类型赋值失败，程序报错，并终止执行。
  - required=false：引用类型如果赋值失败，程序正常执行，引用类型是null

```java
@Autowired(required = false)
@Qualifier("错误的引用")
private School school;
```

- 位置：
  - 在属性定义的上面，无需set方法，推荐使用
  - 在set方法的上面

示例：

1、定义 school类型和 student 类型

```java
@Component("mySchool")
public class School {
    @Value("北京大学")
    private String name;
    
    @Value("北京的海淀区")
    private String address;
    // setter、toString...
}
```

student 类 中引用 school 类，可以通过 @Autowired 来引用。

```java
@Component("myStudent")
public class Student {

    /**
     * 简单类型属性赋值：@Value
     * @Value: 简单类型属性赋值
     */
    @Value("${myname}")
    private String name ;
    @Value("${myage}")
    private int age;

    /**
     * @Autowired: Spring框架提供的，给引用类型赋值的，使用自动注入原理。
     *   位置：1) 在属性定义的上面，无需set方法，推荐使用
     *        2) 在set方法的上面
     */
    // 引用类型，默认使用byType
    @Autowired
    private School school;
    
    public Student() {
        System.out.println("Student无参数构造方法");
    }
	// setter、toString...
}
```



2、配置主配置文件

```xml
<!--声明组件扫描器：使用注解必须加入这个语句
	component-scan:翻译过来是组件扫描器，组件是java对象。
-->
<context:component-scan base-package="com.bjpowernode.ba03"/>
```

3、测试方法

```java
@Test
public void test01(){
    String config="applicationContext.xml";
    ApplicationContext ctx = new ClassPathXmlApplicationContext(config);

    Student student = (Student) ctx.getBean("myStudent");

    System.out.println("student=="+student);
}
```



#### byName

如果要使用byName方式，需要在引用属性上联合使用注解 @Autowired 与@Qualifier

- @Qualifier 的 value 属性用于指定要匹配的 Bean 的 id 值。

具体步骤：

1. 在属性上面加 @Autowired
2. 在属性上面加 @Qualifier(value= "bean的id") ：表示使用**指定名称**的bean完成赋值

1、定义 school类型

```java
@Component("mySchool")
public class School {
    @Value("北京大学")
    private String name;
    
    @Value("北京的海淀区")
    private String address;
    // setter、toString...
}
```



```java
@Component("myStudent")
public class Student {
    @Value("${myname}")
    private String name ;
    @Value("${myage}")
    private int age;

    /**
     * 引用类型
     * 1) @Autowired:给引用类型赋值
     * 2) @Qualifer(value="bean的id")：从容器中找到指定名称的对象，把这个对象赋值给引用类型。
     */
    // byName自动注入
    @Autowired
    @Qualifier("mySchool")
    private School school;
	// 构造、toString...
}
```

2、配置主配置文件

```xml
<context:component-scan base-package="com.bjpowernode.ba04"/>
 <context:property-placeholder location="classpath:/myconf.properties" />
```

3、测试方法

```java
@Test
public void test01(){
    String config="applicationContext.xml";
    ApplicationContext ctx = new ClassPathXmlApplicationContext(config);

    Student student = (Student) ctx.getBean("myStudent");

    System.out.println("student=="+student);
}
```



### @Resource 

@Resource 是来自JDK中的注解，给引用类型赋值的；

- @Resource 使用的也是自动注入原理，既可以按名称匹配 Bean，也可以按类型匹配 Bean。**默认是byName**

jdk1.8带有@Resource注解， 高于jdk1.8没有这个@Resource，需要加入一个依赖

```xml
<dependency>
    <groupId>javax.annotation</groupId>
    <artifactId>javax.annotation-api</artifactId>
    <version>1.3.2</version>
</dependency>
```

使用方法：

- 位置：
  - 在属性定义的上面， 无需set方法，推荐使用。
  - 在set方法的上面
- 默认是byName ：先使用 byName 自动注入，如果 byName 赋值失败， 再使用byType；

#### byType

@Resource 注解若不带任何参数，采用**默认按名称**的方式注入。

- 若按名称不能注入 bean，则会按照类型进行 Bean 的匹配注入。

![image-20210329165138051](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210329165138.png)

#### byName

@Resource 注解指定其 name 属性，则 name 的值即为按照名称进行匹配的 Bean 的 id。

- @Resource(name="bean的名称") ：只使用byName注入

![image-20210329165149185](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210329165149.png)



### 注解实现作业 Ioc

需求：模拟一用户注册操作。 

- 定义dao接口（UserDao)，接口中的方法 insertUser(SysUser user ) 输出「使用了dao执行insert操作」。
- 定义接口的实现类 MySqlUserDao

- 定义一个 service 接口（UserService)，接口方法 addUser(SysUser user)调用userDao.insertUser()
- 定义接口的实现类 UserServiceImpl，其中包含 UserDao 属性。
- 定义一个实体类 SysUser， 表示用户的数据。

要求实现：

- 程序中的 UserServiceImpl、MySqlUserDao 这些类都由 Spring 容器创建和管理
- 从Spring容器中获取  UserServiceImpl 类型的对象，调用addUser()方法，输出 "使用了dao执行insert操作"。

----

1、定义 Dao 接口 和实现类

```java
package com.xxxx.dao;

import com.xxxx.domain.SysUser;

public interface UserDao {
    void insertUser(SysUser user);
}
```



```java
package com.xxxx.dao.impl;

import com.xxxx.dao.UserDao;
import com.xxxx.domain.SysUser;
import org.springframework.stereotype.Repository;

// 持久层使用 @Repository
@Repository("mySqlUserDao")
public class MySqlUserDao implements UserDao {
    public MySqlUserDao() {
        System.out.println("MySqlUserDaoImpl 无参构造");
    }

    @Override
    public void insertUser(SysUser user) {
        System.out.println("使用了dao执行insert操作");
    }
}
```

2、定义service 接口 和 实现类

```java
package com.xxxx.service;

import com.xxxx.domain.SysUser;

public interface UserService {
    void addUser(SysUser user);
}
```

Service 实现类

- 引用类型注解推建使用 `@Resource`

```java
package com.xxxx.service.impl;
import com.xxxx.dao.UserDao;
import com.xxxx.domain.SysUser;
import com.xxxx.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Service;

import javax.annotation.Resource;

// 业务层对象
@Service("userService")
public class UserServiceImpl implements UserService {
    public UserServiceImpl() {
        System.out.println("UserServiceImpl 无参构造");
    }
    // 默认按照名称 userDao 进行匹配
    // 匹配失败按照类型进行匹配
    @Resource
    private UserDao userDao;
    @Override
    public void addUser(SysUser user) {
        userDao.insertUser(user);
    }
}
```

3、配置文件

- 如果要声明组件扫描器需要很多约束文件，不过IDEA可以自动生成

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context
                           https://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 声明组件扫描器 -->
    <context:component-scan base-package="com.xxxx.dao"/>
    <context:component-scan base-package="com.xxxx.service"/>

</beans>
```

4、 测试方法

```java
public class MyTest {
    @Test
    public void test01() {
        String config = "applicationContext.xml";
        ApplicationContext context = new ClassPathXmlApplicationContext(config);

        // 生成实例对象
        UserService service = (UserService) context.getBean("userService");

        service.addUser(null);
    }
}
```



## 2.7  Ioc 总结

Ioc 管理对象，把对象放在容器中，创建、赋值、管理依赖关系。

- Ioc通过管理对象，实现解耦合
- Ioc解决处理业务逻辑对象之间的耦合关系， 也就是 service 和 dao 之间的**解耦合**。

Spring作为容器适合管理什么对象 ？ 

- service对象、dao对象。
- 工具类对象。

不适合交给Spring的对象 ？ 

- 实体类
- servlet、listener、filter等web中的对象，它们是 tomcat 创建和管理的。

### 注解与 XML 的对比

**注解**

- 优点是：方便、直观、高效，代码少，没有配置文件的书写那么复杂。
- 缺点：以硬编码的方式写入到 Java 代码中，修改是需要重新编译代码的。

**XML** 

- 优点：配置和代码是分离的在 xml 中做修改，无需编译代码，只需重启服务器即可将新的配置加载。
- 缺点：编写麻烦，效率低，大型项目过于复杂。