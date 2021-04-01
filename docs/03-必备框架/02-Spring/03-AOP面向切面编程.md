# 三、AOP 面向切面编程

AOP 是 Spring 框架中的一个重要内容。利用 AOP 可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。

## 3.1 概述

面向切面编程AOP（Aspect Orient Programming）：

- Aspect ：表示切面， 给业务方法增加的功能叫做切面。 切面一般都是**非业务功能**， 而且切面功能一般都是可以复用的。 例如日志功能，事务功能，权限检查，参数检查，统计信息等等。
- Orient：面向，对着
- Programming：编程。

注意：面向切面编程只是面向对象编程的一种补充。 使用 AOP 减少重复代码，专注业务实现

![在这里插入图片描述](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210329173821.png)



## 3.2 AOP 作用

在源代码中， 业务方法中增加的功能。 

1. 源代码可能改动的比较多
2. 重复代码比较多
3. 代码难于维护

例如转账：在真正转账业务逻辑前后，需要权限控制、日志记录、加载事务、结束事务等**交叉业务逻辑**

- 而这些业务逻辑与主业务逻辑间并无直接关系。
- 但它们的代码量所占比重能达到总代码量的一半甚至还多。
- 它们的存在，不仅产生了大量的“冗余”代码，还大大干扰了主业务逻辑——转账。

**面向切面编程对有什么好处？**

面向切面编程，就是将**交叉业务逻辑封装成切面**，利用 AOP 容器的功能将切面织入到主业务逻辑中。所谓交叉业务逻辑是指，通用的、与主业务逻辑无关的代码，如安全检查、事务、日志、缓存等。

- 若不使用 AOP，则会出现代码纠缠，即交叉业务逻辑与主业务逻辑混合在 一起。这样，会使主业务逻辑变的混杂不清。

面向切面编程的好处：

1. 让切面功能复用
2. 让开发人员专注业务逻辑，提高开发效率
3. 实现业务功能和其他非业务功能解耦合。
4. 给存在的业务方法， 增加功能，不用修改原来的代码

**怎么理解面向切面编程 ？** 

以切面为核心设计开发你的应用。

1. 需要在分析项目功能时，找出切面。
2. 合理的安排切面的执行时间（在目标方法前， 还是目标方法后）
3. 合理的安全切面执行的位置，在哪个类，哪个方法增加增强功能

**什么时候用AOP ？**

要给某些方法增加相同的一些功能，但源代码不能改；

给业务方法增加非业务功能，也可以使用AOP。

## 3.3 AOP 编程术语

1、切面Aspect：切面泛指**交叉业务逻辑**。例如事务处理、日志处理就可以理解为切面。常用的切面是通知 Advice，实际就是对主业务逻辑的一种增强。

2、 连接点 JoinPoint：连接点指可以被切面织入的具体方法，通常业务接口中的方法均为连接点。 **连接切面的业务方法**， 在这个业务方法执行时，会同时执行切面的功能。

3、切入点 Pointcut：切入点指声明的一个或多个连接点的集合，表示**切面执行的位置**。

- 通过切入点指定一组方法， 被标记为 final 的方法是不能作为连接点与切入点的。

4、目标对象 Target： 目标对象指要**被增强的对象** ，即包含主业务逻辑的类的对象。

5、通知 Advice：通知表示**切面的执行时间**，Advice 也叫增强。

- 通知定义了增强代码切入到目标代码的时间点，是目标方法执行之前执行，还是之后执行等。
- 通知类型不同，切入时间不同。
- 切入点定义切入的位置，通知定义切入的时间。

### AOP 三要素

AOP中重要的三个要素： Aspect、 Pointcut、Advice。

- 这个概念的理解是： 在Advice的时间，在Pointcut 的位置，执行Aspect

AOP是一个动态的思想， 在程序运行期间创建代理（ServcieProxy），使用代理执行方法时，增加切面的功能，这个代理对象是存在内存中的。

面向切面编程是从动态角度考虑程序运行过程，AOP 底层就是采用**动态代理**模式实现的，采用了两种代理：

- JDK 的动态代理：使用jdk中的 Proxy、Method、InvocaitonHanderl创建代理对象，jdk动态代理要求目标类必须实现接口
- CGLIB 的动态代理：第三方的工具库，创建代理对象，原理是继承。通过继承目标类，创建子类，子类就是代理对象
  - 目标类不能是final的， 方法也不能是final的



