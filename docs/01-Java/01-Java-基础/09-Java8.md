# Java 8新特性

> 参考Bilibili尚硅谷课程：[尚硅谷_Java 8 新特性(宋红康主讲)](https://www.bilibili.com/video/BV1Kb411W75N?p=666)

**Java 8新特性汇总**

![image-20200507101934984](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200507101936.png)

**Java 8的改进**

- 速度更快
- 代码更少（增加了新的语法：==Lambda表达式==）
- 引入强大的 ==Stream APl==
- 便于并行
- 最大化减少空指针异常：Optional
- Nashorn引擎，允许在JVM上运行JS应用
- **并行流**就是把一个内容分成多个数据块，并用不同的线程分别处理每个数据块的流。相比较串行的流，并行的流可以很大程度上提高程序的执行效率。
- Java 8中将并行进行了优化，我们可以很容易的对数据进行并行操作。Stream API可以声明性地通过 parallel()与 sequential()在并行流与顺序流之间进行切换



## 一、Lambda表达式

### 1. Lamdba表达式概述

Lambda是一个==匿名函数==，可以把Lambda表达式理解为是**一段可以传递的代码**（将代码像数据一样进行传递）。使用它可以写出更简洁、更灵活的代码。作为一种更紧凑的代码风格，使Java的语言表达能力得到了提升。



### 2. 使用Lambda表达式前后对比

**示例一：**调用Runable接口

```java
import org.junit.Test;

public class LambdaTest {
    @Test
    public void test1() {
        // 1.不使用lambda 表达式
        Runnable runnable1 = new Runnable() {
            @Override
            public void run() {
                System.out.println("hello");
            }
        };
        runnable1.run(); // hello

        // 2.使用Lambda 表达式
        Runnable runnable2 = () ->
                System.out.println("hello lambda");
        runnable2.run(); // hello lambda
    }
}

```

**示例二：**使用Comparator接口

```java
public class LambdaTest {
    @Test
    public void test2() {
        // 1.不使用lambda 表达式
        Comparator<Integer> comparator1 = new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                return Integer.compare(o1, o2);
            }
        };
        int compare1 = comparator1.compare(12, 32);
        System.out.println(compare1);//-1

        // 2.使用lambda 表达式
        Comparator<Integer> comparator2 = (o1, o2) ->
                Integer.compare(o1, o2);
        int compare2 = comparator2.compare(12, 32); // -1
        System.out.println(compare2);

        // 3.方法引用
        Comparator<Integer> comparator3= Integer::compareTo;
        int compare3 = comparator3.compare(12, 32);
        System.out.println(compare3);// -1
    }
}
```



### 3. Lambda 使用 方法

#### 3.1 Lamdba 基本语法

1.举例： `(o1,o2) -> Integer.compare(o1,o2);`

2.格式：

-  `->` ：lambda操作符 或 箭头操作符
- -> 左边：==lambda 形参列表== ，其实就是接口中的抽象方法的**形参列表**
- -> 右边：==lambda 体== ，其实就是重写的抽象方法的**方法体**



#### 3.2  * Lamdba 六种使用情况

3.2.1  无参，无返回值

```java
Runnable r1 = () -> {System.out.println(“hello Lamdba!”)}
```



3.2.2  一个参数，无返回值

```java
Consumer<String> con = (String str) -> {System.out.println(str)}
```



3.2.3 一个参数时，**数据类型可省略**，因为可由编译器推断得出，称为类型推断

```java
Consumer<String> con = (str) -> {System.out.println(str)}
```



3.2.4 Lamdba 若只需要一个参数时，**小括号可以省略**

```java
Consumer<String> con = str -> {System.out.println(str)}
```



3.2.5 Lamdba需要两个以上的参数，多条执行语句，并且可以有返回值

```java
Comparator<Integer>com = (o1,o1) -> {
	Syste.out.println("Lamdba表达式使用");
    return Integer.compare(o1,o2);
}
```



3.2.6 当Lamdba体只有一条语句时，**return和大括号都可以省略**

```java
Comparator<Integer>com = (o1,o1) ->	Integer.compare(o1,o2);
```

##### 测试六种情况

测试一：无参，无返回值

```java
public class LambdaTest2 {
    // 语法格式一：无参，无返回值
    @Test
    public void test1(){
        // 1.不使用lambda 表达式
        Runnable runnable1 = new Runnable() {
            @Override
            public void run() {
                System.out.println("hello");
            }
        };
        runnable1.run(); // hello

        // 2.使用lambda 表达式
        Runnable runnable2 = () ->
                System.out.println("hello lambda");
        runnable2.run(); // hello lambda
    }
}
```

测试二：Lambda 需要一个参数，但是没有返回值。

```java
public class LambdaTest2 {
   // 语法格式二：Lambda 需要一个参数，但是没有返回值。
    @Test
    public void test2() {
        // 1.未使用Lambda表达式
        Consumer<String> stringConsumer = new Consumer<String>() {
            @Override
            public void accept(String s) {
                System.out.println(s);
            }
        };
        stringConsumer.accept("hello");// hello

        // 2.使用lambda 表达式
        Consumer<String> consumer = (String s) -> {
            System.out.println(s);
        };
        consumer.accept("hello Lambda!");// hello Lambda!
    }
}
```

测试三：数据类型可以省略，因为可由编译器推断得出，称为“类型推断”

```java
public class LambdaTest2 {
    // 语法格式三：数据类型可以省略，因为可由编译器推断得出，称为“类型推断”
    @Test
    public void test3() {
        // 类型推断，用左边推断右边
        ArrayList<String> list = new ArrayList<>();
        // 类型推断，用左边推断右边
        int[] arr = {1,2,3,4};

        // 1.未使用Lambda表达式
        Consumer<String> stringConsumer = new Consumer<String>() {
            @Override
            public void accept(String s) {
                System.out.println(s);
            }
        };
        stringConsumer.accept("hello");// hello

        // 2.使用lambda 表达式
        // 类型推断
        Consumer<String> consumer = (s) -> {
            System.out.println(s);
        };
        consumer.accept("hello Lambda!");// hello Lambda!
    }
}
```

测试四：Lambda 若只需要一个参数时，参数的小括号可以省略

```java
public class LambdaTest2 {
    // 语法格式四：Lambda 若只需要一个参数时，参数的小括号可以省略
    @Test
    public void test4() {
        // 1.未使用Lambda表达式
        Consumer<String> stringConsumer = new Consumer<String>() {
            @Override
            public void accept(String s) {
                System.out.println(s);
            }
        };
        stringConsumer.accept("hello");// hello

        // 2.使用lambda 表达式
        // 形参列表只有一个参数时，其一对()可以省略
        Consumer<String> consumer = s -> {
            System.out.println(s);
        };
        consumer.accept("hello Lambda!");// hello Lambda!
    }
}
```

测试五：Lambda 需要两个或以上的参数，多条执行语句，并且可以有返回值

```java
public class LambdaTest2 {
    // 语法格式五：Lambda 需要两个或以上的参数，多条执行语句，并且可以有返回值
    @Test
    public void test5() {
        // 1.不使用lambda 表达式
        Comparator<Integer> comparator1 = new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                System.out.println(o1);
                System.out.println(o2);
                return Integer.compare(o1, o2);
            }
        };
        int compare1 = comparator1.compare(12, 32);
        System.out.println(compare1);//-1

        // 2.使用lambda 表达式
        // 使用类型推断
        Comparator<Integer> comparator2 = (o1, o2) ->{
            System.out.println(o1);
            System.out.println(o2);
            return Integer.compare(o1, o2);
        };

        int compare2 = comparator2.compare(12, 32); // -1
        System.out.println(compare2);
    }
}
```

测试六：当 Lambda 体只有一条语句时，return 与大括号都可以省略

```java
public class LambdaTest2 {
    // 语法格式六：当 Lambda 体只有一条语句时，return 与大括号若有，都可以省略
    @Test
    public void test6() {
        // 1.不使用lambda 表达式
        Comparator<Integer> comparator1 = new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                return Integer.compare(o1, o2);
            }
        };
        int compare1 = comparator1.compare(12, 32);
        System.out.println(compare1);//-1

        // 2.使用lambda 表达式
        // 只有一条执行语句,可以省略这一对{}和return关键字
        Comparator<Integer> comparator2 = (o1, o2) -> o1.compareTo(o2);

        int compare2 = comparator2.compare(12, 32); // -1
        System.out.println(compare2);

        // 1.未使用Lambda表达式
        Consumer<String> consumer1 = new Consumer<String>() {
            @Override
            public void accept(String s) {
                System.out.println(s);
            }
        };
        consumer1.accept("hi!");  // hi!
        System.out.println("====================");
        // 2.使用Lambda表达式
        // lambda体只有一条执行语句,可以省略这一对{}和return关键字
        Consumer<String> consumer2 = s -> System.out.println(s);
        consumer2.accept("hello"); // hello
    }
}
```



#### 3.3 Lambda 使用总结

`->` 左边：

- lambda形参列表 的 **参数类型可以省略**(类型推断)；
- 如果lambda形参列表<u>只有一个参数时</u>，其**一对()也可以省略**

`->` 右边：

- lambda体应该使用一对{}包裹；
- 如果lambda体<u>只有一条执行语句</u>（可能是return语句），可以**省略这一对{}和return关键字**



### 4. Lamdba表达式总结

- Lambda表达式的本质：作为函数式接口的**实例**
- 如果==一个接口中，只声明了一个抽象方法，则此接口就称为函数式接口==。可以在一个接口上使用 `@FunctionalInterface` 注解，这样做可以检查它是否是一个函数式接口。
- 因此以前用匿名实现类表示的现在都可以用Lambda表达式来写。



## 二、*函数式接口

### 1. 函数式接口概述

> 只包含一个抽象方法的接口，称为函数式接口。

可以通过Lambda表达式来创建该接口的对象。（若Lambda表达式抛出一个受检异常（即：非运行时异常），那么该异常需要在目标接口的抽象方法上进行声明）。

- 可以在一个接口上使用`@FunctionalInterface`注解，这样做可以检查它是否是一个函数式接口。同时 javadoc也会包含一条声明，说明这个接口是一个函数式接口。

> Lambda表达式的本质：作为函数式接口的实例

- 在 `java.util.function` 包下定义了Java 8的丰富的函数式接口

**自定义函数式接口**

```java
@FunctionalInterface
public interface MyInterface {
    void method1();
}
```

### 2. 理解函数式接口

Java从诞生日起就是一直倡导“一切皆对象”，在Java里面==面向对象(OOP)编程==是一切。

但是随着python、scala等语言的兴起和新技术的挑战，Java不得不做出调整以便支持更加广泛的技术要求，也即java不但可以支持OOP还可以支持==OOF（面向函数编程)==。

