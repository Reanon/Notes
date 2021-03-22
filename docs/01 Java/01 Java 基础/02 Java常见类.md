# Java之常用类

> 参考Bilibili尚硅谷课程：[尚硅谷_Java常见类(宋红康主讲)](https://www.bilibili.com/video/BV1Kb411W75N?p=450)

## 一、String类

`java.lang.String类`的使用

### 概述

String：字符串，使用一对`""`引起来表示。

1. ==String声明为final的，不可被继承==

2. String 实现了==Serializable接口==：表示字符串是支持**序列化**的。 实现了==Comparable接口==：表示String**可以比较大小**

3. String内部定义了`final char[] value`用于存储字符串数据

4. String：代表不可变的字符序列。简称：不可变性。**体现：**

   4.1 当对字符串**重新赋值**时，需要重写指定内存区域赋值，不能使用原有的value进行赋值。

   4.2 当对现有的字符串进行**连接操作**时，也需要重新指定内存区域赋值，不能使用原有的value进行赋值。

   4.3  当调用String的replace()**替换方法**修改指定字符或字符串时，也需要重新指定内存区域赋值，不能使用原有的value进行赋值。

5. 通过字面量的方式（区别于new给一个字符串赋值，此时的==字符串值声明在字符串常量池中==)。

6. ==字符串常量池中是不会存储相同内容(==使用String类的equals()比较，返回true)的字符串的)。

### String的特性

String类：代表字符串。Java程序中的所有字符串字面值（如"abc"）都作为此类的实例实现。 String是一个final类，代表不可变的字符序列。 字符串是常量，用双引号引起来表示。它们的值在创建之后不能更改。 String对象的字符内容是存储在一个字符数组`vaue[]`中的。

**String源码构造器:**

```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final char value[];

    /** Cache the hash code for the string */
    private int hash; // Default to 0
```

### 1. String的不可变性

#### 1.1 说明：

1. 当对字符串重新赋值时，==需要重写指定内存区域赋值==，不能使用原有的value进行赋值。
2. 当对现的字符串进行连接操作时，也需要重新指定内存区域赋值，不能使用原有的value进行赋值。
3. 当调用String的`replace()`方法修改指定字符或字符串时，也需要重新指定内存区域赋值，不能使用原有的value进行赋值。

#### 1.2 代码举例：

```java
/**
 * FileName: StringTest
 * Description:
 *
 * @author Reanon
 * @create: 2020/12/18 14:10
 */
import org.junit.Test;
public class StringTest {
    @Test
    public void test() {
        // 通过字面量的定义方式，保存在方法区的字符串常量池中
        String s1 = "abc";
        String s2 = "abc";
        // 比较s1与s2的地址值: true
        System.out.println(s1.equals(s2));
        System.out.println("-----------------------");

        // 1.当对字符串重新赋值时，需要重写指定内存区域赋值
        s1 = "hello";
        // hello
        System.out.println(s1);
        // abc
        System.out.println(s2);
        // 比较s1与s2的地址值: false
        System.out.println(s1.equals(s2));
        System.out.println("-----------------------");
        // 2.当对现的字符串进行连接操作时，也需要重新指定内存区域赋值
        String s3 = "abc";
        s3 += "def";
        // abcdef
        System.out.println(s3);
        System.out.println("-----------------------");
        // 3. 当调用String的replace()方法修改指定字符或字符串时，也需要重新指定内存区域赋值
        String s4 = "abc";
        String s5 = s4.replace('a', 'm');
        // abc
        System.out.println(s4);
        // mbc
        System.out.println(s5);
    }
}

```

#### 1.3 图示解析：

![img](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200417112602.png)



### 2. String实例化方法

#### 2.1 实现方式说明：

- 方式一：通过==字面量定义==的方式
- 方式二：通过==new + 构造器==的方式

```java
/*1.字面量定义*/
String str = "hello";

/*2.new + 构造器*/
// 本质上this.value = new char[0];
String s1 = new String();

// this.value = original.value;
String s2 = new String(String original);

// this.value = Arrays.copyof(value, value.length);
String s3 = new String( char[] a);

String s4 = new String( char[] a, int startIndex, int count);
```

**面试题：**String s = new String("abc");方式创建对象，在内存中创建了几个对象？

**答**：两个，一个是堆空间中new结构；另一个是`char[]`对应的常量池中的数据："abc"



**问题**：String str1=“abc”;与 String str2= new String(“abc”);的区别?

![image-20200417150138496](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200417150139.png)

#### 2.2 代码实例：

- 通过「**字面量定义**」的方式：此时的s1和s2的数据 javaEE 声明在==方法区中的字符串常量池==中。
- 通过「**new + 构造器**」的方式：此时的s3和s4保存的地址值，是数据在==堆空间中开辟空间以后对应的地址值==。

```java
@Test
public void test2() {
    // 1.通过字面量定义的方式
    // 此时的s1和s2的数据javaEE声明在方法区中的字符串常量池中。
    String s1 = "javaEE";
    String s2 = "javaEE";

    // 2.通过new + 构造器的方式
    // 此时的s3和s4保存的地址值，是数据在堆空间中开辟空间以后对应的地址值。
    String s3 = new String("javaEE");
    String s4 = new String("javaEE");
    // true:常量池中只有一个
    System.out.println(s1 == s2);
    // false:堆空间中的地址不一样
    System.out.println(s1 == s3);
    // false
    System.out.println(s1 == s4);
    // false
    System.out.println(s3 == s4);
}
```



> String对象的内存解析

- s3、s4中存储的是堆中的地址
- s1、s2中存储的是字符串常量池中的地址

![image-20200417150527547](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200417150528.png)



```java
public class StringTest {
    @Test
    public void test3(){
        Person p1 = new Person("Tom",14);
        Person p2 = new Person("Tom", 12);
        // true
        System.out.println(p1.name.equals(p2.name));
        // true,'Tom'存放在字符串常量池中
        System.out.println(p1.name == p2.name);
    }
}

class Person {
    String name;
    int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public Person() {
    }
}
```



![image-20201218143941495](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20201218143941.png)

### 3. 字符串拼接方式赋值对比

#### 3.1说明：

1. ==常量与常量的拼接结果在常量池==。且常量池中不会存在相同内容的常量。
2. 只要==其中一个是变量，结果就在堆中==。
3. 如果拼接的结果调用`intern()`方法，==返回值就在常量池中==

#### 3.2 代码示例

```java
public class StringTest {
	@Test
    public void test4() {
        String s0 = "helloworld";
        String s1 = "hello";
        String s2 = "world";
        
        // 1.字面量连接：常量与常量的拼接结果在常量池
        String s3 = "hello" + "world";
        
        // 2. 有变量名参与，就有new，记录的就是堆的地址
        String s4 = s1 + "world";
        // 其中一个是变量，结果就在堆中
        String s5 = s1 + s2;
		
        // final 指定为常量
        final String s6 = "hello";// 常量
        String s7 = s6 + "world"; // 常量 + 常量 = 
        // true
        System.out.println(s7 == s3);

        // true
        System.out.println(s0 == s3);
        // false
        System.out.println(s3 == s4);
        // false
        System.out.println(s3 == s5);
        // false
        System.out.println(s4 == s5);

        // 3.返回值得到的s6使用的常量值中已经存在的“helloworld”
        String s6 = (s1 + s2).intern();
        // true
        System.out.println(s3 == s6);
    }
}

```

内存解析：

![image-20200417151140368](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200417151142.png)



### 4. String使用陷阱

`String s1="a"；`

- 说明：在字符串常量池中创建了一个字面量为"a"的字符串。

`s1=s1+"b"`

- 说明：实际上原来的“a”字符串对象已经丢弃了，现在在堆空间中产生了一个字符串s1+"b"（也就是"ab"）。如果多次执行这些改变串内容的操作，会导致大量副本字符串对象存留在内存中，降低效率。如果这样的操作放到循环中，会极大影响程序的性能。

`String s2="ab"；`

- 说明：直接在字符串常量池中创建一个字面量为"ab"的字符串。

`String s3="a"+"b"；`

- 说明：s3指向字符串常量池中已经创建的"ab"的字符串。

`String s4=s1.intern()；`

- 说明：堆空间的S1对象在调用 `intern()`之后，会将常量池中已经存在的"ab"字符串赋值给s4。





#### Java中String对象不可改变的特性

当String作为形参传递到方法里的时候，实际上传递的是==str引用的拷贝==，改变的是str引用的拷贝，而后方法结束，形参str被销毁， **原str的引用保持不变**，还是指向堆中的str 。

```java
public class StringTest01 {
    // ex.str指的该类的一个属性，在堆里
    String str = new String("good");
    char [] ch = {'t','e','s','t'};
    
    // 对象传引用，变量传地址
    // String传递String引用的拷贝
    public void change(String str, char[] ch) {\
        // 在栈中声明局部变量str，相当于在常量池中新建了一个test ok
        str = "test ok"; // 方法结束，形参str被销毁
        ch[0] = 'b';
    }

    public static void main(String[] args) {
        StringTest01 ex = new StringTest01();
        // String 作为形参时，传递的是String引用的拷贝
        // 变量传递地址
        ex.change(ex.str,ex.ch);
        // good，String类型并没有变化
        System.out.println(ex.str);
        // best
        System.out.println(ex.ch);
    }
}

```



### 5. * String类常用方法

#### 5.1 字符串操作

##### **操作字符：**

1. int `length()`：返回字符串的长度： return value.length
2. char `charAt(int index)`： 返回某索引处的字符 return value[index]
3. boolean `isEmpty()`：判断是否是空字符串：return value.length == 0
4. String toLowerCase()：使用默认语言环境，将 String 中的所字符转换为小写
5. String toUpperCase()：使用默认语言环境，将 String 中的所字符转换为大写
6. String `trim()`：返回**字符串的副本**，忽略前导空白和尾部空白
7. boolean `equals(Object obj)`：比较字符串的**内容**是否相同；
   - `==` 是比较地址是否相同。
8. boolean equalsIgnoreCase(String anotherString)：与equals方法类似，忽略大小写
9. String `concat(String str)`：将指定字符串**连接**到此字符串的结尾。 等价于用“+”
10. int `compareTo(String anotherString)`：比较两个字符串的大小
11. String substring(int beginIndex)：返回一个新的字符串，它是此字符串的从beginIndex开始截取到最后的一个子字符串。
12. String `substring(int beginIndex, int endIndex)` ：返回一个新字符串
    - 它是此字符串==[beginIndex, endIndex)左闭右开==的子字符串。