## 3.4 AspectJ 介绍

对于 AOP 这种编程思想，很多框架都进行了实现。Spring 就是其中之一，可以完成面向切面编程，但是比较繁琐和笨重。

- AspectJ 也实现了 AOP 的功能，且其实现方式更为简捷，使用更为方便， 而且还支持注解式开发。所以Spring 又将 AspectJ 的对于 AOP 的实现也引入到了自己的框架中。

> 在 Spring 中使用 AOP 开发时，一般使用 AspectJ 的实现方式。
>
> - 官网地址：http://www.eclipse.org/aspectj/



AspectJ 框架实现 aop 有两种方式：

1. 使用xml的配置文件 ： 配置全局事务
2. 使用注解，在项目中要做 aop 功能，**一般都使用注解**， aspectj 有5个注解。

### Advice 通知

Aspectj 表示切面执行时间，用的通知 `Advice`，这个通知可以使用注解表示。

下面5个注解 表示切面的5个执行时间， 这些注解叫做**通知注解**

- @Before：前置通知
- @AfterRetunring： 后置通知
- @Around：环绕通知
- @AfterThrowing：异常通知
- @After：最终通知



### Pointcut 位置

Pointcut 用来表示**切面执行的位置**， 使用 Aspectj 中切入点表达式。

#### 切入表达式

切入点表达式语法： execution(访问权限 **方法返回值 方法声明(参数)** 异常类型)

- modifiers-pattern：访问权限类型 
- `ret-type-pattern`：返回值类型 
- declaring-type-pattern：包名类名
- `name-pattern(param-pattern)`：方法名(参数类型和参数个数) 
- throws-pattern：抛出异常类型 
- `？`：表示可选的部分

```java
execution(modifiers-pattern? ret-type-pattern declaring-type-pattern?name-pattern(param-pattern) throws-pattern?)
```

示例：

```java
//       访问权限  返回值  包名.类名.方法名(参数类型) 
execution(public   void   com.xxxx.proxy.ServiceProxy.doSome(String,Integer))
```



切入点表达式要匹配的对象就是**目标方法的方法名**。所以，execution 表达式中明显就是方法的签名。

- 注意，表达式中「**返回值类型、方法名**」不可省略，各部分间用空格分开。

在其中可以使用以下符号：

- `*`：0至多个任意字符
- `..`：用在方法参数中，表示任意多个参数
  - 用在包名后，表示当前包及其子包路径
- `+`：用在类名后，表示当前类及其子类
  - 用在接口后，表示当前接口及其实现类

![在这里插入图片描述](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210329175651.png)



## 3.5 AspectJ 开发环境

### Maven 依赖

```xml
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.11</version>
    <scope>test</scope>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.2.5.RELEASE</version>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aspects</artifactId>
    <version>5.2.5.RELEASE</version>
</dependency>

<!-- 插件配置 -->
<build>
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

### 引入 AOP 约束

在 AspectJ 实现 AOP 时，要引入 AOP 的约束。配置文件中使用的 AOP 约束中的标签，均是 AspectJ 框架使用的，而非 Spring 框架本身在实现 AOP 时使用的。 

AspectJ 对于 AOP 的实现有注解和配置文件两种方式，常用是注解方式。

### 实现步骤

#### 定义业务接口与实现类

1、新建maven项目

2、修改pom.xml 加入依赖

- spring-context依赖、spring-aspects依赖（能使用aspectj框架的功能）、junit

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.xxxx</groupId>
    <artifactId>ch07-aspectj-before</artifactId>
    <version>1.0.0</version>
    
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>

    <dependencies>
        <!--spring依赖-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.2.5.RELEASE</version>
        </dependency>
        <!--spring-aspects依赖-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aspects</artifactId>
            <version>5.2.5.RELEASE</version>
        </dependency>
        <!-- junit -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.11</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
    </build>
</project>
```

3、创建业务接口和实现类。

```java
package com.xxxx.service;

public interface SomeService {
    void doSome(String name, Integer age);
}
```

```java
package com.xxxx.service.impl;

import com.xxxx.service.SomeService;

public class SomeServcieImpl implements SomeService {
    @Override
    public void doSome(String name, Integer age) {
        System.out.println("业务方法doSome(), 创建商品订单");
    }
}
```

#### 定义切面类

切面类中定义了若干普通方法，将作为不同的通知方法，用来增强功能

1、创建一个叫做切面类，是一个普通类