在函数式编程语言当中，函数被当做一等公民对待。在将函数作为一等公民的编程语言中，Lambda表达式的类型是函数。但是在Java8中，有所不同。

在Java8中，**Lambda表达式是对象**，而不是函数，它们必须依附于一类特别的对象类型——函数式接口。

- 简单的说，==在Java8中，Lambda表达式就是一个函数式接口的实例==。这就是Lambda表达式和函数式接口的关系。也就是说，只要一个对象是函数式接口的实例，那么该对象就可以用Lambda表达式来表示。
- 所以以前用匿名实现类表示的现在都可以用Lambda表达式来写。

### 3.  *Java 内置函数式接口

#### 3.1 四大核心函数式接口

![image-20200507110931093](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20201227102331.png)

**应用举例**

##### Consumer<T> 消费型接口

- 参数类型：T
- 返回类型：void
- 用途：对类型为T的对象应用操作，包含方法：T  void `accept(T t)`

```java
public class LambdaTest3 {
    // 消费型接口 Consumer<T> void accept(T t)
    @Test
    public void testConsumer() {
        // 1.不使用lambda表达式
        happyTime(500, new Consumer<Double>() {
            @Override
            public void accept(Double aDouble) {
                // 学习太累了,去天上人间消费500.0
                System.out.println("学习太累了,去天上人间消费" + aDouble);
            }
        });

        // 2.使用lambda表达式
        // 消费: 400.0
        happyTime(400,money -> System.out.println("消费: " + money));
    }
    public void happyTime(double money, Consumer<Double> consumer) {
        consumer.accept(money);
    }
}
```

##### Supplier<T> 供给型接口

- 参数类型：无
- 返回类型：T
- 用途：返回类型为T的对象，包含方法: T `get()`

```java
public class LambdaTest3 {
    // 供给型接口 Supplier<T>  T get()
    @Test
    public void test2() {
        // 未使用Lambda表达式
        Supplier<String> sp = new Supplier<String>() {
            @Override
            public String get() {
                // return T 类型：即String
                return new String("我能提供东西");
            }
        };
        System.out.println(sp.get());
        System.out.println("====================");
        // 2.使用Lambda表达
        Supplier<String> sp1 = () -> new String("我能通过lambda提供东西");
        System.out.println(sp1.get());
    }
}
```

##### Function<T, R>  函数型接口

- 参数类型：T
- 返回类型：R
- 用途：对类型为 T 的对象应用操作，并返回结果。结果是 R类型 的对象。
- 包含方法：R `apply(T t)`

```java
public class LambdaTest3 {
    //函数型接口 Function<T,R>   R apply(T t)
    @Test
    public void test3() {
        Employee employee = new Employee(1001, "Tom", 45, 10000);
        
        // 使用Lambda表达式
        Function<Employee, String> func1 =e->e.getName();
        System.out.println(func1.apply(employee));
        System.out.println("====================");

        // 使用方法引用
        Function<Employee,String>func2 = Employee::getName;
        System.out.println(func2.apply(employee));

    }
}
```