**代码示例：**

```java
public class StringMethodTest {
    @Test
    public void test() {
        String s1 = "helloword";
        // int length()
        System.out.println(s1.length());//9
        
        // char charAt(int index)，从0开始
        System.out.println(s1.charAt(4));//o
        
        // boolean isEmpty
        System.out.println(s1.isEmpty());//false

        String s2 = "HELLOword";
        // String toLowerCase()
        System.out.println(s2.toLowerCase());//hellowod
        
        // String toUpperCase()
        System.out.println(s2.toUpperCase());//HELLOWORD

        String s3 = " hello word ";
        // String trim(): 返回字符串的副本，忽略前导空白和尾部空白
        System.out.println(s3.trim());//hello word

        String s4 = "helloword";
        // boolean equals(Object obj):比较字符串的「内容」是否相同
        System.out.println(s4.equals(s1));//true
        
        // boolean equalsIgnoreCase(String anotherString)：与equals方法类似，忽略大小写
        System.out.println(s4.equalsIgnoreCase(s2));//true

        String s5 = "hello";
        // int compareTo(String anotherString):比较两个字符串的大小
        System.out.println(s5.compareTo(s4));// -4 相等时返回0，小的时候返回负数
        System.out.println(s4.compareTo(s1));// 0
        
        // String substring(int beginIndex)
        // 返回一个新的字符串，它是此字符串的从beginIndex开始截取到最后的一个子字符串。
        // [5,): helloword
        System.out.println(s4.substring(5)); //word
        
        // String substring(int beginIndex, int endIndex)
        System.out.println(s4.substring(5, 9));//word,取值范围左闭右开
    }
}
```

##### **判断字符：**

1. boolean `endsWith(String suffix)`：测试此字符串是否以指定的**后缀**结束
2. boolean `startsWith(String prefix)`：测试此字符串是否以指定的**前缀**开始
3. boolean `startsWith(String prefix, int toffset)`：测试此字符串从**指定索引**开始的子字符串是否以指定前缀开始

```java
public class StringMethodTest {
	@Test
    public void test2() {
        String s1 = "javaEE";
        // 1.boolean endsWith(String suffix)
        // 测试此字符串是否以指定的后缀结束
        System.out.println(s1.endsWith("EE"));//true

        // 2.boolean startsWith(String prefix)
        // 测试此字符串是否以指定的前缀开始
        System.out.println(s1.startsWith("a"));//false

        // 3.boolean startsWith(String prefix, int toffset)
        // 测试此字符串从指定索引开始的子字符串是否以指定前缀开始
        System.out.println(s1.startsWith("EE", 4));//true
    }
}
```

#### 5.2 查找字符串中的字符

1. boolean `contains(CharSequence s)`：当且仅当此字符串**包含**指定的 char 值序列时，返回 true
2. int `indexOf(String str)`：返回指定子字符串在此字符串中第一次出现处的**索引**
3. int `indexOf(String str, int fromIndex)`：返回指定子字符串在此字符串中第一次出现处的索引，从指定的**索引**开始。
4. int `lastIndexOf(String str)`：返回指定子字符串在此字符串中**最右边出现处的索引**
5. int `lastIndexOf(String str, int fromIndex)`：返回指定子字符串在此字符串中==最后一次出现处的索引==，从指定的索引开始**反向搜索**。

> 注：indexOf和lastIndexOf方法如果未找到都是返回-1

**代码示例：**

```java
public class StringMethodTest {
	@Test
    public void test1() {
        String s2="hello word";
        // boolean contains(CharSequence s)
        // 当且仅当此字符串包含指定的 char 值序列时，返回 true
        System.out.println(s2.contains("o"));//true

        // int indexOf(String str)
        // 返回指定子字符串在此字符串中第一次出现处的索引
        System.out.println(s2.indexOf("h"));//0

        // int indexOf(String str, int fromIndex)
        // 返回指定子字符串在此字符串中第一次出现处的索引，从指定的索引开始
        System.out.println(s2.indexOf("o", 5));//7

        // int lastIndexOf(String str)
        // 返回指定子字符串在此字符串中最右边出现处的索引
        System.out.println(s2.lastIndexOf("o"));//7
        
        // int lastIndexOf(String str, int fromIndex)
        // 返回指定子字符串在此字符串中最后一次出现处的索引，从指定的索引开始反向搜索
        System.out.println(s2.lastIndexOf("l", 2)); //2
    }
}
```



#### 5.3 字符串操作方法

##### **替换**

- String `replace(char oldChar, char newChar)`：返回一个新的字符串，它是通过用 newChar 替换此字符串中出现的所 oldChar 得到的。
- String `replace(CharSequence target, CharSequence replacement)`：使用指定的字面值替换序列替换此字符串所匹配字面值目标序列的子字符串。

**正则表达式**

- String `replaceAll(String regex, String replacement)`：使用给定的 replacement 替换此字符串所匹配给定的正则表达式的子字符串。
- String `replaceFirst(String regex, String replacement)`：使用给定的 replacement 替换此字符串匹配给定的正则表达式的**第一个子字符串**。

```java
public class StringMethodTest {
    @Test
    public void testReplace(){
        String str1 = "北京你好，你好北京";
        // String replace(char oldChar, char newChar)
        // 返回一个新的字符串，它是通过用 newChar 替换此字符串中出现的所 oldChar 得到的。
        String str2 = str1.replace('北', '南');
        // 北京你好，你好北京
        System.out.println(str1);
        // 南京你好，你好南京
        System.out.println(str2);

        String str3 = str1.replace("北京", "上海");
        System.out.println(str3);//上海你好，你好上海

        // String replaceAll(String regex, String replacement)
        // 使用给定的 replacement 替换此字符串所匹配给定的「正则表达式 regex」的子字符串。
        String str = "12hello34world5java7891mysql456";
        
        // 把字符串中的数字替换成「,」,如果结果中开头和结尾有「,」的话去掉
        String string = str.replaceAll("\\d+", ",").replaceAll("^,|,$", "");
        // hello,world,java,mysql
        System.out.println(string);
    }    
}
```



##### 匹配

- boolean `matches(String regex)`：告知此字符串是否匹配给定的**正则表达式**。

```java
public class StringMethodTest {
     @Test
    public void testMatch(){
        String str = "12345";
        // boolean matches(String regex)
        // 告知此字符串是否匹配给定的正则表达式。
        // 判断str字符串中是否全部有数字组成，即有1-n个数字组成
        boolean matches = str.matches("\\d+");
        System.out.println(matches); //true

        String tel = "0571-4534289";
        //判断这是否是一个杭州的固定电话
        boolean result = tel.matches("0571-\\d{7,8}");
        System.out.println(result);//true
    }
}
```



##### 切片

- String[] `split(String regex)`：根据给定**正则表达式**的匹配拆分此字符串。
- String[] `split(String regex, int limit)`：根据匹配给定的**正则表达式**来**拆分**此字符串，最多不超过limit个，如果超过了，剩下的全部都放到最后一个元素中。

**代码示例:**

```java
public class StringMethodTest {
    @Test
    public void testSplit(){
        // String[] split(String regex)
        // 根据给定正则表达式的匹配拆分此字符串。
        String str1 = "hello|world|java";
        // 使用「|」进行切割
        String[] strs = str1.split("\\|");
        for (int i = 0; i < strs.length; i++) {
            System.out.printt(strs[i]); //依次输出hello word java
        }

        String str2 = "hello.world.java";
        // 使用「.」进行切割
        String[] strs2 = str2.split("\\.");
        for (int i = 0; i < strs2.length; i++) {
            System.out.println(strs2[i]);//依次输出hello word java
        }
    }
}
```

### 6. String与其他结构的转换

#### 6.1 String与基本数据类型、包装类之间的转换

##### String  -->  基本数据类型、包装类

调用**包装类**的**静态方法**：`Xxxxx.parseXxx(str)`

- Integer 包装类的 public static int `parselnt(String s)`：可以将由“数字”字符组成的字符串转换为整型。==静态方法，直接用类名==调用：Integer.parseInt(str1)
- 类似地，使用java.lang包中的Byte、Short、Long、Float、Double类调相应的类方法可以将由“数字”字符组成的字符串，转化为相应的基本数据类型。

##### 基本数据类型、包装类 --> String

调用String==重载==的 String  `valueOf(xxx)`

- public static String `valueOf(int n)`可将int型转换为字符串，==静态方法，直接用类名==调用：String.valueOf(xxx)
- 相应的 valueOf(byte b)、valueOf(long l)、valueOf(float f)、valueOf(double d)、valueOf(boolean b)可由参数的相应类型到字符串的转换

**代码示例：**

```java
public class StringMethodTest {
    @Test
    public void testStringToBasic(){
        String str1 = "123";
        // String --> 基本数据类型、包装类
        // public static int parselnt(String s)
        // 可以将由“数字”字符组成的字符串转换为整型。
        int i = Integer.parseInt(str1);
        System.out.println(i);
        System.out.println(i == 123);//true

        int j = 456;
        // 基本数据类型、包装类 --> String
        String s = String.valueOf(j);
        System.out.println(s);
        System.out.println(s.equals("456"));//true
    }
}
```



#### 6.2 与字符数组之间的转换

- ==String --> char[]==：调用String的实例方法 public char[] `toCharArray()`

- ==char[] --> String==：调用String的构造器：public `String(char value[])`

**代码示例：**

```java
public class StringMethodTest {	
	@Test
    public void testCharToString(){
        String s1 = "helloword";
        // public char[] toCharArray()
        // String -> char[]
        char[] chars = s1.toCharArray();
        for (int i = 0; i < chars.length; i++) {
            System.out.println(chars[i]);
        }

        char[] charArray = new char[]{'h', 'e', 'l', 'l', 'o'};
        // public String(char value[])
        // String -> char[]
        String s2 = new String(charArray);
        System.out.println(s2);
    }
}
```

#### 6.3 与字节数组之间的转换

##### **编码String --> byte[]**

字符串 --> 字节 (看得懂 --->看不懂的二进制数据)

- 调用String的 byte[] `getBytes(String charsetName)` throws UnsupportedEncodingException

##### **解码byte[] --> String**

编码的逆过程，字节 --> 字符串 （看不懂的二进制数据 ---> 看得懂）

- 调用String的构造器 `String(byte bytes[])`
- 调用指定字符集的构造器：public `String(byte bytes[], String charsetName)`
- `String(byte bytes[], int offset, int length)`：

说明：解码时，要求**解码使用的字符集必须与编码时使用的字符集一致**，否则会出现乱码。