1. 在类的上面加入 `@Aspect`
2. 在类中定义方法， 方法表示切面的功能
3. 在方法的上面加入Aspect 框架中的通知注解
   - 例如 `@Before(value="切入点表达式")`

```java
package com.xxxx.handle;

import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;

import java.util.Date;

/**
 * @Aspect: 切面类的注解。
 * 位置：放在某个类的上面
 * 作用：表示当前类是切面类。
 * 切面类：表示切面功能的类
 */
@Aspect
public class MyAspect {
    // 定义方法，表示切面的具体功能
    /*
       前置通知方法的定义
       1）方法是public
       2）方法是void
       3）方法名称自定义
       4）方法可以有参数，如果有是JoinPoint; 也可以没有参数
     */

    /*
    @Before：前置通知
       属性：value 切入点表达式，表示切面的执行位置。
             在这个方法时，会同时执行切面的功能
       位置：在方法的上面
    特点：
     1）执行时间：在目标方法之前先执行的。
     2）不会影响目标方法的执行。
     3）不会修改目标方法的执行结果。
     */
    @Before(value = "execution(public void com.xxxx.service.impl.SomeServcieImpl.doSome(String,Integer))")
    public void myBefore() {
        // 切面的代码
        System.out.println("前置通知，切面的功能，在目标方法之前先执行:" + new Date());
    }
}
```

#### 声明目标对象切面类对象

在定义好切面 Aspect 后，需要通知 Spring 容器，让容器生成 「目标类+  切面」的代理对象。这个代理是由容器自动生成的。

只需要在 Spring 配置文 件中注册一个基于 **aspectj 的自动代理生成器**，其就会自动扫描到@Aspect 注 解，并按通知类型与切入点，将其织入，并生成代理。

1、创建spring配置文件。

1. 声明目标对象
2. 声明切面类对象

3. 声明自动代理生成器

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!--声明目标对象-->
    <bean id="someService" class="com.xxxx.service.impl.SomeServcieImpl"/>

    <!--声明切面类对象-->
    <bean id="myAspect" class="com.xxxx.handle.MyAspect"/>

    <!--声明自动代理生成器：目的是创建目标对象的代理
        调用aspectj框架中的功能， 寻找spring容器中的所有目标对象
        把每个目标对象加入切面类中的功能， 生成代理。
        这个代理对象是修改的内存中的目标对象， 这个目标对象就是代理对象（ServiceProxy）
    -->
    <!--快捷输入 auto...-->
    <aop:aspectj-autoproxy/>

</beans>
```

`<aop:aspectj-autoproxy/>` 的底层是由 AnnotationAwareAspectJAutoProxyCreator 实现的。从其类名就可看出， 是基于 AspectJ 的注解适配自动代理生成器。

- 其工作原理是，`<aop:aspectj-autoproxy/>` 通过扫描找到 `@Aspect` 定义 的切面类，再由切面类根据切入点找到目标类的目标方法，再由通知类型找到 切入的时间点。

#### 测试类

1、创建测试类，测试目标方法执行时，增加切面的功能

```java
package com.xxxx;

import com.xxxx.service.SomeService;
import org.junit.Test;
import org.springframework.context.support.ClassPathXmlApplicationContext;