##### Predicate<T> 断定型接口

- 参数类型：T
- 返回类型：boolean
- 用途：确定类型为T的对象是否满足某约束，并返回boolean值。
- 包含方法：boolean `test(T t)`

```java
public class LambdaTest3 {
    // 断定型接口 Predicate<T>    boolean test(T t)
    @Test
    public void testPredicate() {
        // 1.使用匿名内部类
        List<String> list = Arrays.asList("北京", "南京", "天津", "东京", "西京");
        List<String> filterStr1 = filterString(list, new Predicate<String>() {
            @Override
            public boolean test(String s) {
                // 定义规则：如果字符串中包含「京」就返回true
                return s.contains("京");
            }
        });
        // [北京, 南京, 东京, 西京]
        System.out.println(filterStr1);

        // 2.使用Lambda表达式
        // 形参列表只有一个参数时，()可以省略
        // lambda体只有一条执行语句时, 可以省略这一对{}和return关键字
        List<String> filterStr2 = filterString(list, s -> s.contains("京"));
        // [北京, 南京, 东京, 西京]
        System.out.println(filterStr2);
    }
    
    /**
     * 根据给定的规则，过滤集合中的字符串
     * @param    list 需要过滤的字符串
     * @param    predicate 过滤规则
     * @return   java.util.List<java.lang.String>
     */
    public List<String> filterString(List<String> list, Predicate<String> predicate){
        ArrayList<String> filterList = new ArrayList<>();
        for (String s : list) {
            // 如果满足规则 就添加字符串到结果中
            if (predicate.test(s)) {
                filterList.add(s);
            }
        }
        return filterList;
    }
}
```



```java
public class LambdaTest3 {
    // 消费型接口 Consumer<T>     void accept(T t)
    @Test
    public void test1() {
        //未使用Lambda表达式
        Learn("java", new Consumer<String>() {
            @Override
            public void accept(String s) {
                System.out.println("学习什么？ " + s);
            }
        });
        System.out.println("====================");
        //使用Lambda表达
        Learn("html", s -> System.out.println("学习什么？ " + s));

    }

    private void Learn(String s, Consumer<String> stringConsumer) {
        stringConsumer.accept(s);
    }

    // 供给型接口 Supplier<T>  T get()
    @Test
    public void test2() {
        //未使用Lambda表达式
        Supplier<String> sp = new Supplier<String>() {
            @Override
            public String get() {
                return new String("我能提供东西");
            }
        };
        System.out.println(sp.get());
        System.out.println("====================");
        // 2.使用Lambda表达
        Supplier<String> sp1 = () -> new String("我能通过lambda提供东西");
        System.out.println(sp1.get());
    }

    //函数型接口 Function<T,R>   R apply(T t)
    @Test
    public void test3() {
        //使用Lambda表达式
        Employee employee = new Employee(1001, "Tom", 45, 10000);

        Function<Employee, String> func1 =e->e.getName();
        System.out.println(func1.apply(employee));
        System.out.println("====================");

        //使用方法引用
        Function<Employee,String>func2 = Employee::getName;
        System.out.println(func2.apply(employee));

    }

    //断定型接口 Predicate<T>    boolean test(T t)
    @Test
    public void test4() {
        // 使用匿名内部类
        Function<Double, Long> func = new Function<Double, Long>() {
            @Override
            public Long apply(Double aDouble) {
                return Math.round(aDouble);
            }
        };
        System.out.println(func.apply(10.5));
        System.out.println("====================");

        // 使用Lambda表达式
        Function<Double, Long> func1 = d -> Math.round(d);
        System.out.println(func1.apply(12.3));
        System.out.println("====================");

        // 使用方法引用
        Function<Double,Long>func2 = Math::round;
        System.out.println(func2.apply(12.6));

    }
}
```



#### 3.2 其他函数式接口

![image-20200507111244577](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200507111245.png)



### 4. 使用总结

**4.1 何时使用lambda表达式？**

当需要对一个函数式接口实例化的时候，可以使用lambda表达式。

**4.2 何时使用给定的函数式接口？**

如果我们开发中需要定义一个函数式接口，首先看看在已有的jdk提供的函数式接口是否提供了能满足需求的函数式接口。如果有，则直接调用即可，不需要自己再自定义了。



## 三、方法引用

方法引用可以看做是Lambda表达式深层次的表达。换句话说，==方法引用就是Lambda表达式，也就是函数式接口的一个实例==，通过方法的名字来指向一个方法。

- 当要传递给Lambda体的操作是已经实现的方法了，就可以使用方法引用！

**方法引用使用格式**：`类(或对象)::方法名`

- 情况1：对象 `::` 非静态方法
- 情况2：类 `::` 静态方法
- 情况3：类 `::` 非静态方法

**方法引用使用**：

- 接口中的 抽象方法的形参列表 和 返回值类型 与 方法引用的方法的形参列表和返回值类型相同。（针对于情况1和情况2）
- 当函数式接口方法的第一个参数是需要引用方法的调用者，并且第二个参数是需要引用方法的参数(或无参数)时：`ClassName::methodName`（针对于情况3）

**方法引用使用建议**

- 如果给函数式接口提供实例，恰好满足方法引用的使用情境，就可以考虑使用方法引用给函数式接口提供实例。
  - 如果不熟悉方法引用，那么还可以使用lambda表达式。



### 对象::实例方法

```java
// 情况一：对象::实例方法
//  接口中的抽象方法的 形参列表 和 返回值类型 
// 与方法引用的方法的  形参列表 和 返回值类型 相同。
public class MethodReferenceTest {
    // Consumer中的void accept(T t)
    // PrintStream中的void println(T t)
    @Test
    public void test1() {
        // 1.使用Lambda表达
        Consumer<String> consumer1 = s -> System.out.println(s);
        consumer1.accept("中国");  // 中国

        // 2.使用方法引用
        // 类::方法名
        PrintStream ps = System.out;
        // Consumer接口：方法引用是 函数接口实例
        Consumer consumer2 = ps::println;
        consumer2.accept("China");// China
    }
    // Supplier中的T get()
    // Employee中的String getName()
    @Test
    public void test2() {
        // 1.使用Lambda表达
        Employee emp = new Employee(1001, "Bruce", 34, 600);
        Supplier<String> supplier1 = () -> emp.getName();
        System.out.println(supplier1.get()); // Bruce

        // 2.使用方法引用
        Supplier supplier2 = emp::getName;
        System.out.println(supplier2.get());// Bruce
    }
}
```



### 类 :: 静态方法

测试一：`int compare(T t1,T t2)`

```java
Comparator 中的 int compare(T t1,T t2);
Integer 中的 int compare(T t1,T t2);
```