```java
public class StringMethodTest {	
	@Test
    public void testStringToByte() throws UnsupportedEncodingException {
        String s1 ="你好java世界";
        // 1.byte[] getBytes(String charsetName)
        // String --> byte[]
        byte[] bytesArray = s1.getBytes();//使用默认字符集编码
        System.out.println(Arrays.toString(bytesArray));//[-28, -67, -96, -27, -91, -67, 106, 97, 118, 97, -28, -72, -106, -25, -107, -116]

        byte[] gbks = s1.getBytes("gbk");//使用gbk编码集合
        System.out.println(Arrays.toString(gbks));//[-60, -29, -70, -61, 106, 97, 118, 97, -54, -64, -67, -25]

        System.out.println("--------------------------------");
        // 2.String(byte bytes[]):使用默认字符进行解码
        String str1=new String(bytesArray);
        System.out.println(str1);//你好java世界
        
        // 3.String(byte bytes[], String charsetName)
        String str2 = new String(gbks); //使用默认字符对gbk编码进行解码
        System.out.println(str2);//���java����解码错误，出现中文乱码,原因：编码和解码不一致

        String str3 = new String(gbks,"gbk");// 使用gbk格式进行解码
        System.out.println(str3);// 你好java世界，解码正确，原因：编码和解码一致
    }
}
```



#### 6.4 与StringBuffer、StringBuilder之间的转换

##### String --> StringBuffer、StringBuilder

- 调用StringBuffer：public `StringBuffer(String str)`
- 调用StringBuilder构造器：public `StringBuilder(String str)`

```java
@Test
public void StringToStringBufferTest(){
    String str1 ="helloword";

    StringBuffer stringBuffer = new StringBuffer(str1);
    System.out.println(stringBuffer);//helloword

    StringBuilder stringBuilder = new StringBuilder(str1);
    System.out.println(stringBuilder);//helloword

    stringBuffer.append("isStringBuffer");
    System.out.println(stringBuffer);//hellowordandgood

    stringBuilder.append("isStringBuider");
    System.out.println(stringBuilder);
}
```

##### StringBuffer、StringBuilder --> String

1. 调用==String构造器==。
2. StringBuffer、StringBuilder的 `toString()`。

```java
@Test
public void StringBuiderOrStringBufferToStringTest() {
    StringBuffer sb1 = new StringBuffer("hello StringBuffer");
    System.out.println(sb1);

    StringBuilder sb2 = new StringBuilder("hello StringBuider");
    System.out.println(sb2);

    System.out.println("----------------------");
	
    // 1.StringBuffer --> String
    String str1 = new String(sb1);
    System.out.println(str1);
	
    // StringBuilder --> String
    String str2 = new String(sb2);
    System.out.println(str2);

    System.out.println("----------------------");
    // 2.StringBuffer、StringBuilder --> String
    System.out.println(sb1.toString());
    System.out.println(sb2.toString());
}
```



### 7. JVM中字符串常量池存放位置说明

jdk 1.6 (jdk 6.0 ,java 6.0)：字符串常量池存储在方法区（永久区）

jdk 1.7：字符串常量池存储在堆空间

jdk 1.8：字符串常量池存储在==方法区（元空间）==



### 8. 常见算法题目的考查

1）模拟一个trim方法，去除字符串两端的空格。

```java
public String myTrim(String str) {
    if (str != null) {
        int start = 0;//记录从前往后首次索引位置不是空格的位置索引
        int end = str.length() - 1;//记录从后往前首次索引位置不是空格的位置索引
        while (start < end && str.charAt(start) == ' ') {
            start++;
        }
        while (start < end && str.charAt(end) == ' ') {
            end--;
        }
        if (str.charAt(start) == ' ') {
            return "";
        }
        // [start,end+1)
        return str.substring(start, end + 1);
    }

    return null;
}
```

2）将一个字符串进行反转。将字符串中指定部分进行反转。比如“abcdefg”反转为”abfedcg”

方式一：转换为char []

```java
public static String reverse(String str, int startIndex, int endIndex) {
    /**
         * 将一个字符串进行反转。将字符串中指定部分进行反转。比如“abcdefg”反转为”abfedcg”
         * @param str
         * @param startIndex
         * @param endIndex
         * @return java.lang.String
         */
    if (str == null){
        return null;
    }
    // 1.转换成char型数组
    char[] chars = str.toCharArray();
    // 2.进行反转操作
    for (int i = startIndex, j = endIndex; i < j; i++, j--) {
        char temp = chars[i];
        chars[i] = chars[j];
        chars[j] = temp;
    }
    // 3.返回值
    return new String(chars);
}
```

方式二

- 分析：整个字符串分为三部分不反转的、反转的、不反转的
- 先将前面不反转的部分取出来，将反转的部分取出后进行拼接

使用StringBuffer或==StringBuilder==替换String优化

```java
public static String reverse2(String str, int startIndex, int endIndex) {
    // StringBuilder调用构造器
    StringBuilder stringBuilder = new StringBuilder(str.length());
    // 区间 [0,startIndex)
    stringBuilder.append(str.substring(0, startIndex));
    
    // 区间 [startIndex, endIndex]
    for (int i = endIndex; i >= startIndex; i--) {
        stringBuilder.append(str.charAt(i));
    }
    // 区间 [endIndex + 1, -)
    stringBuilder.append(str.substring(endIndex + 1));

    // StringBuilder -> String:调用toString()方法
    return new String(stringBuilder.toString());
}
```

3）获取一个字符串在另一个字符串中出现的次数。 比如：获取“ ab”在 “abkkcadkabkebfkabkskab” 中出现的次数

方式：

- 调用String 的 int `indexOf(String str, int index)` 方法
- 当indexOf查找失败时，返回-1

```java
public class StringExercise {
    public int count(String mainStr, String subStr) {
        /**
         * 获取一个字符串在另一个字符串中出现的次数。
         * 比如：获取“ ab”在 “abkkcadkabkebfkabkskab” 中出现的次数
         * @param mainStr 主串
         * @param subStr 子串
         * @return int 出现次数
         */
        // 1.判断主串和部分串的大小
        if (mainStr.length() < subStr.length()) {
            return 0;
        }
        int index = 0;
        int count = 0;
        
        // 2.在主串中取出子串下标，并将新的下标赋值给主串，统计量加1
        while ((index = mainStr.indexOf(subStr, index)) != -1) {
            // 避免重复，需要将索引向前移动子串的长度
            index += subStr.length();
            count++;
        }
        return count;
    }

    @Test
    public void test() {
        System.out.println(count("abkkcadkabkebfkabkskab", "ab"));
    }
}
```

4）获取两个字符串中最大相同子串。比如： str1 = "abcwerthelloyuiodef“;str2 = "cvhellobnm" 提示：将短的那个串进行长度依次递减的子串与较长的串比较。

- 这个方法并不完善

```java
import java.util.Arrays;

public class StringExercise3 {
    public static String[] getMaxSameSubString(String str1, String str2) {
        /**
         * 获取两个字符串中最大相同子串。
         * 比如： str1 = "abcwerthelloyuiodef“;str2 = "cvhellobnm"
         * 提示：将短的那个串进行长度依次递减的子串与较长的串比较。
         * @param str1
         * @param str2
         * @return java.lang.String
         */

        if ((str1 == null) || (str2 == null)) {
            return null;
        }
        StringBuilder stringBuilder = new StringBuilder();
        // 1.先比较出两个子串的大小
        String maxStr = (str1.length() > str2.length()) ? str1 : str2;
        String minStr = (str1.length() > str2.length()) ? str2 : str1;
        int length = minStr.length();
        // 2.用小的去依次匹配大的
        // 遍历长度从0 - length 的子串
        for (int i = 0; i < length; i++) {
            for (int j = 0, k = length - i; k <= length; j++, k++) {
                String subString = minStr.substring(j, k);
                // 3.取出匹配到的子串
                if (maxStr.contains(subString)) {
                    stringBuilder.append(subString + ",");
                }
            }
            // 使得找到最大子串之后，退出
            if (stringBuilder.length() != 0){
                break;
            }

        }

        String[] res = stringBuilder.toString()
                .replaceAll(",$", "")
                .split("\\,");
        return res;
    }

    public static void main(String[] args) {
        String [] res = getMaxSameSubString("abcwerthelloyuiodef", "cvyuihellobnabm");
        System.out.println(Arrays.toString(res));
    }
}

```

5）对字符串中字符进行自然顺序排序。 提示：

1. 字符串变成字符数组。
2. 对数组排序，择，冒泡，`Arrays.sort()`;
3. 将排序后的数组变成字符串。

```java
@Test
public void charTest() {
    String str1 = "hello java";
    char[] charArray = str1.toCharArray();
	// Arrays.sort()只能对数组进行排序
    Arrays.sort(charArray);

    String str2 = new String(charArray);
    System.out.println(str2);
}
```



## StringBuffer和StringBuilder

### String、StringBuffer和StringBuilder区别

String、StringBuffer、stringBuilder三者的异同?

- String：==不可变的字符序列==，底层使用 final char[] value存储
- StringBuffer：可变的字符序列，线程安全，**效率低**，底层使用char[] value存储
- `StringBuilder`：**可变的字符序列**，jdk1.5新增，==线程不安全，效率高， 底层使用char[] value存储==

**源码分析**

> String

```java
/*********************构造方法******************/
// char[] value = new char[0];
String str = new String();

// char [] value = new char[] {'a','b','c'}
String str1 = new String("abc");
```

> StringBuffer

扩容问题：如果要添加的数据底层数组盛不下了，那就需要扩容底层的数组。默认情况下，扩容为**原来容量的 2倍＋2**，同时将原有数组中的元素复制到新的数组中。

指导意义：

- 开发中建议大家构造时指定容量： `StringBuffer(int capacity)`或 `StringBuilder(int capacity)`

```java
/*********************构造方法******************/
// char[] value = new char[16];
StringBuffer sb1 = new StringBuffer("");
// value[0] = 'a';
sb.append('a');
// value[1] = 'b';
sb.append('b');
System.out.println(sb1.length());//2

// char[] value = new char["abc".length + 16]
StringBuffer sb2 = new StringBuffer("abc");
System.out.println(sb2.length());//3,返回count的值
```



>StringBuilder

```java
/*********************构造方法******************/
// char[] value = new char[0];
String str = new String();

// char [] value = new char[] {'a','b','c'}
String str1 = new String("abc");
```



### StringBuffer类

#### 1.概述