public class MyTest {
    @Test
    public void test01() {
        String config = "applicationContext.xml";
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext(config);
		// 从spring容器中获取目标对象，目标就是经过aspectj修改后的代理对象
        SomeService service = (SomeService) context.getBean("someService");
        // 代理对象：Service:com.sun.proxy.$Proxy8
        System.out.println("Service:" + service.getClass().getName());
        // 通过代理对象调用方法，实现切面的功能增强
        service.doSome("lisi", 20);
    }
}
// 输出：
// 前置通知，切面的功能，在目标方法之前先执行:Mon Mar 29 21:03:58 CST 2021
// 业务方法doSome(), 创建商品订单
```





## 3.6 AspectJ 的 AOP 

AspectJ 是基于注解的 AOP 实现

@Aspect 是 aspectj 框架中的注解。

- 作用：表示当前类是切面类。
- 切面类：是用来给业务方法增加功能的类，在这个类中有切面的功能代码
- 位置：在类定义的上面

定义方法，方法是实现切面功能的。方法的定义要求：

1. 公共方法 public
2. 方法没有返回值
3. 方法名称自定义
4. 方法可以有参数，也可以没有参数。
   - 如果有参数，参数不是自定义的，有几个参数类型可以使用。

### @Before 前置通知

前置通知方法的定义

1. 方法是public
2. 方法是void
3. 方法名称自定义
4. 方法可以有参数，如果有是JoinPoint; 也可以没有参数

```java
public void myBefore() {
    // 切面的代码
    System.out.println("前置通知，切面的功能，在目标方法之前先执行:" + new Date());
}
```

@Before前置通知注解，方法可以由有 JoinPoint 参数

- value：是切入点表达式，表示切面的功能执行的位置。
- 位置：在方法的上面
- 特点：
  - 在目标方法之前先执行的
  - 不会改变目标方法的执行结果
  - 不会影响目标方法的执行。

```java
// 唯一指定一个方法
@Before(value = "execution(public void com.xxxx.service.impl.SomeServcieImpl.doSome(String,Integer))")
public void myBefore() {
    // 切面的代码
    System.out.println("前置通知，切面的功能，在目标方法之前先执行:" + new Date());
}
```

简化切面表达式

```java
// 省略访问权限
@Before(value = "execution(void com.xxxx.service.impl.SomeServcieImpl.doSome(String,Integer))")
// 返回值类型和参数名用表达式替换
@Before(value = "execution(* *..SomeServcieImpl.doSome(..))")
```

#### JoinPoint

指定通知方法中的参数：JoinPoint

- JoinPoint：表示正在执行的业务方法；
- 作用：可以在通知方法中获取方法执行时的信息，例如方法名称、方法的实参。
- 如果你的切面功能中需要用到方法的信息，就加入JoinPoint.
- 这个JoinPoint参数的值是由框架赋予，所以必须是第一个位置的参数

public interface JoinPoint 的具体方法：

- `Signature getSignature()`：获取方法定义
- `Object[] getArgs()`：获取方法参数
- `jp.getSignature().getName()`：获取方法名称

```java
@Aspect
public class MyAspect {
    @Before(value = "execution(* *..SomeServiceImpl.do*(..) )")
    public void myBefore2(JoinPoint jp){

        // 获取方法的定义
        System.out.println("前置通知中，获取目标方法的定义："+jp.getSignature());
        System.out.println("前置通知中，获取方法名称="+jp.getSignature().getName());
        // 获取方法执行时参数
        // 数组中存放的方法的所有参数
        Object args []= jp.getArgs();
        for(Object obj:args){
            System.out.println("前置通知，获取方法的参数："+obj);
        }

        String methodName = jp.getSignature().getName();
        if("doSome".equals(methodName)){
            // 切面的代码。
            System.out.println("doSome输出日志=====前置通知，切面的功能，在目标方法之前先执行==:"+ new Date());
        } else if("doOther".equals(methodName)){
            System.out.println("doOther前置通知，作为方法名称，参数的记录。");
        }
    }
}
```



### @AfterReturning 后置通知

@AfterReturning 后置通知有value 属性和 `returning` 属性

- value：切入点表达式
-  returning：自定义的变量，表示目标方法的返回值的。自定义变量名必须和通知方法的形参名一样

位置：在方法定义的上面

例如：

```java
@Aspect
public class MyAspect {
    /*
     * 方法的参数：
     * Object res: 表示目标方法的返回值，使用res接收doOther的调用结果。
     * Object res= doOther();
     */
    @AfterReturning(value = "execution(* *..SomeServcieImpl.doOther(..))",returning = "res")
    public void myAfterReturning(Object res) {
        System.out.println("后置通知,在目标方法之后执行的能拿到执行结果：" + res);
        // Object res有什么用
        if ("alice".equals(res)) {
            System.out.println("根据返回值的不同，做不同的增强功能");
        } else if ("add".equals(res)) {
            System.out.println("doOther做了添加数据库,做了备份数据");
        }
    }
}
```

特点：

- 在目标方法之后执行的
- 能够获取到目标方法的返回值，可以根据这个返回值做不同的处理功能
- **不会影响目标方法的执行**

语法：

```java
@AfterReturning(value = "execution(访问权限 方法返回值 方法声明(参数) 异常类型)", returning = "返回值")
```



后置通知使用 JoinPoint 参数需要放在第一个位置

```java
@AfterReturning(value = "execution(* *..SomeServiceImpl.doOther(..))", returning = "res")
public void myAfterReturning(JoinPoint jp, Object res){
    // ...
}
```



### @Around 环绕通知

环绕通知：在目标方法之前开启事务，执行目标方法，在目标方法之后提交事务。

语法：

```java
@Around(value="切入点表达式")
```

环绕通知方法的定义格式：

1. public
2. 必须有个返回值， 推荐使用Object
3. 方法名称自定义
4. 方法必须有参数，固定的参数 ProceedingJoinPoint

属性：

- value切入点表达式

位置：在方法定义的上面

特点：

1. 它是功能最强的通知
2. 在目标方法的前和后都能增强功能。
3. 控制目标方法是否被调用执行
4. 修改原来的目标方法的执行结果， **影响最后的调用结果**

环绕通知等同 JDK 动态代理的 InvocationHandler 接口，@Around环绕通知有 ProceedingJoinPoint 参数：

-  `ProceedingJoinPoint`  相当于反射中 Method。
  - 作用：执行目标方法，等于Method.invoke()

```java
public interface ProceedingJoinPoint extends JoinPoint {}
```

----

#### 使用示例

1、定义业务方法与接口

```java
// 接口
public interface SomeService {
    String doFirst(String name);
}