```java
// 情况二：类::静态方法
// Comparator中的int compare(T t1,T t2)
// Integer中的int compare(T t1,T t2)
public class MethodReferenceTest {
    @Test
    public void test3() {
        // 1.使用Lambda表达
        Comparator<Integer> comparator1 = (t1, t2) -> Integer.compare(t1, t2);
        System.out.println(comparator1.compare(32, 45)); // -1

        // 2.使用方法引用
        Comparator<Integer> comparator2 =Integer::compare;
        System.out.println(comparator2.compare(43, 34)); // 1
    }
}
```



测试二：`Function<T,T>`

```java
Function<T,T>中的 R apply(T t);
Math中的Long round(Double d)
```



```java
// 情况二：类::静态方法
// Function<T,T>中的R apply(T t)
// Math中的Long round(Double d)
public class MethodReferenceTest {
    @Test
    public void test4() {
        // 1.使用匿名内部类
        Function<Double, Long> function1 = new Function<Double, Long>() {
            @Override
            public Long apply(Double aDouble) {
                return Math.round(aDouble);
            }
        };
        System.out.println(function1.apply(10.5)); // 11

        // 2.使用Lambda表达式：函数式接口示例赋值 给 接口
        Function<Double,Long> function2 = d -> Math.round(d);
        System.out.println(function2.apply(10.4)); // 10

        // 3.使用方法引用
        Function<Double,Long> function3 = Math::round;
        System.out.println(function3.apply(-10.5));// -10
        System.out.println(function3.apply(-10.6));// -11
    }
}
```



### 类 :: 实例方法

当函数式接口方法的第一个参数是需要引用方法的调用者，并且第二个参数是需要引用方法的参数(或无参数)时，可以使用`ClassName::methodName`方式。

```java
// 情况三：类::实例方法
// Comparator中的int compare(T t1,T t2)
// String中的int t1.compareTo(t2)
public class MethodReferenceTest {
    @Test
    public void test5() {
        // 1.使用Lambda表达式
        Comparator<String> comparator1 = (s1, s2) -> s1.compareTo(s2);
        System.out.println(comparator1.compare("abd", "aba")); // 3

        // 2.使用方法引用
        Comparator<String> comparator2 = String::compareTo;
        System.out.println(comparator2.compare("abc", "abd")); // -1

    }

    // BiPredicate中的boolean test(T t1, T t2);
    // String中的boolean t1.equals(t2)
    @Test
    public void test6() {
        // 1.使用Lambda表达式
        BiPredicate<String, String> predicate1 = (s1, s2) -> s1.equals(s2);
        System.out.println(predicate1.test("abc", "abc")); // true

        // 2.使用方法引用
        BiPredicate<String, String> predicate2 = String::equals;
        System.out.println(predicate2.test("aba", "abd")); // false
    }

    // Function中的R apply(T t)
    // Employee中的String Employee.getName();
    @Test
    public void test7() {
        // 1.使用Lambda表达式
        Employee employee = new Employee(1001, "Tom", 45, 10000);
        Function<Employee, String> function1 = e->e.getName(); // Tom
        System.out.println(function1.apply(employee));

        // 2.使用方法引用
        Function<Employee,String>function2 = Employee::getName;
        System.out.println(function2.apply(employee));
    }
}
```



## 四、构造器和数组的引用

### 1 使用格式

方法引用：类名 `::new`

数组引用：数组类型 `[] :: new`



### 2 使用要求

#### 2.1 构造器引用

和方法引用类似，函数式接口的抽象方法的形参列表和构造器的形参列表一致。抽象方法的返回值类型即为构造器所属的类的类型

#### 2.2 数组引用

可以把数组看做是一个特殊的类，则写法与构造器引用一致。



### 3. 使用举例

#### 3.1 构造器引用

1、Supplier<T> 中的T `get()`

```java
public class ConstructorReferenceTest {
    // 构造器引用
    // 一、Supplier中的T get()
    @Test
    public void test1() {
        // 1.使用匿名内部类
        Supplier<Employee> supplier1 = new Supplier<Employee>() {
            @Override
            public Employee get() {
                return new Employee();
            }
        };
        // Employee{name='null', age=0}
        System.out.println(supplier1.get());

        // 2.使用Lambda表达式
        Supplier<Employee> supplier2 = () -> new Employee("Tom", 23);
        // Employee{name='Tom', age=23
        System.out.println(supplier2.get());

        // 3.使用方法引用
        Supplier<Employee> supplier3 = Employee::new;
        // Employee{name='null', age=0}
        System.out.println(supplier3.get());
    }
}
```



2、`Function<T, R>`中的R apply(T t)

```java
public class ConstructorReferenceTest {
    // 构造器引用
    // 二、Function中的R apply(T t)
    @Test
    public void test2() {
        // 1.使用Lambda表达式
        Function<String, Employee> function1 = name -> new Employee(name);
        // Employee{name='Tom', age=0}
        System.out.println(function1.apply("Tom"));

        // 2.使用方法引用
        Function<String, Employee> function2 = Employee::new;
        // Employee{name='Jerry', age=0}
        System.out.println(function2.apply("Jerry"));
    }
}
```



3、`BiFunction<T, U, R>`中的R apply(T t,U u)

```java
public class ConstructorReferenceTest {
    // 构造器引用
    // 三、BiFunction中的R apply(T t,U u)
    @Test
    public void test3() {
        // 1.使用Lambda表达式
        BiFunction<String, Integer, Employee> function1 = (name, age)
                -> new Employee(name, age);
        // Employee{name='Alice', age=13}
        System.out.println(function1.apply("Alice", 13));

        // 2.使用方法引用
        BiFunction<String,Integer,Employee> function2 = Employee::new;
        // Employee{name='Bob', age=15}
        System.out.println(function2.apply("Bob", 15));
    }
}
```



#### 3.2 数组引用

1、`Function<T, R>`中的R apply(T t)

```java
import java.util.Arrays;
import java.util.function.Function;

public class ArrayReferenceTest {
    // 数组引用
    // Function中的R apply(T t)
    @Test
    public void test1(){
        // 1.使用Lambda表达式
        Function<Integer,String[]>  function1 = length -> new String[length];
        String[] strings = function1.apply(5);
        // [null, null, null, null, null]
        System.out.println(Arrays.toString(strings));

        // 2.使用方法引用
        Function<Integer,String[]> function2 = String[]::new;
        String[] arrays = function2.apply(7);
        System.out.println(arrays); // [Ljava.lang.String;@123772c
        System.out.println(Arrays.asList(arrays));  // [null, null, null,...]
        System.out.println(Arrays.toString(arrays));// [null, null, null,...]
    }
}
```



## 五、StreamAPI

Java8中有两大最为重要的改变。第一个是 Lambda表达式；另外一个则是Stream API。

==Stream APl ( java.util.stream)== 把真正的函数式编程风格引入到Java中。这是目前为止对Java类库最好的补充，因为Stream API可以极大提供Java程序员的生产力，让程序员写出高效率、干净、简洁的代码。