`java.lang.String.Buffer`代表可变的字符序列，**JDK1.0**中声明，可以对字符串内容进行增删，此时不会产生新的对象。 很多方法与 String相同作为参数传递时，方法内部可以改变值。

```java
abstract class AbstractStringBuilder implements Appendable, CharSequence {
    /**
     * The value is used for character storage.
     */
    char[] value; //value没有final声明，value可以不断扩容

    /**
     * The count is the number of characters used.
     */
    int count;//count记录有效字符个数
```

- StringBuffer类不同于 String，其==对象必须使用构造器生成==。
- 有三个构造器:：
  -  StringBuffer()：初始容量为**16**的字符串缓冲区
  - StringBuffer(int size)：构造指定容量的字符串缓冲区
  - StringBuffer(String str)：将内容初始化为指定字符串内容

```java
public class StringBufferTest {
	@Test
    public void test1(){
        StringBuffer sb1 = new StringBuffer("abc");
        // void setCharAt(int index, char ch)
        sb1.setCharAt(0,'m');
        System.out.println(sb1);//mbc
    }
}
```

![image-20200417221841421](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200417221842.png)



#### 2.常用方法：

1. StringBuffer `append(xxx)`：提供了很多的append()方法，用于进行字符串拼接
2. StringBuffer `delete(int start, int end)`：删除指定位置的内容
3. StringBuffer `replace(int start, int end, String str)`：把==[start,end)==位置替换为str
4. StringBuffer `insert(int offset, xxx)`：在指定位置插入xxx
5. StringBuffer `reverse()` ：把当前字符序列逆转

当 append和insert时，如果原来vaue数组长度不够，可扩容。 如上这些方法支持==方法链==操作。 方法链的原理：

```java
@Override
public StringBuilder append(String str) {
    super.append(str);
    return this; // 再次返回自身
}
```

- public int `indexOf(String str)`：返回子串的下标
- public String `substring(int start, int end)`：返回一个从start开始到end索引结束的==左闭右开==区间的子字符串
- public int `length()`：获取字符串的长度
- public char `charAt(int n )`：返回指定位置的字符
- public void `setCharAt(int n ,char ch)`：设置指定位置的字符

**总结：**

增：append(xxx) ；

删：delete(int start,int end) ；

改：setCharAt(int n ,char ch) / replace(int start, int end, String str) ；

查：charAt(int n ) ；

插：insert(int offset, xxx) ；

长度：length();

遍历：for() + charAt() / toString()；

**代码示例：**

```java
public class StringBufferTest {
	@Test
    public void teststringBufferMethod() {
        StringBuffer s1 = new StringBuffer("abc");
        System.out.println(s1);
        // StringBuffer append(xxx)
        // 1.增提供了很多的append()方法，用于进行字符串拼接
        System.out.println(s1.append("1"));//abc1

        // StringBuffer delete(int start, int end)
        // 2.删：删除指定位置的内容，[start,end)
        System.out.println(s1.delete(0, 1));//bc1

        // 3.改：StringBuffer replace(int start, int end, String str)
        // 把[start,end)位置替换为str,左闭右开
        System.out.println(s1.replace(0, 1, "hello"));//helloc1

        // StringBuffer insert(int offset, xxx)
        // 在指定位置插入xxx
        System.out.println(s1.insert(3, "v"));//helvloc1
        // StringBuffer reverse()
        // 把当前字符序列逆转
        System.out.println(s1.reverse());//1colvleh
    }
}
```



### StringBuilder类

StringBuilder和 StringBuffer非常类似，均代表可变的字符序列，而且提供相关功能的方法也一样，只是StringBuilder类没有加线程锁，执行效率更高。

#### 1. String、StringBuffer、StringBuilder三者的对比

- String：不可变的字符序列；底层使用char[]存储；占用内存（会不断的创建和回收对象）
- StringBuffer：可变的字符序列；线程安全的，效率低；线程安全；底层使用char[]存储；
- `StringBuilder`：可变的字符序列；jdk5.0新增的，线程不安全的，效率高；线程不安全；底层使用char[]存储

> 注意：作为参数传递的话，**方法内部String不会改变其值**， StringBuffer和 StringBuilder会改变其值。

#### 2. StringBuffer与StringBuilder的内存解析

**以StringBuffer为例：**

```java
//char[] value = new char[0];
String str = new String();
String str1 = new String("abc");//char[] value = new char[]{'a','b','c'};

//char[] value = new char[16];底层创建了一个长度是16的数组。
StringBuffer sb1 = new StringBuffer();
System.out.println(sb1.length());//
sb1.append('a');//value[0] = 'a';
sb1.append('b');//value[1] = 'b';

//char[] value = new char["abc".length() + 16];
StringBuffer sb2 = new StringBuffer("abc");
```

**StringBuffer构造器源码：**

```java
public StringBuffer(String str) {
    super(str.length() + 16);
    append(str);
}
```

问题1: 

- System.out.println(sb2.length()); //3

问题2：扩容问题：

- 如果要添加的数据底层数组盛不下了，那就需要扩容底层的数组。 默认情况下，扩容为原来容量的==2倍 + 2==，同时将原有数组中的元素复制到新的数组中。

指导意义：开发中建议大家使用：StringBuffer(int capacity) 或 `StringBuilder(int capacity)`

#### 3. 对比String、StringBuffer、StringBuilder三者的执行效率

从高到低排列：StringBuilder  >  StringBuffer > String

- public static long `currentTimeMillis()`
  - 调用方法：`System.currentTimeMillis()`；
  - 返回值是long型
  - 以==毫秒==为单位

```java
public class StringBufferTest {
	@Test
    public void test2() {
        //初始设置
        long startTime = 0L;
        long endTime = 0L;
        String text = "";
        StringBuffer buffer = new StringBuffer("");
        StringBuilder builder = new StringBuilder("");
        // 开始对比
        // public static long currentTimeMillis()
        // 用来返回当前时间与1970年1月1日0时0分0秒之间以毫秒为单位的时间差
        startTime = System.currentTimeMillis();
        for (int i = 0; i < 100_0000; i++) {
            // StringBuffer
            buffer.append(String.valueOf(i));
        }
        endTime = System.currentTimeMillis();
        System.out.println("StringBuffer的执行时间：" + (endTime - startTime));

        startTime = System.currentTimeMillis();
        for (int i = 0; i < 100_0000; i++) {
            // StringBuilder
            builder.append(String.valueOf(i));
        }
        endTime = System.currentTimeMillis();
        System.out.println("StringBuilder的执行时间：" + (endTime - startTime));

        startTime = System.currentTimeMillis();
        for (int i = 0; i < 1_0000; i++) {// 少了两个数量级
            // String类型
            text = text + i;
        }
        endTime = System.currentTimeMillis();
        System.out.println("String的执行时间：" + (endTime - startTime));
    }
}
```

>StringBuffer的执行时间：171
>StringBuilder的执行时间：80
>String的执行时间：586

## 三、JDK 8.0以前的日期时间API