/* 在不同文件中 */

// 实现类方法
public class SomeServiceImpl implements SomeService {
    @Override
    public String doFirst(String name) {
        System.out.println("执行业务方法 doFirst");
        return "doFirst";
    }
}
```

2、定义切面类

```java
@Aspect
public class MyAspect {
    /*
       环绕置通知方法的定义
       1）方法是public
       2）方法是必须有返回值， 推荐使用Object类型
       3）方法名称自定义
       4）方法必须有ProceedingJoinPoint参数，
     */
    /**
     * 在目标方法的前和后都能增强功能
     * @param proceedingJoinPoint 相当于反射中 Method
     * @return   java.lang.Object 表示调用目标方法希望得到执行结果
     */
    @Around(value = "execution(* *..SomeServiceImpl.doFirst(..))")
    public Object myAround(ProceedingJoinPoint proceedingJoinPoint) {
        System.out.println("执行了环绕通知，在目标方法之前，输出日志时间=="+ new Date());
        // 返回的不是目标方法的执行结果
        return "helloAround";
    }
}
```

3、配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!--声明目标对象-->
    <bean id="someService" class="com.xxxx.service.impl.SomeServiceImpl"/>

    <!--声明切面类对象-->
    <bean id="myAspect" class="com.xxxx.handle.MyAspect"/>

    <!--声明自动代理生成器：目的是创建目标对象的代理-->
    <aop:aspectj-autoproxy/>

</beans>
```

4、测试文件

- 可以看出 Around 切面修改了doFirst()的返回结果。

```java
public class MyTest {
    @Test
    public void test01() {
        String config = "applicationContext.xml";
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext(config);
        SomeService service = (SomeService) context.getBean("someService");
        // Service:com.sun.proxy.$Proxy8
        System.out.println("Service:" + service.getClass().getName());

        String res = service.doFirst("lisa");
        // helloAround
        System.out.println(res);
    }
}
```

可知使用环绕通知时，就是调用切面类中的通知方法。

![image-20201024113735575](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210330114434.png)

#### ProceedingJoinPoint

使用 ProceedingJoinPoint 参数来执行目标方法 doFirst()

- `proceedingJoinPoint.proceed()` 相当于执行目标方法

  - ```java
    // 可以获取目标方法的返回值
    Object proceed() throws Throwable
    ```

```java
@Around(value = "execution(* *..SomeServiceImpl.doFirst(..))")
public Object myAround(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
    System.out.println("执行了环绕通知，在目标方法之前，输出日志时间=="+ new Date());

    // 目标方法的返回值
    Object methodReturn = null;

    // 执行目标方法, ProceedingJoinPoint表示doFirst
    // 相当于 method.invoke()，表示执行doFirst()方法本身
    methodReturn = proceedingJoinPoint.proceed();

    return "helloAround";
}
```

ProceedingJoinPoint 还可以获取方法

