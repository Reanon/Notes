# Java泛型

> 参考Bilibili尚硅谷课程：[尚硅谷_Java泛型(宋红康主讲)](https://www.bilibili.com/video/BV1Kb411W75N?p=565)

## 一、泛型简介

### 1. 泛型的概念

- 所谓泛型，就是允许在定义类、接口时通过一个标识表示类中某个属性的类型或者是某个方法的返 回值及参数类型。这个类型参数将在使用时（例如，继承或实现这个接口，用这个类型声明变量、 创建对象时确定（即传入实际的类型参数，也称为类型实参）。
- 从JDK 5.0以后，Java引入了“==参数化类型(Parameterized type)==”的概念，允许我们在创建集合时再指定集合元素的类型，正如:`List<String>`，这表明该List只能保存字符串类型的对象。
- JDK 5.0改写了集合框架中的全部接口和类，为这些接口、类增加了泛型支持，从而可以在声明集合变量、创建集合对象时传入类型实参。

### 2. 泛型的引入背景

集合容器类在设计阶段/声明阶段不能确定这个容器到底实际存的是什么类型的对象，所以在JDK1.5之前只能把元素类型设计为Object，JDK1.5之后使用泛型来解决。

因为这个时候除了元素的类型不确定，其他的部分是确定的，例如关于这个元素如何保存，如何管理等是确定的，因此此时把元素的类型设计成一个参数，这个类型参数叫做泛型。Collection<E>，List<E>，ArrayList<E> 这个==<E>就是类型参数==，即泛型。



### 3. 引入泛型的目的

1. 解决元素存储的安全性问题，好比商品、药品标签，不会弄错。
2. 解决获取数据元素时，需要类型强制转换的问题，好比不用每回拿商品、药品都要辨别。

> Java泛型可以保证如果程序在编译时没有发岀警告，运行时就不会产生ClassCastException异常。同时，代码更加简洁、健壮。



## 二、泛型在集合中的应用

### 1  在集合中没有使用泛型的例子

```java
@Test
public void test1(){
    ArrayList list = new ArrayList();
    //需求：存放学生的成绩
    list.add(78);
    list.add(76);
    list.add(89);
    list.add(88);
    //问题一：类型不安全
    //        list.add("Tom");

    for(Object score : list){
        //问题二：强转时，可能出现ClassCastException
        int stuScore = (Integer) score;

        System.out.println(stuScore);

    }

}
```

图示：

![image-20200429224753376](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200429224754.png)



### 2.  在集合中使用泛型

#### 例子一

```java
public class GenericTest {
    @Test
    public void test1() {
        // 在集合中使用泛型，以ArrayList为例
        ArrayList<String> list = new ArrayList<>();
        list.add("AAA");
        list.add("BBB");
        list.add("FFF");
        list.add("EEE");
        // 编译时，就会进行类型检查，保证数据的安全
        list.add("CCC");
        Iterator<String> iterator = list.iterator();
        // 1.遍历方式一
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
        
        // 2.遍历方式二
        for (String s : list) {
            System.out.println(s);
        }
    }
}
```

图示：

![image-20200429224832416](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200429224833.png)



#### **例子二**

```java
public class GenericTest {
    @Test
    public void test2() {
        // 在集合中使用泛型的情况：以HashMap为例
        HashMap<String, Integer> map = new HashMap<>();
        map.put("Tom",26);
        map.put("Jerry",30);
        map.put("Bruce",28);
        map.put("Davie",60);
        // 遍历HashMap
        // 1.取得entry集:Map.Entry<String, Integer>
        Set<Map.Entry<String, Integer>> entries = map.entrySet();
        // 2.获取迭代器
        Iterator<Map.Entry<String, Integer>> iterator = entries.iterator();
        while (iterator.hasNext()) {
            // 3.获取entry
            Map.Entry<String, Integer> entry = iterator.next();
            // 4.分别取得键-值对
            String key = entry.getKey();
            Integer value = entry.getValue();
            // Bruce=28 Tom=26 Davie=60 Jerry=30
            System.out.println(key + "=" + value);
        }
    }
}
```



### 3 集合中使用泛型总结

① 集合接口或集合类在JDK 5.0时都修改为带泛型的结构。

② 在实例化集合类时，可以指明具体的泛型类型

③ 指明完以后，在集合类或接口中凡是定义类或接口时，内部结构（比如：方法、构造器、属性等）使用到类的泛型的位置，都指定为实例化的泛型类型。

  比如：add(E e) --> 实例化后：add(Integer e)

④ 注意点：==泛型的类型必须是类，不能是基本数据类型==。需要用到基本数据类型的位置，拿包装类替换

⑤ 如果实例化时，没有指明泛型的类型。==默认类型为java.lang.Object类型==。IDEA中会提示警告。



## 三、自定义泛型结构

泛型类、泛型接口、泛型方法



### 1. 泛型的声明

- interface List<T> 和 class GenTest<K,V>其中，T，K，V，不代表值，而是表示类型。这里使用任意字母都可以。
- 常用T表示，是Type的缩写。

### 2. 泛型的实例化

一定要在类名后面指定类型参数的值（类型）。如：

List<String> strList =new ArrayList<String>();

Iterator<Customer> iterator = customers.iterator();

- T 只能是类，不能用基本数据类型填充。但可以使用包装类填充
- 把一个集合中的内容限制为一个特定的数据类型，这就是 generics背后的核心思想

```java
//JDK 5.0以前
Comparable c = new Date();
System.out.println(c.comparaTo("red");
                   
//JDK 5.0以后
Comparable <Date> c = new Date();
System.out.println(c.comparaTo("red");                   
```

> 总结：使用泛型的主要优点在于能够在编译时而不是在运行时检测错误



### 3. 注意点

1. 泛型类可能有多个参数，此时应将多个参数一起放在尖括号内。比如<E1,E2,E3>

2. 泛型类的构造器如下： public GenericClass(){}

   - 而下面是错误的： public GenericClass<E>{}

3. 实例化后，操作原来泛型位置的结构必须与指定的泛型类型一致。

4. **泛型不同的引用不能相互赋值**。

   > 尽管在编译时 ArrayList<String>和ArrayList<Integer>是两种类型，但是，在运行时只有一个ArrayList被加载到JVM中。

5. 泛型如果不指定，将被擦除，泛型对应的类型均按照Object处理，但不等价于Object。

   > 建议：泛型要使用一路都用。要不用，一路都不要用。

6. 如果泛型结构是一个接口或抽象类，则不可创建泛型类的对象。

7. JDK 7.0，泛型的简化操作： ArrayList<Fruit>first= new ArrayList<>();（**类型推断**）

8. 泛型的指定中不能使用基本数据类型，可以使用包装类替换。

9. 在类 / 接口上声明的泛型，在本类或本接口中即代表某种类型，可以作为非静态属性的类型、非静态方法的参数类型、非静态方法的返回值类型。但在==静态方法中不能使用类的泛型==。

10. ==异常类不能是泛型的==。

11. 不能使用new E[]。但是可以：`E [] elements= (E[]) new Object[capacity];`

    > 参考：ArrayList源码中声明：Object[] elementData，而非泛型参数类型数组。

12. 父类有泛型，子类可以选择保留泛型也可以选择指定泛型类型：

    - 子类不保留父类的泛型：按需实现
      - 没有类型 --> 擦除
      - 具体类型
    - 子类保留父类的泛型：泛型子类
      - 全部保留
      - 部分保留
    - 结论：子类必须是“富二代”，子类除了指定或保留父类的泛型，还可以增加自己的泛型

代码示例：

```java
class Father<T1, T2> {
}

/**
 * 定义泛型子类Son
 * 情况一：继承泛型父类后不保留父类的泛型
 */
// 1.没有指明类型  擦除
class Son1<A, B> extends Father {//等价于class Son1 extends Father<Object,Odject>{}
}

// 2.指定具体类型
class Son2<A, B> extends Father<Integer, String> {
}

/**
 * 定义泛型子类Son
 * 情况二：继承泛型父类后保留泛型类型
 */
// 1.全部保留
class Son3<T1, T2, A, B> extends Father<T1, T2> {
}

// 2.部分保留
class Son4<T2, A, B> extends Father<Integer,T2>{
}
```



### 4. 自定义泛型结构

#### 4.1 自定义泛型类

如果定义了泛型类，实例化没有指明类的泛型，则认为此泛型类型为**Object类型**。如果大家定义了类是带泛型的，==建议在实例化时要指明类的泛型==。

-  泛型方法：在方法中出现了泛型的结构，泛型参数与类的泛型参数没有任何关系。换句话说，泛型方法所属的类是不是泛型类都没有关系。
-  ==泛型方法，可以声明为静态的==。原因：泛型参数是在调用方法时确定的。并非在实例化类时确定。

注意：

- 静态方法中不能使用类的泛型。

- try-catch中不能是泛型的。

代码示例：

```java
/**
 * 自定义泛型类Order
 */
class Order<T> {
    private String orderName;
    private int orderId;
    //使用T类型定义变量
    private T orderT;

    public Order() {
    }
    //使用T类型定义构造器
    public Order(String orderName, int orderId, T orderT) {
        this.orderName = orderName;
        this.orderId = orderId;
        this.orderT = orderT;
    }

    // 这个不是泛型方法
    public T getOrderT() {
        return orderT;
    }
    //这个不是泛型方法
    public void setOrderT(T orderT) {
        this.orderT = orderT;
    }
    //这个不是泛型方法
    @Override
    public String toString() {
        return "Order{" +
                "orderName='" + orderName + '\'' +
                ", orderId=" + orderId +
                ", orderT=" + orderT +
                '}';
    }
//    //静态方法中不能使用类的泛型,这里的 T 就是类的泛型。
//    public static void show(T orderT){
//        System.out.println(orderT);
//    }

//    //try-catch中不能是泛型的。
//    public void show(){
//        try {
//
//        }catch (T t){
//
//        }
//    }

    // 泛型方法：在方法中出现了泛型的结构，泛型参数与类的泛型参数没有任何关系。
    // 换句话说，泛型方法所属的类是不是泛型类都没有关系。
    // 泛型方法，可以声明为静态的。
    // 原因：泛型参数是在调用方法时确定的。并非在实例化类时确定。
    public static <E> List<E> copyFromArryToList(E[] arr) {
        ArrayList<E> list = new ArrayList<>();
        for (E e : list) {
            list.add(e);
        }
        return list;
    }
}
```

自定义泛型类Order的使用

```java
public class GenericTest2 {
    @Test
    public void test() {
        // 如果定义了泛型类，实例化没有指明类的泛型，则认为此泛型类型为Object类型
        // 要求：如果大家定义了类是带泛型的，建议在实例化时要指明类的泛型。
        Order order = new Order("jerry",23,456);
        System.out.println(order.getOrderT().getClass().getName());// java.lang.Integer

        // 建议：实例化时指明类的泛型
        Order<String> stringOrder = new Order<>("Tom", 16, "male");
        stringOrder.setOrderT("AA:BB");
        System.out.println(stringOrder.getOrderT());// AA:BB
    }
}
```





```java
@Test
public void test1() {
    // 如果定义了泛型类，实例化没有指明类的泛型，则认为此泛型类型为Object类型
    // 要求：如果大家定义了类是带泛型的，建议在实例化时要指明类的泛型。
    Order order = new Order();
    order.setOrderT(123);
    System.out.println(order.getOrderT());

    order.setOrderT("abc");
    System.out.println(order.getOrderT());

    //建议：实例化时指明类的泛型
    Order<String> order1 = new Order<>("Tom", 16, "male");
    order1.setOrderT("AA:BBB");
    System.out.println(order1.getOrderT());
}

@Test
//调用泛型方法
public void test2(){
    Order<String> order = new Order<>();
    Integer [] arr = new Integer[]{1,2,3,4,5,6};
	// 指明泛型方法类型为 Integer
    List<Integer> list = order.copyFromArryToList(arr);
    System.out.println(list);
}
```



#### 4.2 自定义泛型接口

代码示例：

```java
/**
 * 自定义泛型接口
 */
public interface DemoInterface <T> {
    void show();
    int size();
}

//实现泛型接口
public class Demo implements DemoInterface {
    @Override
    public void show() {
        System.out.println("hello");
    }

    @Override
    public int size() {
        return 0;
    }
}

@Test
//测试泛型接口
public void test3(){
    Demo demo = new Demo();
    demo.show();
}
```



#### 4.3 自定义泛型方法

方法，也可以被泛型化，不管此时定义在其中的类是不是泛型类。在泛型方法中可以定义泛型参数，此时，参数的类型就是传入数据的类型。

- 泛型方法声明泛型时也可以指定上限
- 泛型方法，可以声明为静态的。原因：==泛型参数是在调用方法时确定的==。并非在实例化类时确定。

> 泛型方法的格式

- [访问权限] <泛型> 返回类型 方法名 (泛型标识 参数名称)  抛出的异常

代码示例：

```java
// 泛型方法：在方法中出现了泛型的结构，泛型参数与类的泛型参数没有任何关系。
// 换句话说，泛型方法所属的类是不是泛型类都没有关系。
// 泛型方法，可以声明为静态的。
// 原因：泛型参数是在调用方法时确定的。并非在实例化类时确定。
public static <E> List<E> copyFromArryToList(E[] arr) {
    ArrayList<E> list = new ArrayList<>();
    for (E e : list) {
        list.add(e);
    }
    return list;
}
```



#### 4.4 总结

- 泛型实际上就是标签，声明时不知道类型，再使用时指明
- 定义泛型结构，即：泛型类、接口、方法、构造器时贴上泛型的标签<T>
- 用泛型定义类或接口是<T>放到==类名或接口名后==面；定义泛型方法时在==方法名前==加上<T>



### 5.泛型的应用场景

【DAO.java】：定义了操作数据库中的表的通用操作。 ORM思想(数据库中的表和Java中的类对应)

```java
import java.util.List;

public class DAO<T> {
    // 1.添加一条记录
    public void add(T t){
    }
    // 2.删除一条记录
    public boolean remove(int index){
        return false;
    }
    // 3.修改一条记录
    public void update(int index,T t){
    }
    // 4.查询一条记录
    public T getIndex(int index){

        return null;
    }

    // 5.查询多条记录
    public List<T> getForList(int index){

        return null;
    }

    // 泛型方法
    // 举例:获取表中一共有多少条记录？获取最大的员工入职时间？
    public <E> E getValue(){

        return null;
    }
}
```

【CustomerDAO.java】:

```java
public class CustomerDAO extends DAO<Customer>{//只能操作某一个表的DAO
}
```

【StudentDAO.java】:

```java
public class StudentDAO extends DAO<Student> {//只能操作某一个表的DAO
}
```



## 四、泛型在继承上的体现

泛型在继承方面的体现：

- 类A是类B的父类，但G<A> 和G<B>二者不具备子父类关系，二者是并列关系。

> 补充：类A是类B的父类，则A<G> 是 B<G> 的父类

代码示例：

```java
import java.util.ArrayList;
import java.util.List;

public class GenericTest {
    @Test
    public void test1(){
        // Object 是 String的父类
        Object obj = null;
        String str = null;
        obj = str;

        Object[] arr1 = null;
        String[] arr2 = null;
        arr1 = arr2;

        // 不同类型不可转换
        // List<Object> 不是 List<String>的父类
        List<Object> list1 = null;
        List<String> list2 = new ArrayList<String>();
        // 编译不通过
        // 此时的list1和list2的类型不具子父类关系
        // list1 = list2;
        /*
        * 反证法：
        * 假设list1 = list2;
        * list1.add(123);导致混入非String的数据。出错。
        */
        show(list1);
        show1(list2);

    }
    public void show1(List<String> list){
    }

    public void show(List<Object> list){
    }
}
```



```java
public class GenericTest {
    @Test
    public void test2(){
        // 类A是类B的父类，A<G> 是 B<G> 的父类
        AbstractList<String> list1 = null;
        List<String> list2 = null;
        ArrayList<String> list3 = null;

        list1 = list3;
        list2 = list3;

        List<String> list4 = new ArrayList<>();
    }
}
```



## 五、通配符

### 1.通配符的使用

使用类型通配符：`?`，比如：List<?>，Map<?,?>

- `List<?>` 是List<String>、List<Object> 等各种泛型List的父类。

- 读取List<?>的对象list中的元素时，永远是安全的，因为不管list的真实类型是什么，它包含的都是==Object==。

- 不可以向list<?>中写入元素。因为我们不知道？的元素类型，我们不能向其中添加对象。 除了添加null之外，null是所有类型的成员。

说明：

- 将任意元素加入到其中不是类型安全的

  - ```java
  Collection<?> c = new ArrayList<String>()
    c.add(new Object());   //编译时错误
  ```
    
  - 因为我们不知道c的元素类型，我们不能向其中添加对象。add方法有类型参数E作为集合的元素类型。我们传给add的任何参数都必须是一个已知类型的子类。因为我们不知道那是什么类型，所以我们无法传任何东西进去。

- ==唯一的例外的是null，它是所有类型的成员==。

- 我们可以调用get()方法并使用其返回值。返回值是一个未知的类型，但是我们知道，它总是一个Object。

测试通配符代码示例:

```java
public class GenericTest {
    @Test
    public void test(){
        List<Object> list1 = null;
        List<String> list2 = null;
        // List<?>是List<Object>和List<String>的父类
        List<?> list = null;
        list = list1;
        list = list2;

        // 不可以向list<?>中写入元素时
        // list.add("DD");

        // 允许读取数据，读取的数据类型为Object。
        Object o = list.get(0);
        System.out.println(o);
    }

    public void print(List<?> list){
        // 使用通配符作为参数
        Iterator<?> iterator = list.iterator();
        while(iterator.hasNext()){
            Object obj = iterator.next();
            System.out.println(obj);
        }
    }
}
```



### 2.注意点

```java
//注意点1：编译错误：不能用在泛型方法声明上，返回值类型前面<>不能使用?
public static <?> void test(ArrayList<?> list){
    
}

//注意点2：编译错误：不能用在泛型类的声明上
class GenericTypeClass<?>{
    
}

//注意点3：编译错误：不能用在创建对象上，右边属于创建集合对象
ArrayList<> list2 new ArrayList<?>();
```



### 3.有限制的通配符

- `<?>`：允许所有泛型的引用调用
- 通配符指定上限：`extends`
  - 使用时指定的类型必须是**继承某个类**，或者实现某个接口，即 `<=`
- 通配符指定下限：`super`
  - 使用时指定的类型**不能小于操作的类**，即 `>=`

> 举例

1、只允许泛型为**Number及Number子类**的引用调用

- `< ? extends Number>`：(无穷小， Number]



2、只允许泛型为**Number及Number父类**的引用调用

- `< ? super Number>`：[Number，无穷大）



3、只允许泛型为**实现 Comparable接口的实现类**的引用调用

- `< ? extends Comparable>`：Comparable接口实现类



代码示例：Object -> Person -> Student

```java
@Test
public void test4(){

    List<? extends Person> list1 = null;
    List<? super Person> list2 = null;

    List<Student> list3 = new ArrayList<Student>();
    List<Person> list4 = new ArrayList<Person>();
    List<Object> list5 = new ArrayList<Object>();
	
    // List<? extends Person> 是包含Person 和Person 的子类
    list1 = list3;
    list1 = list4;
    
    //        list1 = list5;
    //        list2 = list3;
    // List<? super Person> 是包含Person 和 Person的父类
    list2 = list4;
    list2 = list5;

    // 读取数据：
    list1 = list3;
    Person p = list1.get(0);
    // 编译不通过
    // Student s = list1.get(0);

    list2 = list4;
    Object obj = list2.get(0);
    // 编译不通过
    //        Person obj = list2.get(0);

    // 写入数据：
    // 编译不通过
    //        list1.add(new Student());

    //编译通过
    list2.add(new Person());
    list2.add(new Student());

}
```



## 六、练习

1、定义个泛型类DAO<T>，在其中定义一个Map成员变量，Map 的键为 string类型，值为T类型。分别创建以下方法:

- public void save(String id,T entity)：保存T类型的对象到 Map 成员变量中
- public T get(String id)：从map 中获取id对应的对象
- public void update(String id,T entity)：替换 map 中key为id的内容,改为entity对象
- public list<T> list()：返回map 中存放的所有T对象
- public void delete(String id)：删除指定id 对象

```java
import java.util.ArrayList;
import java.util.Collection;
import java.util.List;
import java.util.Map;

public class DAO<T> {
    Map<String, T> map;

    public DAO(String id, T entity) {
        // map中的修改键值对的方法是put
        this.map.put(id, entity);
    }

    public T get(String id) {
        return map.get(id);
    }

    public void update(String id, T entity) {
        if(map.containsKey(id)){
            map.put(id, entity);
        }
    }
    public List<T> list(){
        // 错误的写法：不可以进行强转换
        // return (List<T>) map.values();

        // 正确写法
        ArrayList<T> list = new ArrayList<>();
        Collection<T> values = map.values();
        for (T value : values) {
            list.add(value);
        }
        return list;
    }

    public void delete(String id) {
        map.remove(id);
    }
}

```

2、定义一个User类:该类包含: private成员变量(int类型) id，age;(String 类型)name。

```java
public class User {
    private int id;
    private int age;
    private String name;

    public User(int id, int age, String name) {
        this.id = id;
        this.age = age;
        this.name = name;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", age=" + age +
                ", name='" + name + '\'' +
                '}';
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;

        User user = (User) o;

        if (id != user.id) return false;
        if (age != user.age) return false;
        return name != null ? name.equals(user.name) : user.name == null;
    }

    @Override
    public int hashCode() {
        int result = id;
        result = 31 * result + age;
        result = 31 * result + (name != null ? name.hashCode() : 0);
        return result;
    }
}
```



3、创建DAO 类的对象，分别调用其 save、get、update、list、 deLete方法来操作 User对象，使用Junit单元测试类进行测试。

```java
import org.junit.Test;

public class DAOTest {
    @Test
    public void test() {
        DAO<User> dao = new DAO<User>();

        dao.save("1001", new User(1001, 31, "Tom"));
        dao.save("1002", new User(1002, 32, "Jerry"));
        dao.save("1003", new User(1003, 33, "Alice"));
        // User{id=1001, age=31, name='Tom'}
        System.out.println(dao.get("1001"));

        dao.update("1004", new User(1004, 25, "JIM"));
        System.out.println(dao);

        System.out.println(dao.list());

        dao.delete("1002");
        System.out.println(dao);
    }
}
```