Stream是 Java 8中处理集合的关键抽象概念，它可以指定你希望对集合进行的操作，可以执行非常复杂的查找、过滤和映射数据等操作。==使用Stream API对集合数据进行操作，就类似于使用SQL执行的数据库查询==。也可以使用Stream API来并行执行操作。简言之，Stream API提供了一种高效且易于使用的处理数据的方式。

### 1. Stream API概述

Stream关注的是对数据的运算，与CPU打交道；集合关注的是数据的存储，与内存打交道;

Java8提供了一套api，使用这套api可以对内存中的数据进行过滤、排序、映射、归约等操作。类似于sql对数据库中表的相关操作。

Stream是数据渠道，用于操作数据源（集合、数组等）所生成的元素序列。“集合讲的是数据， Stream讲的是计算！”

> Stream 和 Collection集合的区别

Collection 是一种静态的内存数据结构，而 **Stream 是有关计算的**。

- 前者是主要面向内存，存储在内存中，后者主要是面向 CPU，通过 CPU 实现计算。

**使用注意点:**

1. Stream 自己不会存储元素。
2. Stream 不会改变源对象。相反，他们会返回一个持有结果的新Stream。
3. Stream 操作是延迟执行的。这意味着他们会等到需要结果的时候才执行。



### 2. Stream 使用流程

1、创建Stream：Stream的实例化

- 一个数据源（如:集合、数组），获取一个流

2、 中间操作

- 一个中间操作链（过滤、映射、...），对数据源的数据进行处理

3、终止操作(终端操作)

- 一旦执行终止操作，**就执行中间操作链**，并产生结果。之后，不会再被使用
  

![image-20200507114714381](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200507114715.png)



### 3.  Stream使用方法

#### 3.1 创建Stream

##### **3.1.1 通过集合**

Java 8的Collection接口被扩展，提供了两个获取流的方法：

- default Stream <E> `stream()` : 返回一个**顺序流**
- default Stream <E> `parallelStream()` : 返回一个**并行流**

```java
public class StreamAPITest {
    // 创建Stream方式一：通过集合
    @Test
    public void test1(){
        List<Employee> employee = EmployeeData.getEmployee();
        // 1.default Stream<E> stream()
        // 返回一个顺序流
        Stream<Employee> stream = employee.stream();

        // 2.default Stream<E> parallelStream()
        // 返回一个并行流
        Stream<Employee> parallelStream = employee.parallelStream();
    }
}
```



##### **3.1.2 通过数组**

Java 8中的Arrays的静态方法stream()可以获取数组流

- 调用Arrays类的 static\<T>  Stream \<T> `stream(T[] array)`：返回一个流
- 重载形式，能够处理对应基本类型的数组：
  - public static `IntStream stream(int[]  array)`
  - public static `LongStream stream(long[] array)`
  - public static `DoubleStream stream(double[] array)`

```java
public class StreamAPITest {
    // 创建Stream方式二：通过数组
    @Test
    public void test3(){
        int[] arrays = {1, 2, 3, 4, 5};
        // 1.static <T> Stream<T> stream(T[] array)
        // 调用Arrays类的stream,返回一个流
        IntStream stream1 = Arrays.stream(arrays);

        Employee alice = new Employee("Alice", 23);
        Employee bob = new Employee("Bob", 14);
        Employee[] employees = {alice, bob};
        Stream<Employee> stream2 = Arrays.stream(employees);
    }
}
```



##### **3.1.3 Stream的of()方法**

可以调用Stream类静态方法of()，通过显示值创建一个流。可以用于接收任意数量的参数

- public static \<T> Stream\<T> `of(T...values)`：返回一个流

```java
public class StreamAPITest {
    // 创建Stream方式三：通过Stream的of()
    @Test
    public void test3(){
        // 4.public static <T> Stream<T> of(T...values)
        // 调用Stream类静态方法of(),通过显示值创建一个流
        Stream<Integer> integerStream = Stream.of(1, 2, 3, 4, 5);
    }
}
```



##### **3.1.4 创建无限流**

调用Stream类静态方法迭代

- public static \<T> Stream\<T>  `iterate (final T seed, final UnaryOperator <T> f)`

调用Stream类静态方法generate(Supplier <T> s)生成: 

- public static \<T>  Stream\<T> `generate(Supplier<T> s)`

```java
public class StreamAPITest {
    //创建 Stream方式四：创建无限流
    @Test
    public void test4(){
        // 5.public static<T> Stream<T> iterate(final T seed, final UnaryOperator<T> f)
        // 迭代
        // 遍历前5个偶数
        // 0 2 4 6 8
        Stream.iterate(0, t -> t + 2).limit(5).forEach(System.out::println);

        // 6.public static<T> Stream<T> generate(Supplier<T> s)
        // 生成 5 个随机数
        Stream.generate(Math::random).limit(5).forEach(System.out::println);
    }
}
```



#### 3.2 中间操作

多个中间操作可以连接起来形成一个**流水线**，除非流水线上触发终止操作，否则中间操作不会执行任何的处理，而在终止操作时一次性全部处理，称为“==惰性求值==”。

> 注意执行终止操作后，Stream流就被关闭了，使用时需要再次创建Stream流

##### **3.2.1 筛选与切片**

Stream<T> `filter(Predicate<? super T> predicate)`

- 接收 Lambda ，从流中排除某些元素

Stream<T> `distinct()`

- **筛选**，通过流所生成元素的hashCode()和 equals()去除重复元素

Stream<T> `limit(long maxSize)`

- **截断流**，使其元素不超过给定数量

Stream<T> `skip(long n)`

- **跳过元素**，返回一个扔掉了前n个元素的流。若流中元素不足n个，则返回一个空流。与 limit(n)互补

![image-20200507115721208](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200507115722.png)

**代码示例:**

```java
public class StreamAPITest1 {
    // 1-筛选与切片
    // 注意执行终止操作后,Stream流就被关闭了,使用时需要再次创建Stream流
    @Test
    public void test1(){
        List<Employee> employees = EmployeeData.getEmployee();
        // 实例化Stream
        Stream<Employee> stream = employees.stream();

        // 1.Stream<T> filter(Predicate<? super T> predicate)
        // 接收 Lambda,从流中排除某些元素。
        // 练习:查询员工表中年龄大于25的员工信息
        // Employee{name='Candy', age=34}
        // Employee{name='Tom', age=56}
        // 注意执行终止操作后,Stream流就被关闭了
        stream.filter(e -> e.getAge() > 25).forEach(System.out::println);
        System.out.println();

        // 2.Stream<T> limit(long maxSize)
        // 截断流，使其元素不超过给定数量。
        // 使用时需要再次创建Stream流: employees.stream()
        employees.stream().limit(3).forEach(System.out::println);
        System.out.println();

        // 3.Stream<T> skip(long n)
        // 跳过元素，返回一个扔掉了前 n 个元素的流。
        // 若流中元素不足n个，则返回一个空流。与limit(n) 互补
        employees.stream().skip(3).forEach(System.out::println);
        System.out.println();

        // 4.Stream<T> distinct()
        // 筛选，通过流所生成元素的 hashCode() 和 equals() 去除重复元素
        employees.add(new Employee("Jim", 23));
        employees.add(new Employee("Jim", 23));
        employees.add(new Employee("Jim", 23));
        employees.add(new Employee("Jim", 23));
        employees.stream().distinct().forEach(System.out::println);
    }
}
```