```java
@Around("execution(* *..SomeServiceImpl.doFirst(..))")
public Object myAround(ProceedingJoinPoint pjp) throws Throwable {

    // 获取方法执行时的参数值
    String name = "";
    Object args [] = pjp.getArgs();
    if( args != null && args.length > 0){
        Object arg = args[0];
        if(arg !=null){
            name=(String)arg;
        }
    }
    Object methodReturn = null;
    System.out.println("执行了环绕通知，在目标方法之前，输出日志时间=="+ new Date());
    // 执行目标方法  ProceedingJoinPoint，表示doFirst
    if("lisi".equals(name)){
        methodReturn = pjp.proceed();// method.invoke(),表示执行doFirst()方法本身
    }

    if( methodReturn != null){
        methodReturn ="环绕通知中，修改目标方法原来的执行结果";
    }

    System.out.println("环绕通知，在目标方法之后，增加了事务提交功能");

    // return "HelloAround,不是目标方法的执行结果";
    // 返回目标方法执行结果。没有修改的。
    return methodReturn;

}
```









### @AfterThrowing 异常通知

@AfterThrowing 异常通知：注解中有 throwing 属性

语法：

```java
@AfterThrowing(value="切入点表达式"，throwing="自定义变量")
```

@AfterThrowing异常通知

- 属性
  - value：切入点表达式
  - throwing：自定义的变量，表示目标方法抛出的异常对象。变量名必须和方法的参数名一样
- 特点：
  - 在目标方法抛出异常后执行， 没有异常不执行
  - 能获取到目标方法的异常信息。
  - 不是异常处理程序。如果有异常，可以发送邮件，短信进行通知。看做是目标方法的监控程序。

异常通知方法的定义

1. 方法是public
2. 方法是没有返回值。是void
3. 方法名称自定义
4. 方法有参数是Exception

异常通知的执行

```java
try{
    SomeServiceImpl.doSecond(..)
}catch(Exceptoin e){
    myAfterThrowing(e);
}
```

相当于

```java
/*
异常发生可以做：
1.记录异常的时间，位置，等信息。
2.发送邮件，短信，通知开发人员
*/
@AfterThrowing(value = "execution(* *..SomeServiceImpl.doSecond(..))",throwing = "ex")
public void myAfterThrowing(Exception ex){
    System.out.println("异常通知，在目标方法抛出异常时执行的，异常原因是："+ex.getMessage());
}
```

----

示例

业务类方法

```java
@Override
public void doSecond(String name) {
    System.out.println("执行业务方法doSecond,处理库存"+(10/0));
}
```

切面类

```java
@AfterThrowing(value = "execution(* *..SomeServiceImpl.doSecond(..))", throwing = "ex")
public void myAfterThrowing(Exception ex) {
    System.out.println("异常通知，在目标方法抛出异常时执行的，异常原因是：" + ex.getMessage());
    /*
	异常发生可以做：
	1.记录异常的时间，位置，等信息。
	2.发送邮件，短信，通知开发人员
	*/
}
```

测试方法

```java
@Test
public void test01() {
    String config = "applicationContext.xml";
    ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext(config);
    SomeService service = (SomeService) context.getBean("someService");
    // Service:com.sun.proxy.$Proxy8
    System.out.println("Service:" + service.getClass().getName());

    // 异常通知，在目标方法抛出异常时执行的，异常原因是：/ by zero
    service.doSecond("lisa");
}
```



### @After 最终通知

语法：

```java
@After(value="切入点表达式")
```

最终通知方法的定义格式：

1. public
2. 没有返回值，即void
3. 方法名称自定义
4. 方法没有参数，如果有是JoinPoint

属性： value 切入点表达式

位置：在方法的上面

特点：

1. **总是会执行**
2. 在目标方法之后执行
3. 可以用来做程序最后的收尾工作。例如清除临时数据，变量；清理内存

----

示例：

```java
@After(value = "execution(* *..SomeServiceImpl.doThird(..))")
public void myAfter(){
    System.out.println("最终通知，总是会被执行的");
}
```

等同于：

```java
try{
    SomeServiceImpl.doThird(..)
}finally{
    myAfter()
}
```



## 3.7 @Pointcut 

@Pointcut  定义和管理切入点注解，不是通知注解。

- 如果项目中有多个**切入点表达式**是重复的，可以复用的，那么就可以使用@Pointcut

语法：

```java
@Pointcut(value="切入点表达式")
```

属性：value 切入点表达式

位置：在自定义的方法上面；这个方法看做是切入点表达式的别名。

特点：

1. 当使用 Pointcut 定义在一个方法的上面，此时这个**方法名**就是**切入点表达式的别名**。
2. 其它的通知中，value属性就可以使用这个方法名称，代替切入点表达式了。
3. 使用 @Pointcut 注解 的方法一般使用 private 的标识方法，即没有实际作用的方法