[尚硅谷_Java零基础教程-java入门必备：时间与日期](https://www.bilibili.com/video/BV1Kb411W75N?p=469)

### 1. java.lang.System类

System类提供的 public static long `currentTimeMillis()`：用来返回当前时间与**1970年1月1日0时0分0秒**之间以==毫秒==为单位的时间差。

- (时间戳) 此方法适于计算时间差。

> 计算世界时间的主要标准有：
>
> UTC（Coordinated Universal Time） GMT（Greenwich Mean Time） CST（Central Standard Time）

**代码示例：**

```java
public class DateTimeTest {
    @Test
    public void test1() {
        // public static long currentTimeMillis()
        // 获取系统当前时间：System类中的currentTimeMillis()
        // 返回当前时间与1970年1月1日0时0分0秒之间以毫秒为单位的时间差。
        long time = System.currentTimeMillis();
        // 称为时间戳
        System.out.println(time);
    }
}
```



### 2. java.util.Date类

表示特定的瞬间，精确到**毫秒**

#### 2.1 构造器

- `Date()`：使用无参的构造器创建对象可以获取本地当前时间

- `Date(long date)`：创建指定毫秒数的Date对象

#### 2.2 常用方法

long `getTime()`：返回自 1970年1月1日00:00:00GMT以来此Date对象表示的毫秒数

String `toString()`：把此Date对象转换为以下形式的 String：

- "EEE MMM dd HH:mm:ss zzz yyyy"
- 周几   月    日 时    分 秒 时区 年

其中：EEE是一周中的某一天（Sun，Mon，Tue，Wed，Thu，Fri，Sat），zzz是时间标准。 ==其它很多方法都过时了==

```java
public class DateTimeTest {
	@Test
    public void test2(){
        // 构造器一：Date()创建一个对应当前时间的Date对象
        Date date1 = new Date();
        
        // Sat Dec 19 14:46:48 CST 2020
        System.out.println(date1.toString());
        
        // 1608360545305
        System.out.println(date1.getTime());
        
        // 构造器二：创建指定毫秒数的Date对象
        Date date2 = new Date(15872745176L);
        // Sat Jul 04 01:05:45 CST 1970
        System.out.println(date2.toString());
    }
}
```



### 3.  java.sql.Date类

java.sql.Date==对应着数据库中的日期类型的变量==；注意与java.util.Date的区别：

- |---java.util.Date类

- |---java.sql.Date类

1. 两个构造器的使用
   - 构造器一：Date()：创建一个对应当前时间的Date对象
   - 构造器二：`Date(long date)`创建指定毫秒数的Date对象

2. 两个方法的使用
   - toString()：显示当前的年、月、日、时、分、秒
   - getTime()：获取当前Date对象对应的毫秒数。（时间戳）

将==java.util.Date对象转换为java.sql.Date对象==：`java.sql.Date(date.getTime())`;

- ```java
  java.sql.Date dateSql = new java.sql.Date(date.getTime());
  ```

  

```java
public class DateTimeTest {
	@Test
    public void test2(){
        // 创建java.sql.Date对象
        java.sql.Date date3 = new java.sql.Date(1332543141644L);
        // 2012-03-24
        System.out.println(date3);
        // 1332543141644
        System.out.println(date3.getTime());

        // 将java.util.Date对象转换为java.sql.Date对象
        Date date4 = new Date();
        
        // 报错：java.util.Date cannot be cast to java.sql.Date
		// java.sql.Date date5 = (java.sql.Date) date4;

        // 通过Date.getTime方式转换
        java.sql.Date date6 = new java.sql.Date(date4.getTime());
        
        // 2020-12-19
        System.out.println(date6);
    }
}
```



### 4. java.text.SimpleDateFormat类

==Date类的API不易于国际化，大部分被废弃了==， java.text.SimpleDateFormat类是一个不与语言环境有关的方式来格式化和解析日期的具体类。它允许进行格式化、解析。

构造实例

- SimpleDateFormat()：默认的模式和语言环境创建对象

- public `SimpleDateFormat(String pattern)`：该构造方法可以用参数 pattern指定的格式创建一个对象，该对象调用。

#### 1. SimpleDateFormat对日期Date类的格式化和解析

**格式化：**日期 → 文本

- public String `format(Datedate)`：方法格式化时间对象date

**解析**：文本 → 日期

-  public Date `parse(String source)`：从给定字符串的开始解析文本，以生成个日期



#### 2. SimpleDateFormat的实例化:new + 构造器

照指定的方式格式化和解析：调用带参的构造器

```java
SimpleDateFormat sdf1 = new SimpleDateFormat("yyyyy.MMMMM.dd GGG hh:mm aaa");
```

| Date and Time Pattern          | Result                               |
| ------------------------------ | ------------------------------------ |
| "yyyy.MM.dd G 'at' HH:mm:ss z" | 2001.07.04 AD at 12:08:56 PDT        |
| "EEE, MMM d, ''yy"             | Wed, Jul 4, '01                      |
| "h:mm a"                       | 12:08 PM                             |
| "hh 'o''clock' a, zzzz"        | 12 o'clock PM, Pacific Daylight Time |
| "K:mm a, z"                    | 0:08 PM, PDT                         |
| "yyyyy.MMMMM.dd GGG hh:mm aaa" | 02001.July.04 AD 12:08 PM            |
| "EEE, d MMM yyyy HH:mm:ss Z"   | Wed, 4 Jul 2001 12:08:56 -0700       |
| "yyMMddHHmmssZ"                | 010704120856-0700                    |
| "yyyy-MM-dd'T'HH:mm:ss.SSSZ"   | 2001-07-04T12:08:56.235-0700         |
| "yyyy-MM-dd'T'HH:mm:ss.SSSXXX" | 2001-07-04T12:08:56.235-07:00        |
| "YYYY-'W'ww-u"                 | 2001-W27-3                           |

**代码示例：**

```java
public class DateTimeTest {
    @Test
    public void test3() throws ParseException {
        Date date = new Date();
        // Sat Dec 19 16:47:40 CST 2020
        System.out.println(date);

        // 1.默认方式实例化SimpleDateFormate对象
        SimpleDateFormat sdf1 = new SimpleDateFormat();

        // 格式化：日期 --> 字符串
        String format = sdf1.format(date);
        // 20-12-19 下午4:47
        System.out.println(format);

        // 解析：格式化的逆过程，字符串 ---> 日期
        String str = "20-12-19 下午4:53";
        // 解析:要求字符串必须是符合SimpleDateFormat识别的格式(通过构造器参数体现);
        // 否则，抛异常
        Date date2 = sdf1.parse(str);
        // Sat Dec 19 16:53:00 CST 2020
        System.out.println(date2);

        // 2.实例化SimpleDateFormat对象，并设置显示格式
        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss");
        // 格式化:日期 -> 文本
        String format2 = simpleDateFormat.format(date);
        // 2020-12-19 05:07:04
        System.out.println(format2.toString());
        // 解析：文本 —> 日期
        Date date3= simpleDateFormat.parse("2020-12-19 05:07:04");
        // Sat Dec 19 05:07:04 CST 2020
        System.out.println(date3);
    }
}
```

![image-20200419141139894](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200419141141.png)

**小练习：**

将字符串"2020-09-08"转换为java.sql.Date格式的时间

```java
public class DateExercise {
    @Test
    public void test() throws ParseException {
        String birth = "2020-12-20";
        // 新建SimpleDateFormat对象并设置时间格式
        // 月份是大写的MM
        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd");
        // 1.解析：将字符串格式的时间格式化为Date类
        // 需要处理异常 ParseException
        Date birthday = simpleDateFormat.parse(birth);
        // 2.通过Date的getTime方法将Date对象转化为时间戳放到java.sql.date类的构造方法中
        java.sql.Date date = new java.sql.Date(birthday.getTime());
        System.out.println(date);
    }
}
```



### 5. Calendar类：日历类、抽象类

==Calendar是一个抽象基类==，主用用于完成日期字段之间相互操作的功能。

- 获取 Calenda实例的方法，使用 `Calendar.getInstance()`方法 调用它的子类 `GregorianCalendarl`的构造器。
- 一个 Calendar的实例是系统时间的抽象表示，通过 get(int field)方法来取得想要的时间信息。 比如YEAR、MONTH、DAY_OF_WEEK、HOUR_OF_DAY、MINUTE、SECOND public void set(int field,int value) public void add(int field,int amount) public final Date get Time() public final void set Time(Date date)
- 注意 获取月份时：一月是0，二月是1，以此类推，12月是11 获取星期时：周日是1，周二是2...周六是7

#### 4.1 实例化

方式一：创建其子类GregorianCalendar的对象

方式二：调用其静态方法getInstance()

```java
 @Test
    public void testCalender(){
        // 实例化
        // 1.方式一：创建其子类GregorianCalendar的对象
        GregorianCalendar c = (GregorianCalendar) Calendar.getInstance();

        // 2.方式二：调用其静态方法getInstance()
        Calendar calendar = Calendar.getInstance();
        // java.util.GregorianCalendar
        System.out.println(calendar.getClass().getName());
    }
```



#### 4.2 常用方法

- int get(int field)：获取日期
- void set(int field, int value)：设置日期
- void add(int field, int amount)：添加、修改日期
- final Date getTime()：日历类-->Date
- void setTime(Date date)：Date-->日历类

**代码示例：**

```java
@Test
// 常用方法
public void testCalender() {
    // 1. int get(int field)：获取日期
    // 获取本月第几天
    int days = calendar.get(Calendar.DAY_OF_MONTH);
    System.out.println(days);// 19
    // 获取本年第几天
    System.out.println(calendar.get(Calendar.DAY_OF_YEAR));// 354

    // 2. void set(int field, int value)：设置日期
    // 设置本月第几天
    calendar.set(Calendar.DAY_OF_MONTH,22);
    days = calendar.get(Calendar.DAY_OF_MONTH);
    System.out.println(days);

    // 3. void add(int field, int amount)：添加、修改日期
    calendar.add(Calendar.DAY_OF_MONTH,-3);
    days = calendar.get(Calendar.DAY_OF_MONTH);
    System.out.println(days);// 19

    // 4. Date getTime():日历类 --> Date
    Date date = calendar.getTime();
    // 修改之后的时间
    System.out.println(date);// Sat Dec 19 17:29:14 CST 2020

    //5. void setTime(Date date):Date --> 日历类
    Date date1 = new Date();
    calendar.setTime(date1);
    days = calendar.get(Calendar.DAY_OF_MONTH);
    System.out.println(days); // 19
}
```



## 四、JDK 8.0中新的日期时间类

### 1. 日期时间API的迭代：

第一代：jdk 1.0 Date类

第二代：jdk 1.1 Calendar类，一定程度上替换Date类

第三代：jdk 1.8 提出了新的一套API



### 2. 前两代的问题：

可变性：像日期和时间这样的类应该是不可变的。

偏移性：Date中的年份是从1900开始的，而月份都从0开始。

格式化：格式化只对Date用，Calendar则不行。 此外，它们也不是线程安全的；不能处理闰秒等。

Java 8.0中新引入的==java.time API==

Java 8.0吸收了Joda-Time的精华，以一个新的开始为Java创建优秀的APl。新的java.time中包含了所有关于本地日期(LocalDate)、本地时间(Localtime)、本地日期时间(LocalDate time)、时区(ZonedDate time)和持续时间(Duration)的类。历史悠久的Date类新增了tolnstant()方法用于把Date转换成新的表示形式。这些新增的本地化时间日期API大大简化了日期时间和本地化的管理。



### 3. Java 8.0中新的日期时间API涉及的包:

- `java.time` ：包含值对象的基础包
- java.time.chrono：提供对不同的日历系统的访问
- `java.time.format`：格式化和解析时间和日期
- java.time.temporal：包括底层框架和扩展特性
- java.time.zone：包含时区支持的类

说明：大多数开发者只会用到==基础包和format包==，也可能会用到temporal包。因此，尽管有68个新的公开类型，大多数开发者，大概将只会用到其中的三分之一。



### 4. 本地日期、本地时间、本地日期时间的使用：

LocalDate / LocalTime / LocalDateTime

#### 4.1 说明：

① 分别表示使用 ISO-8601日历系统的日期、时间、日期和时间。它们提供了简单的本地日期或时间，并不包含当前的时间信息，也不包含与时区相关的信息。

② `LocalDateTime`==相较于LocalDate、LocalTime，使用频率要高==

③ 类似于Calendar

#### 4.2 常用方法：

![img](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200421112139.png)

**代码示例：**

```java
import org.junit.Test;

import java.time.LocalDate;
import java.time.LocalDateTime;
import java.time.LocalTime;

public class JDK8DateTimeTest {
    @Test
    public void test1() {
        //1.now():获取当前的日期、时间、日期时间
        LocalDate localDate = LocalDate.now();
        LocalTime localTime = LocalTime.now();
        LocalDateTime localDateTime = LocalDateTime.now();

        System.out.println(localDate);// 2020-12-19
        System.out.println(localTime);// 17:43:31.855
        System.out.println(localDateTime);//2020-12-19T17:43:31.855

        //2.of():设置指定的年、月、日、时、分、秒。没有偏移量
        LocalDateTime localDateTime1 = LocalDateTime.of(2020, 10, 6, 12, 13, 12);
        System.out.println(localDateTime1);//2020-10-06T12:13:12

        //3.getXxx()：获取相关的属性
        System.out.println(localDateTime.getDayOfMonth());// 19
        System.out.println(localDateTime.getDayOfWeek());// SATURDAY
        System.out.println(localDateTime.getMonth());// DECEMBER
        System.out.println(localDateTime.getMonthValue());// 12
        System.out.println(localDateTime.getMinute());// 47

        //4.withXxx():设置相关的属性
        LocalDate localDate1 = localDate.withDayOfMonth(22);
        // 体现LocalDate类的不可变性
        System.out.println(localDate);// 2020-12-19
        System.out.println(localDate1);// 2020-12-22

        // 体现LocalDateTime类的不可变性
        LocalDateTime localDateTime2 = localDateTime.withHour(4);
        System.out.println(localDateTime); // 2020-12-19T17:51:03.596
        System.out.println(localDateTime2);// 2020-12-19T04:51:03.596

        // 5.plusXXX / minusXXX：加减操作
        LocalDateTime localDateTime3 = localDateTime.plusMonths(3);
        System.out.println(localDateTime); // 2020-12-19T17:52:51.014
        System.out.println(localDateTime3);// 2021-03-19T17:52:51.014

        LocalDateTime localDateTime4 = localDateTime.minusDays(6);
        System.out.println(localDateTime); // 2020-12-19T17:52:51.014
        System.out.println(localDateTime4);// 2020-12-13T17:52:51.014
    }
}

```

### 5.时间点：Instant

[尚硅谷_Java零基础教程](https://www.bilibili.com/video/BV1Kb411W75N?p=486)

#### 5.1 说明：

① 时间线上的一个瞬时点。 概念上讲，它只是简单的表示自1970年1月1日0时0分0秒（UTC开始的秒数。）

② 类似于 java.util.Date类

#### 5.2 常用方法：

-  Instant `now()`：获取本初子午线对应的标准时间
- OffsetDateTime `atOffset(ZoneOffset offset)` 
- long `toEpochMilli()`：获取自1970年1月1日0时0分0秒（UTC）开始的毫秒数
- static Instant `ofEpochMilli(long epochMilli)`：通过给定的毫秒数，获取Instant实例

![image-20200421112259386](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200421112300.png)

**代码示例：**

```java
public class InstantTest {
    @Test
    public void test() {
        // 1.now():获取本初子午线对应的标准时间
        Instant instant = Instant.now();
        System.out.println(instant);// 2020-12-20T19:11:26.666+08:00

        // 2.添加东八区的时间偏移量
        OffsetDateTime offsetDateTime = instant.atOffset(ZoneOffset.ofHours(8));
        System.out.println(offsetDateTime); // 2020-12-20T19:11:26.666+08:00

        // 3.toEpochMilli():获取自1970年1月1日0时0分0秒（UTC）开始的毫秒数
        // Date类的getTime()
        long milli = instant.toEpochMilli();

        // 4.ofEpochMilli():通过给定的毫秒数，获取Instant实例 
        // Date(long millis)
        Instant instant1 = Instant.ofEpochMilli(1587467105795L);
        System.out.println(instant1); //2020-04-21T11:05:05.795Z
    }
}
```



### 6.日期时间格式化类：DateTimeFormatter

#### 6.1 说明：

1. 格式化或解析日期、时间
2. 类似于SimpleDateFormat

#### 6.2 常用方法

java.time.format.`DateTimeFormatter`类：该类提供了三种格式化方法:

1. 预定义的标准格式。如：ISO_LOCAL_DATE_TIM、ISO_LOCAL_DATE、ISO_LOCAL_TIME

2. 本地化相关的格式。如:

   1. static DateTimeFormatter `ofLocalizedDateTime(FormatStyle dateTimeStyle)`：适用于LocalDateTime；

      - ```java
        FormatStyle.LONG     // 2020年12月20日 下午07时55分49秒
        FormatStyle.MEDIUM   // 2020-12-20 19:55:20
        FormatStyle.SHORT    // 20-12-20 下午7:56
        ```

   2. static DateTimeFormatter `ofLocalizedDate(FormatStyle dateStyle)`：适用于LocalDate；

      - ```java
        FormatStyle.FULL     // 2020年12月20日 星期日
        FormatStyle.LONG     // 2020年12月20日
        FormatStyle.MEDIUM   // 2020-12-20
        FormatStyle.SHORT    // 20-12-20
        ```

3. ==自定义的格式==。如: `ofPattern("yyyy-MM-dd hh:mm:ss")`：静态方法，返回一个指定字符串格式的DateTimeFormatter

**格式化**

- String `format(TemporalAccessor temporal)`：格式化一个日期、时间；==返回字符串==

**解析**

- TemporalAccessor `parse(CharSequence text)`：将指定格式的字符序列解析为一个日期、时间

```java
// 重点： 方式三：自定义的格式。如：ofPattern(“yyyy-MM-dd hh:mm:ss”)
DateTimeFormatter formatter3 = DateTimeFormatter.ofPattern("yyyy-MM-dd hh:mm:ss");

// 1.格式化:日期 -> 字符串
String str4 = formatter3.format(LocalDateTime.now());
System.out.println(str4);//2020-12-20 07:59:03

// 2.解析：字符串 -->日期
TemporalAccessor parse1 = formatter3.parse("2020-12-20 07:59:03");
System.out.println(parse);// {},ISO resolved to 2020-12-20T19:41:31.757
```

**代码示例：**

```java
import java.time.LocalDate;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import java.time.format.FormatStyle;
import java.time.temporal.TemporalAccessor;
public class DateTimeFormatterTest {
    @Test
    public void test() {
        // 方式一：预定义的标准格式。
        // 如：ISO_LOCAL_DATE_TIME;ISO_LOCAL_DATE;ISO_LOCAL_TIME
        DateTimeFormatter formatter = DateTimeFormatter.ISO_LOCAL_DATE_TIME;
        
        // 1.格式化:日期 -> 字符串
        LocalDateTime localDateTime = LocalDateTime.now();
        // String format(TemporalAccessor temporal)
        String str1 = formatter.format(localDateTime);
        System.out.println(str1);// 2020-12-20T19:41:31.757
        
        // 2.解析：字符串 -->日期
        // TemporalAccessor parse(CharSequence text)
        TemporalAccessor parse = formatter.parse("2020-12-20T19:41:31.757");
        System.out.println(parse);//{},ISO resolved to 2020-12-20T19:41:31.757

        // 方式二.1 本地化相关的格式。如：ofLocalizedDateTime()
        // FormatStyle.LONG / FormatStyle.MEDIUM / FormatStyle.SHORT:适用于LocalDateTime
        DateTimeFormatter formatter1 = DateTimeFormatter.ofLocalizedDateTime(FormatStyle.SHORT);
        // 1.格式化:日期 -> 字符串
        String str2 = formatter1.format(localDateTime);
        System.out.println(str2);//2020-12-20 19:47:52
        
        
        // 方式二.2 本地化相关的格式。如：ofLocalizedDate()
        // FormatStyle.FULL / FormatStyle.LONG / FormatStyle.MEDIUM / FormatStyle.SHORT : 适用于LocalDate
        DateTimeFormatter formatter2 = DateTimeFormatter.ofLocalizedDate(FormatStyle.SHORT);
        // 1.格式化:日期 -> 字符串
        String str3 = formatter2.format(LocalDate.now());
        System.out.println(str3);// 2020-12-20

        // 重点： 方式三：自定义的格式。如：ofPattern(“yyyy-MM-dd hh:mm:ss”)
        DateTimeFormatter formatter3 = DateTimeFormatter.ofPattern("yyyy-MM-dd hh:mm:ss");
        // 1.格式化:日期 -> 字符串
        String str4 = formatter3.format(LocalDateTime.now());
        System.out.println(str4);//2020-12-20 07:59:03
        // 2.解析：字符串 -->日期
        TemporalAccessor parse1 = formatter3.parse("2020-12-20 07:59:03");
        System.out.println(parse);// {},ISO resolved to 2020-12-20T19:41:31.757
    }
}
```



### 7.其它API的使用：

![image-20200421113038985](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200421113040.png)



#### 7.1 带时区的日期时间：

ZonedDateTime / ZoneId

**代码示例：**

```java
// ZoneId:类中包含了所的时区信息
@Test
public void test1(){
    //getAvailableZoneIds():获取所的ZoneId
    Set<String> zoneIds = ZoneId.getAvailableZoneIds();
    for(String s : zoneIds){
        System.out.println(s);
    }
    System.out.println();

    //获取“Asia/Tokyo”时区对应的时间
    LocalDateTime localDateTime = LocalDateTime.now(ZoneId.of("Asia/Tokyo"));
    System.out.println(localDateTime);


}
//ZonedDateTime:带时区的日期时间
@Test
public void test2(){
    //now():获取本时区的ZonedDateTime对象
    ZonedDateTime zonedDateTime = ZonedDateTime.now();
    System.out.println(zonedDateTime);
    //now(ZoneId id):获取指定时区的ZonedDateTime对象
    ZonedDateTime zonedDateTime1 = ZonedDateTime.now(ZoneId.of("Asia/Tokyo"));
    System.out.println(zonedDateTime1);
}
```



#### 7.2 时间间隔：

Duration--用于计算两个“时间”间隔，以秒和纳秒为基准

![image-20200421113320241](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200421113321.png)

**代码示例：**

```java
@Test
public void test3(){
    LocalTime localTime = LocalTime.now();
    LocalTime localTime1 = LocalTime.of(15, 23, 32);
    //between():静态方法，返回Duration对象，表示两个时间的间隔
    Duration duration = Duration.between(localTime1, localTime);
    System.out.println(duration);

    System.out.println(duration.getSeconds());
    System.out.println(duration.getNano());

    LocalDateTime localDateTime = LocalDateTime.of(2016, 6, 12, 15, 23, 32);
    LocalDateTime localDateTime1 = LocalDateTime.of(2017, 6, 12, 15, 23, 32);

    Duration duration1 = Duration.between(localDateTime1, localDateTime);
    System.out.println(duration1.toDays());

}
```



#### 7.3 日期间隔：

Period --用于计算两个“日期”间隔，以年、月、日衡量

![image-20200421113353331](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200421113354.png)

**代码示例：**

```java
@Test
public void test4(){
    LocalDate localDate = LocalDate.now();
    LocalDate localDate1 = LocalDate.of(2028, 3, 18);

    Period period = Period.between(localDate, localDate1);
    System.out.println(period);

    System.out.println(period.getYears());
    System.out.println(period.getMonths());
    System.out.println(period.getDays());

    Period period1 = period.withYears(2);
    System.out.println(period1);

}
```



#### 7.4 日期时间校正器：TemporalAdjuster

**代码示例：**

```java
@Test
public void test5(){
    //获取当前日期的下一个周日是哪天？
    TemporalAdjuster temporalAdjuster = TemporalAdjusters.next(DayOfWeek.SUNDAY);

    LocalDateTime localDateTime = LocalDateTime.now().with(temporalAdjuster);
    System.out.println(localDateTime);

    //获取下一个工作日是哪天？
    LocalDate localDate = LocalDate.now().with(new TemporalAdjuster(){

        @Override
        public Temporal adjustInto(Temporal temporal) {
            LocalDate date = (LocalDate)temporal;
            if(date.getDayOfWeek().equals(DayOfWeek.FRIDAY)){
                return date.plusDays(3);
            }else if(date.getDayOfWeek().equals(DayOfWeek.SATURDAY)){
                return date.plusDays(2);
            }else{
                return date.plusDays(1);
            }

        }

    });

    System.out.println("下一个工作日是：" + localDate);
}
```



#### 7.5 新的日期API与原来API的转化问题：

![image-20200421113620480](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200421113621.png)



## 五、Java比较器

### 1. Java比较器的使用背景

- Java中的对象，正常情况下，只能进行比较：`== 或 !=` 。不能使用 > 或 < 的

Java实现对象排序的方式有两种

- 自然排序：==java.lang.Comparable== 
- 定制排序： ==java.util.Comparator==

### 2. 自然排序：使用Comparable接口

接口：`public interface Comparable<T>` 

#### 2.1 说明

1. 像String、包装类等实现了Comparable接口，重写了int `compareTo(T o)`方法，给出了比较两个对象大小的方式。
2. 像String、包装类重写int `compareTo(String anotherString)`方法以后，进行了从小到大的排列
3. 对于==自定义类==来说，如果需要排序，我们可以让自定义类：
   1. 实现Comparable接口
   2. 重写int compareTo(obj)方法；同时在int compareTo(obj)方法中指明如何排序。

> **重写compareTo(obj)的规则**

一般默认是从小到大进行排序。

1. 如果当前对象this**大于**形参对象obj，则返回**正整数**。
2. 如果当前对象this**小于**形参对象obj，则返回**负整数**。
3.  如果当前对象this**等于**形参对象obj，则返回**零**。

> Comparable的典型实现(默认都是从小到大排列的)

- String：按照字符串中字符的**Uincode值**进行比较
- Character：按照字符的**Unicode值**来进行比较 
- 数值类型对应的包装类以及BigInteger、BigDecimal：按照它们对应的**数值大小**进行比较
- Boolean：true对应的包装类实例大于false对应的包装类实例
- Date、Time等：后面的**日期时间**比前面的日期时间大

```java
import org.junit.Test;
import java.util.Arrays;
/**
 * FileName: CompareTest
 * Description: Comparable 接口的使用举例
 *
 * @author Reanon
 * @create: 2020/12/20 20:17
 */

public class CompareTest {
    @Test
    public void test1() {
        String[] arr = new String[]{"AA", "CC", "MM", "DD", "JJ", "BB"};
        Arrays.sort(arr);// 从小到大排序
        System.out.println(Arrays.toString(arr));
    }
}
```



#### 2.2 自定义类代码举例：

```java
/**
 * FileName: Goods
 * Description:指明商品比较大小的方式:照价格从低到高排序,再照产品名称从高到低排序
 *
 * @author Reanon
 * @create: 2020/12/20 20:28
 */

public class Goods implements Comparable {

    private String name;
    private double price;

    public Goods(String name, double price) {
        this.name = name;
        this.price = price;
    }

    @Override
    public int compareTo(Object o) {
        /**
         * 指明商品比较大小的方式：照价格从低到高排序
         * @param o  比较对象
         * @return int
         */
        if (o instanceof Goods) {
            Goods goods = (Goods) o;
            // 方式一：价格从低到高排序
            if (this.price > goods.price) {
                return 1;
            } else if (this.price < goods.price) {
                return -1;
            } else {
                // 再照产品名称从高到低排序
                return -this.name.compareTo(goods.name);
            }
            //方式二：return Double.compare(this.price, goods.price);
        }
        // 处理异常
        throw new RuntimeException("传入的数据类型不一致！");
    }

    @Override
    public String toString() {
        /**
         * 重写toString 方法
         * @param 
         * @return   java.lang.String
         */
        return "Good{" +
                "name = '" + name + '\'' + "," +
                "price = " + price  +
                '}';
    }
    public String getName() {
        return name;
    }

    public double getPrice() {
        return price;
    }
}
```

测试一下：

```java
public class CompareTest {
    @Test
    public void test2() {
        Goods[] goods = new Goods[4];
        goods[0] = new Goods("lx", 34);
        goods[1] = new Goods("dr", 43);
        goods[2] = new Goods("xm", 12);
        goods[3] = new Goods("hw", 43);
		// Arrays.sort():必须传入数组
        Arrays.sort(goods);
        System.out.println(Arrays.toString(goods));
    }
}
// [Good{name = 'xm',price = 12.0}, Good{name = 'lx',price = 34.0}, Good{name = 'hw',price = 43.0}, Good{name = 'dr',price = 43.0}]
```



### 3. 定制排序：使用Comparator接口

#### 3.1 说明:

1. 接口：`public interface Comparator<T>` 
2. 背景

当元素的类型没实现 java.lang.Comparable 接口而又不方便修改代码，或者实现了java.lang.Comparable接口的排序规则不适合当前的操作，那么可以考虑使用  ==java.util.Comparator==的对象来排序。

3. 重写int `compare(T o1, T o2)`方法，比较 o1 和 o2 的大小：
   - 如果方法返回正整数，则表示 o1 大于 o2 。
   - 如果返回 0，表示相等；
   - 返回负整数，表示 o1 小于 o2。

```java
public class CompareTest {
    @Test
    public void test3() {
        String[] arr = new String[]{"AA", "CC", "MM", "DD", "JJ", "BB"};
        Arrays.sort(arr, new Comparator() {
            @Override
            public int compare(Object o1, Object o2) {
                // 确保数据类型一致
                if (o1 instanceof String && o2 instanceof String) {
                    String s1 = (String) o1;
                    String s2 = (String) o2;
                    // 使得字符串从小到大排列
                    return -s1.compareTo(s2);
                }
                throw new RuntimeException("输入数据有误");
            }
        });
        // [MM, JJ, DD, CC, BB, AA]
        System.out.println(Arrays.toString(arr));
    }
}
```



#### 3.2 代码举例：

```java
public class CompareTest {
    @Test
    public void test4() {
        Goods[] goods = new Goods[4];
        goods[0] = new Goods("lx", 34);
        goods[1] = new Goods("dr", 43);
        goods[2] = new Goods("xm", 12);
        goods[3] = new Goods("hw", 43);

        Arrays.sort(goods, new Comparator() {
            // 先照产品名称从低到高，再照价格从高到低排序
            @Override
            public int compare(Object o1, Object o2) {
                if (o1 instanceof Goods && o2 instanceof Goods) {
                    Goods g1 = (Goods) o1;
                    Goods g2 = (Goods) o2;
                    if (g1.getName().equals(g2.getName())) {
                        // 价格从高到低排序(默认从低到高）
                        return -Double.compare(g1.getPrice(), g2.getPrice());
                    }else {
                        // 产品名称从低到高(默认从低到高）
                        return g1.getName().compareTo(g2.getName());
                    }
                }
                return 0;
            }
        });
        System.out.println(Arrays.toString(goods));
    }
}
// [Good{name = 'dr',price = 43.0}, Good{name = 'hw',price = 43.0}, Good{name = 'lx',price = 34.0}, Good{name = 'xm',price = 12.0}]
```



### 4. 两种排序方式对比

- Comparable接口的方式是==一定的==，一般在类中实现Comparable接口，来确保类的对象在任何位置都可以比较大小。
- Comparator接口属于==临时性==的比较。

## 六、其他常用类

### 1.System类

- `java.lang.System类`代表系统，系统级的很多属性和控制方法都放置在该类的内部。
- 由于该类的构造器是private的，所以无法创建该类的对象，也就是无法实例化该类。其内部的成员变量和成员方法都是static的，所以也可以很方便的进行调用。

**成员变量**

- System类内部包含in、out和err三个成员变量，分别代表标准输入流(键盘输入)，标准输出流(显示器)和标准错误输出流(显示器)。
  - final static InputStream in = null;
  - final static PrintStream out = null;
  - final static PrintStream err = null;

**成员方法：**

- static `native` long `currentTimeMillis()`：

  该方法的作用是返回当前的计算机时间，时间的表达格式为当前计算机时间和GMT时间（格林威治时间）1970年1月1号0时0分0秒所差的毫秒数。

- static void `exit(int status)`

  该方法的作用是退出程序。其中 status的值为==0代表正常退出==，非零代表异常退出。使用该方法可以在图形界面编程中实现程序的退出功能等

- static void `gc()`

  该方法的作用是==请求系统进行垃圾回收==。至于系统是否立刻回收，则取决于系统中垃圾回收算法的实现以及系统执行时的情况。

- static String `getProperty(String key)`

  该方法的作用是获得系统中属性名为key的属性对应的值。系统中常见的属性名以及属性的作用如下表所示：

  ![image-20200421114933166](https://gitee.com/realbruce/blogImage/raw/master/imgs/20200421114934.png)

**代码示例：**

```java
public class SystemClassTest {
    @Test
    public void test() {
        String javaVersion = System.getProperty("java.version");
        // java的version:1.8.0_251
        System.out.println("java的version:" + javaVersion);

        String javaHome = System.getProperty("java.home");
        // java的home:D:\DevelopmentTools\Java\jdk1.8.0_251\jre
        System.out.println("java的home:" + javaHome);

        String osName = System.getProperty("os.name");
        // os的name:Windows 10
        System.out.println("os的name:" + osName);

        String osVersion = System.getProperty("os.version");
        // os的version:10.0
        System.out.println("os的version:" + osVersion);

        String userName = System.getProperty("user.name");
        // user的name:XXX
        System.out.println("user的name:" + userName);

        String userHome = System.getProperty("user.home");
        // user的home:C:\Users\XXX
        System.out.println("user的home:" + userHome);

        String userDir = System.getProperty("user.dir");
        // user的dir:D:\Documents\IdeaProjects\JavaBasic\chapter10 Time
        System.out.println("user的dir:" + userDir);
    }
}
```



### 2.Math类

`java.lang.Math`提供了一系列**静态方法**用于科学计算。其==方法的参数和返回值类型一般为double型==。

成员变量

- static final double `E` = 2.7182818284590452354;
- static final double `PI` = 3.14159265358979323846;

成员方法

- abs：绝对值
- acos,asin,atan,cos,sin,tan：三角函数
  - double `sin(double a)`
  - ...
- double sqrt(double a)：平方根
- double pow(double a, double b)：a的b次幂
- double log(double a)：自然对数
- double exp(double a)：e为底指数
- max(double a, double b)
- min(double a, double b)
- double `random()`：返回 ==[0.0, 1.0)== 的随机数
- long round(double a)：double型数据a转换为long型（四舍五入）
- double `ceil(double a)`：返回==大于x==的最小整数，ceil(10.5) == 11  ceil(-10.5) ==-10；**天花板**
- double `floor(double a)`：返回==小于或等于x==的最大整数， floor(10.5) == 10  floor(-10.5) == -11；**地板**
- double toDegrees(double angrad)：弧度—>角度
- double toRadians(double angdeg)：角度—>弧度

```java
public class MathClassTest {
    @Test
    public void test1() {
        double a = Math.ceil(-10.5); // -10.0
        double b = Math.ceil(10.5);  // 11.0

        double c = Math.floor(-10.5);// -11.0
        double d = Math.floor(10.5); // 10.

        double e = Math.round(10.6); // 11.0
        double f = Math.round(10.4); // 10.0
        double r = Math.random();    // 0.6767069605010585
        
        System.out.println(Math.E);  // 2.718281828459045
        System.out.println(Math.PI); // 3.141592653589793
    }
}
```



### 3.BigInteger类、BigDecimal类

Integer类作为int的包装类，能存储的最大整型值为$2^{31}-1$；Long类也是有限的，最大为$2^{63}-1$。如果要表示再大的整数，不管是基本数据类型还是他们的包装类都无能为力，更不用说进行运算了。

#### 3.1 BigInteger

- `java.math.BigInteger`可以表示==不可变的任意精度的整数==。
- BigInteger提供所有Java的基本整数操作符的对应物，并提供 java.lang.Math 的所有相关方法。另外，BigInteger还提供以下运算：模算术、GCD计算、质数测试、素数生成、位操作以及一些其他操作。

> 构造器

-  `BigInteger(String val, int radix)`：根据字符串构建 BigInteger对象，radix指明进制
- BigInteger(String val)：默认 this(val, 10);

> 常用方法

- public BigInteger `abs`()：返回此 BigInteger 的绝对值的BigInteger。
- BigInteger `add`(Biglnteger val)：返回其值为(this + val)的 BigInteger
- BigInteger `subtract`(BigInteger val)：返回其值为(this - val)的 BigInteger
- BigInteger `multiply`(Biglnteger val)：返回其值为(this * val)的 BigInteger
- BigInteger `divide`(BigInteger val)：返回其值为(this / val)的 BigInteger。整数相除只保留整数部分。
- BigInteger `remainder`(BigInteger val)：返回其值为(this % val)的 BigInteger，**取余，遵循尽可能让商大的原则**
- BigInteger[] `divideAndRemainder`(BigInteger val)：返回包含(this / val)后跟(this % val)的两个Biglnteger 的数组。
- BigInteger `pow`(int exponent)：返回其值为($this^{exponent}$)的 BigInteger。

**代码举例：**

```java
@Test
public void test2() {
    BigInteger bi = new BigInteger("1243324112234324324325235245346567657653");
    BigDecimal bd = new BigDecimal("12435.351");
    BigDecimal bd2 = new BigDecimal("11");
    System.out.println(bi);
    // System.out.println(bd.divide(bd2));
    System.out.println(bd.divide(bd2, BigDecimal.ROUND_HALF_UP));
    System.out.println(bd.divide(bd2, 25, BigDecimal.ROUND_HALF_UP));

}
```



#### 3.2 BigDecimal

一般的Float类和Double类可以用来做科学计算或工程计算，但在商业计算中要求数字精度比较高，用到java.math.BigDecimal类。BigDecimal类支持==不可变的、任意精度的有符号十进制定点数==。

> **构造器**

- public BigDecimal(double val)

- public BigDecimal(String val)

> **常用方法**

- public BigDecimal `add`(BigDecimal augend)
- public BigDecimal `subtract`(BigDecimal subtrahend)
- public BigDecimal `multiply`(BigDecimal multiplicand)
- public BigDecimal `divide`(BigDecimal divisor， int scale， int rounding Mode)

**代码举例：**

```java
public class BigDecimalClassTest {  
	@Test
    public void test2() {
        BigInteger bi = new BigInteger("1243324112234324324325235245346567657653");
        BigDecimal bd = new BigDecimal("12435.351");
        BigDecimal bd2 = new BigDecimal("11");

        System.out.println(bi);
        // 1130.486
        System.out.println(bd.divide(bd2, BigDecimal.ROUND_HALF_UP));
        // 保留15位小数: 1130.486454545454545
        System.out.println(bd.divide(bd2, 15, BigDecimal.ROUND_HALF_UP));
    }
}
```

#### 练习

> 练习1：将字符串”2017-08-16"转换为对应的java.sql.Date类的对象。(使用JDK8之前或JDK8中的API皆可)

答：

JDK8之前：SimpleDateFormat sdf = new `SimpleDateFormat`("yyyy-MM-dd");

```java
public class DateExercise {
    @Test
    public void test() throws ParseException {
        String birth = "2020-12-20";
        // 新建SimpleDateFormat对象并设置时间格式
        // 月份是大写的MM
        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd");
        // 1.解析：将字符串格式的时间格式化为Date类
        // 需要处理异常 ParseException
        Date birthday = simpleDateFormat.parse(birth);
        // 2.通过Date的getTime方法将Date对象转化为时间戳放到java.sql.date类的构造方法中
        java.sql.Date date = new java.sql.Date(birthday.getTime());
        System.out.println(date);
    }
}
```

JDK8：DateTimeFormatter dtf= `DateTimeFormatter.ofPattern`("yyyy-MM-dd");

```

```

>2．解释何为编码?解码?何为日期时间的格式化?解析?

- 编码：  字符串  →   字节
- 解码： 字节      →   字符串



- 格式化：日期    →  字符串
- 解析：字符串    →  日期

> 5.JDK8之前和JDK8中日期、时间相关的类分别有哪些 

|            JDK8之前            |                JDK8                 |
| :----------------------------: | :---------------------------------: |
| java.util.Date和 java.sql.Date |               Instant               |
|        SimpleDateFormat        |          DateTimeFormatter          |
|            Calendar            | LocalDate、LocalTime、LocalDateTime |

## 七、Arrays工具类

### 1.理解

`java.util.Arrays`类即为操作数组的工具类，包含了用来操作数组（比如排序和搜索）的各种方法。

### 2.方法

- static boolean `equals(long[] a, long[] a2)`：判断两个数组是否相等。
- String `toString(int[] a)`：输出数组信息。
- static void `fill(int[] a, int val)`：将指定值val 填充到数组之中。
- static void `sort(Object[] a)`：对数组进行排序。
- static int `binarySearch(Object[] a, Object key)`：对排序后的数组进行二分法检索指定的值。
- static int[] `copyOf(int[] original, int newLength)`：拷贝数组
  - 其内部调用了 `System.arraycopy()` 方法，从下标 0 开始，如果超过原数组长度，则会用 null 进行填充

> 都是**静态方法**，通过Arrays.xxx()来调用。

### 2.使用

```java
public class ArraysTest {
    @Test
    public void test(){
        int arr1[] = new int[] { 21, 43, 542, 432, 4, 2, 5, 1 };
        int arr2[] = new int[] { 32, 43, 4, 1, 4, 76, 54, 68, 4 };
        // 1.Arrays.equals(arr1, arr2):判断数组是否相等
        System.out.println(Arrays.equals(arr1, arr2));

        // 2.Arrays.toString(arr1):输出数组信息
        System.out.println(arr1.toString());      // [I@621be5d1
        System.out.println(Arrays.toString(arr1));// [21, 43, 542, 432, 4, 2, 5, 1]

        // 3.Arrays.fill(arr1, 2):将指定值填充到数组中
        Arrays.fill(arr1, 2);
        System.out.println(Arrays.toString(arr1));// [2, 2, 2, 2, 2, 2, 2, 2]

        // 4.Arrays.sort(arr2):对数组进行排序
        Arrays.sort(arr2);
        System.out.println(Arrays.toString(arr2));// [1, 4, 4, 4, 32, 43, 54, 68, 76]

        // 5.Arrays.binarySearch(arr2, 1):堆排序好的数组用二分法检索指定值
        int index = Arrays.binarySearch(arr2, 54);
        System.out.println(index);  // 6

        // 6.Arrays.copyOf(T[] original, int newLength)
        int[] ints = Arrays.copyOf(arr2, 14);
        System.out.println(Arrays.toString(ints)); // [1, 4, 4, 4, 32, 43, 54, 68, 76, 0, 0, 0, 0, 0]
    }
}

```



static <T>  List<T> `asList(T... a)`：数组转换为List

```java
public static <T> List<T> asList(T... a) {
    return new ArrayList<>(a);
}
```

注意

- 该方法返回的是 Arrays 内部静态类 ArrayList，而不是我们平常使用的 ArrayList。该静态类 ArrayList 没有覆盖父类的 add()、 remove() 等方法，所以如果直接调用，会报 UnsupportedOperationException 异常

- 如果将基本数据类型的数组作为参数传入，该方法会把整个数组当作一个元素

```java
public class ArraysTest {    
	@Test
    public void test1(){
        // 将数组转换为集合，接收一个可变参
        List<Integer> list1 = Arrays.asList(1, 2, 3);
        list1.forEach(System.out::println); // 1 2 3

        Integer[] data1 = {1, 2, 3};
        List<Integer> list2 = Arrays.asList(data1);
        list2.forEach(System.out::println); // 1 2 3

        // 如果将基本数据类型的数组作为参数传入，该方法会把整个数组当作一个元素
        int[] data2 = {1, 2, 3};
        List<int[]> list3 = Arrays.asList(data2);
        System.out.println(list3.size()); // 1
        System.out.println(Arrays.toString(list3.get(0))); // [1, 2, 3]
    }
}

```





### 数组的常见异常

#### 1.数组角标越界异常

- ArrayIndexOutOfBoundsException

```java
int[] arr = new int[]{1,2,3,4,5};

for(int i = 0;i <= arr.length;i++){
    System.out.println(arr[i]);
}

System.out.println(arr[-2]);

System.out.println("hello");
```



#### 2.空指针异常：

- NullPointerException

```java
//情况一：
int[] arr1 = new int[]{1,2,3};
arr1 = null;
System.out.println(arr1[0]);

//情况二：
int[][] arr2 = new int[4][];
System.out.println(arr2[0][0]);

//情况：
String[] arr3 = new String[]{"AA","BB","CC"};
arr3[0] = null;
System.out.println(arr3[0].toString());
```

> 提示：一旦程序出现异常，未处理时，就终止执行。