##### **3.2.2 映射**

<R> Stream<R> `map(Function<? super T, ? extends R> mapper)`

- 接收一个函数作为参数，该函数会被应用到每个元素上，并将其映射成一个新的元素。

mapToDouble(ToDoubleFunction f)

- 接收一个函数作为参数，该函数会被应用到每个元素上，产生一个新的DoubleStream。

mapTolnt(TolntFunction f)

- 接收一个函数作为参数,该函数会被应用到每个元素上，产生一个新的IntStream。

mapToLong(ToLongFunction f)

- 接收一个函数作为参数，该函数会被应用到每个元素上，产生一个新的LongStream。

<R> Stream<R> `flatMap`(Function<? super T, ? extends Stream<? extends R>> mapper)

- 接收一个函数作为参数,将流中的每个值都换成另一个流，然后把所有流连接成一个流。

![image-20200507115738168](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200507115739.png)

**代码示例:**

- map() 和 flatMap() 方法类似于List中的add()和addAll()方法

```java
public class StreamAPITest1 {
    // 2 映射
    @Test
    public void test2() {
        List<String> list = Arrays.asList("aa", "bb", "cc", "dd");
        // 1.<R> Stream<R> map(Function<? super T, ? extends R> mapper)
        // 接收一个函数作为参数,将元素转换成其他形式或提取信息
        // 该函数会被应用到每个元素上, 并将其映射成一个新的元素。
        list.stream().map(str -> str.toUpperCase()).forEach(System.out::println);

        // 练习1: 获取员工姓名长度大于3的员工的姓名。
        List<Employee> employees = EmployeeData.getEmployee();
        Stream<String> nameStream = employees.stream().map(Employee::getName);
        nameStream.filter(name -> name.length() > 3).forEach(System.out::println);
        System.out.println();

        // 2.flatMap(Function f)
        // 接收一个函数作为参数,将流中的每个值都换成另一个流,然后把所有流连接成一个流。
        Stream<Stream<Character>> streamStream2 = list.stream().map(StreamAPITest1::fromStringToStream);
        streamStream2.forEach(s -> {
            s.forEach(System.out::println);
        });
        System.out.println();

        // 练习2：使用map()中间操作实现flatMap()中间操作方法
        Stream<Stream<Character>> streamStream1 = list.stream().map(StreamAPITest1::fromStringToStream);
        streamStream1.forEach(s -> {
            s.forEach(System.out::println);
        });
    }

    // 将字符串中的多个字符构成的集合转换为对应的Stream的实例
    public static Stream<Character> fromStringToStream(String str) {
        ArrayList<Character> list = new ArrayList<>();
        for (Character c : str.toCharArray()) {
            list.add(c);
        }
        return list.stream();
    }

    // map()和flatMap()方法类似于List中的add()和addAll()方法
    @Test
    public void test() {
        ArrayList<Object> list1 = new ArrayList<>();
        list1.add(1);
        list1.add(2);
        list1.add(3);
        list1.add(4);

        ArrayList<Object> list2 = new ArrayList<>();
        list2.add(5);
        list2.add(6);
        list2.add(7);
        list2.add(8);

        list1.add(list2);
        //[1, 2, 3, 4, [5, 6, 7, 8]]
        System.out.println(list1);

        list1.addAll(list2);
        //[1, 2, 3, 4, [5, 6, 7, 8], 5, 6, 7, 8]
        System.out.println(list1);
    }
}
```

**3.2.3 排序**


Stream<T> `sorted()`

- 产生一个新流，其中按自然顺序排序

Stream<T> `sorted(Comparator<? super T> comparator)`

- 产生一个新流，其中按比较器顺序排序

![image-20200507115751929](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200507115752.png)

**代码示例:**

```java
public class StreamAPITest1 {
    //3-排序
    @Test
    public void test3() {
        // 1.Stream<T> sorted()
        // 自然排序
        List<Integer> list = Arrays.asList(12, 34, 26, 47, 53);
        list.stream().sorted().forEach(System.out::println);

        // 抛异常:java.lang.ClassCastException
        // 原因: Employee没有实现Comparable接口
        List<Employee> employee = EmployeeData.getEmployee();
        employee.stream().sorted().forEach(System.out::println);
        System.out.println();

        // 2.Stream<T> sorted(Comparator<? super T> comparator)
        // 定制排序
        List<Employee> employee2 = EmployeeData.getEmployee();
        employee2.stream().sorted((e1, e2) -> {
            int ageCompare = Integer.compare(e1.getAge(), e2.getAge());
            if (ageCompare == 0) {
                return e1.getName().compareTo(e2.getName());
            } else {
                // 按照age大小逆序排列
                return -ageCompare;
            }
        }).forEach(System.out::println);
    }
}
```



#### 3.3  终止操作

终端操作会从流的流水线生成结果。其结果可以是任何不是流的值，例如：List、 Integer，甚至是void。==流进行了终止操作后，不能再次使用==。

**3.3.1 匹配与查找**

###### 匹配

- boolean `allMatch(Predicate p)`：检查是否匹配所有元素
- boolean `anyMatch(Predicate p)`：检查是否至少匹配一个元素
- boolean `noneMatch(Predicate p)`：检查是否没有匹配所有元素
- Optional<T> `findFirst()`：返回第一个元素
- Optional<T> `findFirst()`：返回当前流中的任意元素

![image-20200507120245034](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20201226224625.png)

**代码示例：**

```java
import java.util.List;
import java.util.Optional;

public class StreamAPITest2 {
    // 匹配与查找
    @Test
    public void test1(){
        List<Employee> employees = EmployeeData.getEmployee();
        // 1.boolean allMatch(Predicate<? super T> predicate)
        // 检查是否匹配所有元素。
        // 练习：是否所有的员工的年龄都大于18
        boolean allMatch = employees.stream().allMatch(e -> e.getAge() > 18);
        System.out.println(allMatch); // false

        // 2.boolean anyMatch(Predicate<? super T> predicate)
        // 检查是否至少匹配一个元素。
        // 练习: 是否存在员工的年龄大于 35
        boolean anyMatch = employees.stream().anyMatch(e -> e.getAge() > 35);
        System.out.println(anyMatch);// true

        // 3.boolean noneMatch(Predicate<? super T> predicate)
        // 检查是否没有匹配的元素。
        // 练习：是否不存在员工“A”开头
        boolean noneMatch = employees.stream().noneMatch(e -> e.getName().startsWith("A"));
        System.out.println(noneMatch); // false

        // 4.Optional<T> findFirst()
        // 返回第一个元素
        Optional<Employee> first = employees.stream().findFirst();
        // Optional[Employee{name='Alice', age=23}]
        System.out.println(first);

        // 5.Optional<T> findAny()
        // 返回当前流中的任意元素
        Optional<Employee> any = employees.stream().findAny();
        // Optional[Employee{name='Alice', age=23}]
        System.out.println(any);
    }
}

```