---

定义切入点注解 @Pointcut

-  `mypt()`  相当于  `execution(* *..SomeServiceImpl.doThird(..))`

```java
/**
 * @Pointcut: 定义和管理切入点，不是通知注解。
 *     属性：value 切入点表达式
 *     位置：在一个自定义方法的上面， 这个方法看做是切入点表达式的别名。
 *          其他的通知注解中, 可以使用方法名称,就表示使用这个切入点表达式了
 */
@Pointcut("execution(* *..SomeServiceImpl.doThird(..))")
private void mypt(){
    //无需代码
}
```

在其他通知中复用切入点表达式

```java
// 在最终通知复用切入点表达式
@After(value = "mypt()")
public void myAfter(){
    System.out.println("最终通知，总是会被执行的");
}
```

## 3.8 AOP总结

AOP是一种动态的技术思想， 目的是实现业务功能和非业务功能的解耦合。 

- 业务功能是独立的模块，其他功能也是独立的模块。例如事务功能、日志等等。 
- 让这些事务、日志功能是可以被复用的。  

当目标方法需要一些功能时，可以在不修改或不能修改源代码的情况下，使用aop技术在程序执行期间生成代理对象，通过代理执行业务方法同时增加功能。

----

## 3.9 AOP 实例

需求：使用aop做方法的参数检查

要求：

使用 aop做 addNumber() 方法的参数检查：

1. 当addNumber方法的参数不为null的时候且 addNumber 方法的参数是大于0才能执行addNumber()，来计算三个数的和
2. 如果任意一个参数是null 或者小于0，则调用 addNumber 方法返回结果是 -1

思路：

1. 使用环绕通知，在目标方法之前先执行， 可以修改目标方法的执行结果
2. 切面功能：做参数检查

----

1、定义业务接口和实现类

```java
package com.xxxx.service;

public interface NumberService {
    Integer addNumber(Integer num1, Integer num2, Integer num3);
}
```

```java
package com.xxxx.service.impl;

import com.xxxx.service.NumberService;

public class NumberServiceImpl implements NumberService {
    @Override
    public Integer addNumber(Integer num1, Integer num2, Integer num3) {
        Integer res = num1 + num2 + num3;
        return res;
    }
}
```

2、定义切面类

```java
@Aspect
@Component
public class MyAspect {

    @Around(value = "execution(* *..NumberServiceImpl.addNumber(..))")
    public Object checkArgument(ProceedingJoinPoint pjp) throws Throwable {
        System.out.println("执行了环绕通知checkArgument");

        boolean isAll = true;//true表示参数正确的

        //获取执行addNumber方法时的实参
        Object args [] = pjp.getArgs();
        for(Object arg:args){
            if( arg == null){
                //参数是null， 不正确。
                isAll = false;
                break;
            } else {
                // 参数不为null
                Integer temp = (Integer)arg;
                if( temp.intValue() <=0 ){
                    isAll = false;
                    break;
                }
            }
        }

        // 根据参数检查的结果，决定是否执行目标方法
        Object res = null;
        if( isAll ){
            //调用目标方法
            res = pjp.proceed();
        } else {
            //参数检查不正确
            res  = -1;
        }
        return res;
    }
}
```

3、修改配置文件

- 配置pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.xxxx</groupId>
    <artifactId>ch13-aop-homework</artifactId>
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
        <!--spring-aspects依赖-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aspects</artifactId>
            <version>5.2.5.RELEASE</version>
        </dependency>
    </dependencies>

    <build>
    </build>
</project>
```

- applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!--声明目标对象-->
    <bean id="numberService" class="com.xxxx.service.impl.NumberServiceImpl"/>
    <!--声明切面类对象-->
    <bean id="myAspect" class="com.xxxx.handle.MyAspect"/>
    <!--声明自动代理生成器：目的是创建目标对象的代理-->
    <aop:aspectj-autoproxy/>
</beans>
```



4、测试方法

```java
public class MyTest {
    @Test
    public void test01() {
        // 加载配置文件
        String config = "applicationContext.xml";
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext(config);
        // 通过Spring 生成类
        NumberService service = (NumberService) context.getBean("numberService");

        // 查看是否成功生成代理类
        System.out.println(service.getClass().getName());
        
        int res = service.addNumber(-2, 3, 4);
        System.out.println(res);
    }
}
```