###### 查找

- long `count()`：返回流中元素总数
- Optional<T> `max(Comparator c)`：返回流中最大值
- Optional<T> `min(Comparator c)`：返回流中最小值
- default void `forEach(Consumer c)`：==内部迭代，Stream API使用内部迭代==——它帮你把迭代做了。使用Collection接口需要用户去做迭代，称为**外部迭代**。

![](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20201226224627.png)



**代码示例：**

```java
public class StreamAPITest2 {
    // 匹配与查找
    @Test
    public void test2(){
        List<Employee> employees = EmployeeData.getEmployee();
        // 6.long count()
        // 返回流中元素的总个数
        long count = employees.stream().filter(e -> e.getAge() > 28).count();
        System.out.println(count); // 2

        // 7.min(Comparator c)
        // 返回流中最小值
        // 练习：返回最低年龄的员工
        // 先生成年龄流
        Stream<Integer> ageStream = employees.stream().map(e -> e.getAge());
        Optional<Integer> min = ageStream.min(Integer::compareTo);
        System.out.println(min); // Optional[12]

        // 8.Optional<T> max(Comparator<? super T> comparator)
        // 返回流中最大值
        // 练习：返回最高的年龄
        Optional<Integer> max = employees.stream().map(e -> e.getAge()).max(Integer::compare);
        System.out.println(max); // Optional[56]

        // 9.forEach(Consumer c)
        // 内部迭代
        employees.stream().forEach(System.out::println);
        System.out.println();

        // 10.default void forEach(Consumer<? super T> action)
        // 使用集合的遍历操作
        employees.forEach(System.out::println);
    }
}
```



**3.3.2 归约**

- T `reduce(T identity,  BinaryOperator<T> accumulator)`：可以将流中元素反复结合起来，得到一个值。返回T

- Optional<T> `reduce(BinaryOperator<T> accumulator)`：可以将流中元素反复结合起来，得到一个值。返回Optional<T>

![image-20200507120318120](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200507120320.png)

> 备注：map和reduce的连接通常称为 map-reduce模式，因Google用它来进行网络搜索而出名

**代码示例：**

```java
public class StreamAPITest2 {
    // 2-归约
    @Test
    public void test3(){
        // 1.T reduce(T identity, BinaryOperator<T> accumulator)
        // 可以将流中元素反复结合起来，得到一个值。返回 T
        // 练习1：计算1-10的自然数的和
        List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
        // identity 相当于初始值
        Integer sum = list.stream().reduce(0, Integer::sum);
        System.out.println(sum); // 55

        // 2.Optional<T> reduce(BinaryOperator<T> accumulator)
        // 可以将流中元素反复结合起来，得到一个值。返回 Optional<T>
        // 练习2：计算公司所有员工年龄的总和
        List<Employee> employees = EmployeeData.getEmployee();
       // 写法一
        Optional<Integer> ageSum1 = employees.stream().map(e -> e.getAge()).reduce(Integer::sum);
        System.out.println(ageSum1); // Optional[138]

        // 写法二
        Stream<Integer> integerStream = employees.stream().map(Employee::getAge);
        Optional<Integer> ageSum2 = integerStream.reduce(Integer::sum);
        // Optional<Integer> ageSum2 = integerStream.reduce((d1,d2) -> d1 + d2);
        System.out.println(ageSum2); // Optional[138]
    }
}
```

**3.3.3 收集**

- `collect(Collector c)`：将流转换为其他形式。接收一个 Collector接口的实现，用于给Stream中元素做汇总的方法

![image-20200507120339459](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200507120340.png)

Collector接口中方法的实现决定了如何对流执行收集的操作（如收集到List、Set、Map）



Collectors实用类提供了很多静态方法，可以方便地创建常见收集器实例具体方法与实例如下表：

![image-20200507120628702](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200507120630.png)



![image-20200507120648175](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200507120649.png)

**代码示例：**

```java
public class StreamAPITest2 {
    // 3-收集
    @Test
    public void test4(){
        // 1.<R, A> R collect(Collector<? super T, A, R> collector)
        // 将流转换为其他形式。接收一个 Collector接口的实现,用于给Stream中元素做汇总的方法
        // 练习1：查找年龄大于35的员工，结果返回为一个List或Set
        List<Employee> employees = EmployeeData.getEmployee();
        List<Employee> employeeList = employees.stream().filter(e
                -> e.getAge() > 35).collect(Collectors.toList());
        System.out.println(employeeList);// [Employee{name='Tom', age=56}]
        System.out.println();

        // 转为Set
        Set<Employee> employeeSet = employees.stream().filter(e
                -> e.getAge() < 13).collect(Collectors.toSet());
        employeeSet.forEach(System.out::println); // Employee{name='Jerry', age=12}
    }
}
```



## 六、Optional类的使用

### 1. OPtional类的概述

- ==为了解决java中的空指针问题而生==
- Optional<T> 类 (`java.util.Optional`) 是一个**容器类**，它可以保存类型T的值，代表这个值存在。或者仅仅保存null，表示这个值不存在。原来用 null 表示一个值不存在，现在 Optional 可以更好的表达这个概念。并且可以避免空指针异常。
- Optional类的Javadoc描述如下：这是一个可以为null的容器对象。如果值存在则`isPresent()`方法会返回true，调用get()方法会返回该对象。


### 2. Optional类提供的方法

Optional类提供了很多方法，可以不用再现实的进行空值检验。

#### 2.1 创建Optional类对象的方法

- `Optional.of(T t)` ：创建一个 Optional 实例，==t必须非空==；
- `Optional.empty()` ：创建一个空的 Optional 实例
- `Optional.ofNullable(T t)`：==t可以为null==

```java 
public class OptionalTest {
    @Test
    public void test1(){
        // 1.static <T> Optional<T> of(T value)
        // 封装数据t生成Optional对象。要求t非空，否则报错。
        Girl girl = new Girl();
        Optional<Girl> optionalGirl = Optional.of(girl);

        Girl girlNull = null;
        // 报错：java.lang.NullPointerException
        // Optional<Girl> girlNullOptional1 = Optional.of(girlNull);

        // 2.static<T> Optional<T> empty()
        // 创建的Optional对象内部的value = null
        Optional<Object> empty = Optional.empty();
        System.out.println(empty); // Optional.empty

        // 3.static <T> Optional<T> ofNullable(T value)
        // 封装数据t赋给Optional内部的value。不要求t非空
        Optional<Girl> girlNullOptional2 = Optional.ofNullable(girlNull);
        System.out.println(girlNullOptional2); // Optional.empty
    }
}
```



#### 2.2 判断Optional容器是否包含对象

- boolean `isPresent()`：判断是否包含对象
- void `ifPresent(Consumer<? super T> consumer)`：如果有值，就执行Consumer接口的实现代码，并且该值会作为参数传给它。

```java
public class OptionalTest2 {
    @Test
    public void test1(){
        Optional<Object> empty = Optional.empty();
        // 1.boolean isPresent()
        // 判断是否包含数据
        if (! empty.isPresent()) {
            System.out.println("数据为空"); //
        }
    }
}
```





#### 2.3 获取Optional容器的对象

- T `get()`：如果调用对象包含值，返回该值，否则抛异常
- T `orElse(T other)`：如果有值则将其返回，否则返回指定的other对象
- T orElseGet(Supplier<? extends t> other)：如果有值则将其返回，否则返回由Supplier接口实现提供的对象。
- T orElseThrow(Supplier<? extends X> exceptionSupplier)：如果有值则将其返回，否则抛出由Supplier接口实现提供的异常。



#### 2.4 搭配使用

- `of()` 和 `get()` 方法搭配使用，**明确对象非空**
- `ofNullable()` 和 `orElse()` 搭配使用，**不确定对象非空**

```java
public class OptionalTest2 {
    // 1.of() 和 get() 方法搭配使用,明确对象非空
    @Test
    public void test1(){
        // of(T t)
        // 封装数据t生成Optional对象。要求t非空，否则报错。
        String hello = "Hello";
        Optional<String> helloOptional = Optional.of(hello);

        // T get(): 通常与of()方法搭配使用,用于获取内部的封装的数据value
        // 如果调用对象包含值，返回该值，否则抛异常
        String s = helloOptional.get();
        System.out.println(s); // Hello
    }

    // 2.ofNullable() 和 orElse() 搭配使用，不确定对象非空
    @Test
    public void test2(){
        String str1 ="Beijing";
        str1 = null;
        // ofNullable(T t)
        // 封装数据t赋给Optional内部的value。不要求t非空
        Optional<String> str1Optional = Optional.ofNullable(str1);
        System.out.println(str1Optional); // Optional.empty

        // T orElse(T other)
        // 如果Optional内部的value非空，则返回此value值。如果value为空，则返回other.
        String str2 = str1Optional.orElse("ShangHai");
        System.out.println(str2); // ShangHai
    }
}
```



### 3. 应用举例

> 使用Optional类避免产生空指针异常

```java
public class OptionalTest {
    // 1.原始方法
    public String getGirlName1(Boy boy){
        // 可能发生空指针异常
        return boy.getGirl().getName();
    }
    @Test
    public void test1(){
        Boy boy = new Boy();
        // 报错：java.lang.NullPointerException
        String girlName = getGirlName1(boy);
        System.out.println(girlName);
    }

    // 2.使用原始方法进行非空检验
    public String getGirlName2(Boy boy){
        if (boy !=null) {
            Girl girl = boy.getGirl();
            if (girl != null) {
                return girl.getName();
            }
        }
        return null;
    }
    @Test
    public void test2(){
        Boy boy = new Boy();
        String girlName = getGirlName2(boy);
        // 调用做过非空检验的方法, 没有空指针异常了
        System.out.println(girlName); // null
    }

    // 3. 使用Optional类的getGirlName()进行非空检验
    public String getGirlNameOptional(Boy boy){
        Optional<Boy> boyOptional = Optional.ofNullable(boy);
        // T orElse(T other)
        // 如果Optional内部的value非空,则返回此value值。
        // 如果value为空，则返回other.

        // 此时的boy1一定非空,boy为空是返回“Alice”
        Boy boy1 = boyOptional.orElse(new Boy(new Girl("ALice")));
        Girl girl1 = boy1.getGirl();

        //girl2一定非空,girl1为空时返回“Candy”
        Optional<Girl> girlOptional = Optional.ofNullable(girl1);
        Girl girl2 = girlOptional.orElse(new Girl("Candy"));

        return girl2.getName();
    }
    @Test
    public void test3(){
        Boy boyNull = null;
        String girlName1 = getGirlNameOptional(boyNull);
        System.out.println(girlName1); // ALice

        Boy boy1 = new Boy();
        String girlName2 = getGirlNameOptional(boy1);
        System.out.println(girlName2); // Candy

        // 正常情况
        Boy boy2 = new Boy(new Girl("Bess"));
        String girlName3 = getGirlNameOptional(boy2);
        System.out.println(girlName3); // Bess
    }
}
```



## 七、对反射的支持增强

提高了创建对象、对象赋值和反射创建对象的时间

**代码示例：**

```java
public class testReflection {
    // 循环次数10亿次
    private static final int loopCnt = 1000 * 1000 * 1000;

    public static void main(String[] args) throws InvocationTargetException, NoSuchMethodException, InstantiationException, IllegalAccessException {
        // 输出jdk版本
        System.out.println("java version is" + System.getProperty("java.version"));
        creatNewObject();
        optionObject();
        reflectCreatObject();
    }

    // person对象
    static class Person {
        private Integer age = 20;

        public Integer getAge() {
            return age;
        }

        public void setAge(Integer age) {
            this.age = age;
        }
    }

    // 每次创建新对象
    public static void creatNewObject() {
        long startTime = System.currentTimeMillis();
        for (int i = 0; i < loopCnt; i++) {
            Person person = new Person();
            person.setAge(30);
        }
        long endTime = System.currentTimeMillis();
        System.out.println("循环十亿次创建对象所需的时间：" + (endTime - startTime));
    }

    // 为同一个对象赋值
    public static void optionObject() {
        long startTime = System.currentTimeMillis();
        Person p = new Person();
        for (int i = 0; i < loopCnt; i++) {
            p.setAge(10);
        }
        long endTime = System.currentTimeMillis();
        System.out.println("循环十亿次为同一对象赋值所需的时间：" + (endTime - startTime));
    }

    // 通过反射创建对象
    public static void reflectCreatObject() throws IllegalAccessException, InstantiationException, NoSuchMethodException, InvocationTargetException {
        long startTime = System.currentTimeMillis();
        Class<Person> personClass = Person.class;
        Person person = personClass.newInstance();
        Method setAge = personClass.getMethod("setAge", Integer.class);
        for (int i = 0; i < loopCnt; i++) {
            setAge.invoke(person, 90);
        }
        long endTime = System.currentTimeMillis();
        System.out.println("循环十亿次反射创建对象所需的时间：" + (endTime - startTime));
    }
}
```

**编译级别为JDK8时**

```txt
java version is 1.8.0_201
循环十亿次创建对象所需的时间：9
循环十亿次为同一对象赋值所需的时间：59
循环十亿次反射创建对象所需的时间：2622
```

**编译级别为JDK7时**

```txt
java version is 1.7
循环十亿次创建对象所需的时间：6737
循环十亿次为同一对象赋值所需的时间：3394
循环十亿次反射创建对象所需的时间：293603
```