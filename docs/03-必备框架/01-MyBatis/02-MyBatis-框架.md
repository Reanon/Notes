#  Mybatis 框架

## 三、MyBatis 框架 Dao 代理

MyBatis 框架创建 Dao接口的实现类对象， 完成对 sql 语句的执行，mybatis 创建一个对象代替你的 dao实现类功能。

**使用mybatis代理要求**：

1. mapper文件中的 `namespace`  一定dao接口的全限定名称
2. mapper文件中标签的 `id` 是dao接口方法名称

![image-20210318151409585](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210318151409.png)

**mybatis代理实现方式**

使用 SqlSession 对象的方法 `getMapper(dao.class)` 获取 dao 接口方法的实现类。

例如： 现在有 StudentDao接口

```java
SqlSession session  = MyBatisUtils.getSqlSession();
// Dao接口名.class
// 这句等同于StudentDao dao  = new StudentDaoImpl();
StudentDao dao  = session.getMapper(StudentDao.class);
// 通过接口调用方法
Student student = dao.selectById(1001);
```

### 3.1 Dao 代理实现 CURD

#### 实现步骤

##### 1、去掉 Dao 接口实现类

![image-20210318151809071](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210318151809.png)

新建一个项目：

1. 复制 ch02-dao 项目，修改 `pom.xml` 中的 artifactId

```xml
<groupId>com.xxxx</groupId>
<!-- 指定新项目的名称 -->
<artifactId>ch03-proxy-dao</artifactId>
<version>1.0</version>
```

2. 删除 target 文件和 .iml 文件

![image-20210318152600946](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210318152601.png)

3. 删除 Dao 实现类 StudentDaoImpl

![image-20210318153231903](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210318153232.png)

##### 2、getMapper 获取代理对象

只需调用 SqlSession 的 `getMapper() `方法，即可获取指定接口的实现类对象，该方法的参数为指定 ==Dao 接口类的 class 值==。

```java
SqlSession session = factory.openSession();
// StudentDao 接口.class
StudentDao dao = session.getMapper(StudentDao.class);
```

这里使用工具类：

```java
StudentDao studentDao = MyBatisUtil.getSqlSession().getMapper(StudentDao.class);
```

查看 生成的代理 dao

```java
// com.sun.proxy.$Proxy 类似于 StudentDaoImpl
System.out.println(dao.getClass().getName());
```

![image-20210318153909432](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210318153909.png)

##### 3、使用 Dao 代理对象方法执行 sql 语句

select 方法：

```java
@Test
public void testSelectById() {
    // 1.获取SqlSession
    SqlSession sqlSession = MyBatisUtil.getSqlSession();

    // 2. 获取dao 代理类
    StudentDao dao = sqlSession.getMapper(StudentDao.class);

    // 3.通过代理执行sql
    Student student = dao.selectStudentById(1004);
    System.out.println("查询结果:" + student);

    // 4.关闭SqlSession对象
    sqlSession.close();
}
```

selectStudents 方法：

```java
// 测试方法
@Test
public void testSelectStudents() {
    SqlSession sqlSession = MyBatisUtil.getSqlSession();
    StudentDao dao = sqlSession.getMapper(StudentDao.class);
    List<Student> students = dao.selectStudents();
    students.forEach(student -> System.out.println(student));
    sqlSession.close();
}
```

insert 方法：

```java
@Test
public void testInsert() throws IOException {
    Student student = new Student();
    student.setId(1006);
    student.setName("林浩");
    student.setEmail("linhao@163.com");
    student.setAge(26);
    int nums = studentDao.insertStudent(student);
    System.out.println("使用 Dao 添加数据:"+nums);
}
```

update 方法：

```java
@Test
public void testUpdate() throws IOException {
    Student student = new Student();
    student.setId(1006);
    student.setAge(28);
    int nums = studentDao.updateStudent(student);
    System.out.println("使用 Dao 修改数据:"+nums);
}
```

delete 方法

```java
@Test
public void testDelete() throws IOException {
    int nums = studentDao.deleteStudent(1006);
    System.out.println("使用 Dao 修改数据:"+nums);
}
```

#### Dao 原理

动态代理

![image-20210219111417832](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210219111417.png)

MapperProxy 类定义:

![image-20210219111423498](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210219111423.png)

invoke()方法:

![image-20210219111428165](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210219111428.png)

重点方法:

![image-20210219111435889](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210219111435.png)



### 3.2 深入理解参数

#### * parameterType

parameterType：接口中**方法参数的类型**， 类型的完全限定名或别名。

- 用于指定 dao 方法的形参数据类型，这个形参的数据类型是给mybatis使用
- 这个属性是可选的，因为 MyBatis 可以推断出具体传入语句的参数，默认值为未设置（unset）。
- parameterType：mybatis通过反射机制可以获取 dao接口方法参数的类型， 所以可以不写。

接口中方法的参数从 java 代码传入到 mapper 文件的 sql 语句。

- int 或 java.lang.Integer 
- hashmap 或 java.util.HashMap 
- list 或 java.util.ArrayList student 或 com.xxxx.domain.Student

`<select>`、`<insert>`、`<update>`、`<delete>` 都可以使用 parameterType 指定类型。

mybatis执行的sql语句：

```java
// ? 是占位符, 使用 jdbc 中的 PreparedStatement 执行这样的sql语
select id,name,email,age from student where id=?
```

等同于

```java
PreparedStatement pst  = conn.preparedStatement("select id,name,email,age from student where id=?");
// 给 ? 位置赋值
// 参数 Integer , 执行
pst.setInt(1, 1005);
///参数是String, 执行
pst.setString(1,"1005");
```

**parameterType 用法**

1. java 类型的 全限定类型名称：`parameterType="java.lang.Integer"`
2. mybatis 定义的 java 类型的别名  `parameterType="int"`

例如：

```xml
<select id="selectStudentById" parameterType="int" resultType="com.xxxx.domain.Student">
    select id, name, email, age
    from student
    where id = #{studentId}
</select>
```

等同于

```xml
<select id="selectStudentById" parameterType="java.lang.Integer" resultType="com.xxxx.domain.Student">
    select id, name, email, age
    from student
    where id = #{studentId}
</select>
```

![image-20210318155827500](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210318155827.png)



#### * 一个简单参数

==MyBatis 传递参数==：从 java 代码中把参数传递到 mapper.xml 文件。

当 Dao 接口中方法的参数只有一个简单类型（java 基本类型和 String）时，占位符 `#{任意字符}`和方法的参数名无关。

例如：

接口方法：`Student selectById(int id);`

mapper 文件：

- `#{studentId}` , studentId 是自定义的变量名称，和方法参数名无关，即可以随便写。

```xml
<!--
 dao接口是一个简单类型的参数
 mapper文件，获取这个参数值，使用#{任意字符}
-->
<select id="selectById" resultType="com.xxxx.domain.Student">
    select id,name,email,age from student where id=#{studentId}
</select>
```

测试方法：

```java
@Test
public void testSelectById(){
    // 一个参数
    Student student = studentDao.selectById(1005);
    System.out.println("查询 id 是 1005 的学生："+student);
}
```

----

#### *多个参数-使用@Param

`@Param`：命名参数， 在方法的形参前面使用的， 定义参数名。 

当 Dao 接口方法多个参数，需要通过名称使用参数。

- 在方法形参前面加入`@Param("自定义参数名")`
-  mapper 文件使用`#{自定义参数名}`。

例如：

定义接口方法：`List selectStudent( @Param("myName") String name ) { … }` 

```java
/*
	多个简单类型的参数
	使用@Param命名参数， 注解是mybatis提供的
	位置：在形参定义的前面
	属性：value 自定义的参数名称
*/
// value = 可以省略
List<Student> selectByNameOrAge(@Param(value = "myName") String name,
                          @Param(value = "myAge") Integer age);
```

mapper 文件： `select * from student where name = #{myName}`

- 多个参数时，mapper 文件中的 `#{...}` 参数名需要与抽象方法里`@Param(...)`定义的参数名保持一致。

```xml
<!--
	多个简单类型的参数.
	当使用了@Param命名后，例如@Param("myName").
	在mapper中，使用#{命名的参数}， 例如 #{myName}
-->
<select id="selectByNameOrAge" resultType="com.xxxx.domain.Student">
    select id, name, email, age
    from student
    where name = #{myName}
    or age = #{myAge}
</select>
```

测试方法：

```java
@Test
public void testSelectByNameOrAge() {
    // 1.获取SqlSession
    SqlSession sqlSession = MyBatisUtil.getSqlSession();
    // 2. 获取dao 代理
    StudentDao dao = sqlSession.getMapper(StudentDao.class);
    // 3.通过代理执行sql
    List<Student> students = dao.selectByNameOrAge("张三", 26);
    students.forEach(student -> System.out.println("查询结果:" + student));
    // 4.关闭SqlSession对象
    sqlSession.close();
}
```

![image-20210318161956410](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210318161956.png)



#### *多个参数-使用对象

使用 java 对象传递参数， java 的属性值就是 sql 需要的参数值，每一个属性就是一个参数。 

- 语法格式： `#{property, javaType = java中数据类型名, jdbcType = 数据类型名称 }` 
  - `property` 是 Java 对象的属性名称，该属性需要有 get/set 方法
  - javaType、jdbcType 的类型 MyBatis 可以检测出来，一般不需要设置。

常用格式 `#{property}` 

- ——mybatis-3.5.1.pdf 第 43 页 4.1.5.4 小节：

![image-20210219112743580](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210219112743.png)

创建保存参数值的对象 QueryParam

```java
package com.xxxx.vo;
public class QueryParam {
    private String queryName;
    private int queryAge;
    // set ，get 方法
}
```

接口方法：

```java
/*
*  一个java对象作为参数(对象有属性, 每个属性有set，get方法)
*  这里的传入的对象并没有限定，只有该对象的属性有set/get方法 就可以作为参数
*/
List<Student> selectMultiObject(QueryParam queryParam);
```

mapper 文件：

```xml
<!--
	一个java对象作为方法的参数，使用对象的属性作为参数值使用
	简单的语法: #{属性名}, mybatis调用此属性的getXXX()方法获取属性值
-->
<select id="selectMultiObject" resultType="com.xxxx.domain.Student">
    <!--#{属性名} 需要与对象中的属性完全一致-->
	select id,name,email,age from student where name=#{queryName} or age=#{queryAge}
</select>
```

或

```xml
<select id="selectMultiObject" resultType="com.xxxx.domain.Student">
    select id,name,email,age from student where name=#{queryName, javaType=string, jdbcType=VARCHAR} or age =#{queryAge, javaType=int, jdbcType=INTEGER}
</select>
```

测试方法：

```java
@Test
public void selectMultiObject(){
    // 1.获取SqlSession
    SqlSession sqlSession = MyBatisUtil.getSqlSession();
    // 2. 获取dao 代理
    StudentDao dao = sqlSession.getMapper(StudentDao.class);
    // 设置传入对象的属性
    QueryParam qp = new QueryParam();
    qp.setQueryName("李力");
    qp.setQueryAge(20);
    
    // 3.通过代理执行sql
    List<Student> stuList = studentDao.selectMultiObject(qp);
    stuList.forEach( stu -> System.out.println(stu));
    // 4.关闭SqlSession对象
    sqlSession.close();    
}
```

#### 多个参数-按位置

参数位置从 0 开始， 引用参数语法 `#{arg 位置 }` ：

- 第一个参数是 `#{arg0}`，第二个是`#{arg1}`
- 不推荐使用这种方法

注意：mybatis-3.3 版本和之前的版本使用  #{0}、#{1}方式，从 mybatis3.4 开始使用#{arg0}方式。

接口方法：

```java
List<Student> selectByPosition(String name, int age);
```

mapper 文件：

```xml
<select id="selectByPosition" resultType="com.xxxx.domain.Student">
	select id,name,email,age from student where name=#{arg0} or age =#{arg1}
</select>
```

测试方法：

```java
@Test
public void testSelectByPosition(){
    // 按位置参数
    List<Student> stuList = studentDao.selectByNameAndAge("李力",20);
    stuList.forEach( stu -> System.out.println(stu));
}
```

#### 多个参数-使用 Map

Map 集合可以存储多个值，使用 Map 向 mapper 文件一次传入多个参数。

- Map 集合使用 String的 key、Object 类型的值存储参数。 
- mapper 文件通过Map集合中的键名获取参数值：即使用 `#{key}` 引用==参数值==。

例如：

```java
Map data = new HashMap(); 
data.put("myname","李力"); 
data.put("myage",20);
```

接口方法：

```java
/*
 使用Map作为参数
*/
List<Student> selectMultiMap(Map<String, Object> map);
```

mapper 文件：

- `#{...}` 中的参数名需要与传入的 Map 的键名相同

```xml
<!--
	使用Map传递参数，
	在mapper文件中，获取map的值, 是通过key获取的
	语法：#{key}
-->
<select id="selectMultiMap" resultType="com.bjpowernode.domain.Student">
    select id,name,email,age from student where name=#{myname} or age =#{myage}
</select>
```

测试方法：

```java
@Test
public void testSelectMultiMap(){
    // 1.获取SqlSession
    SqlSession sqlSession = MyBatisUtil.getSqlSession();
    // 2. 获取dao 代理
    StudentDao dao = sqlSession.getMapper(StudentDao.class);
    // 使用map传递参数
    Map<String,Object> data = new HashMap<>();
    // 对应 Mapper 文件中的 #{myname}
    data.put("myname","李力");
    // 对应 Mapper 文件中的  #{myage}
    data.put("myage",20); 
    List<Student> stuList = studentDao.selectMultiMap(data);
    stuList.forEach( stu -> System.out.println(stu));
    // 4.关闭SqlSession对象
    sqlSession.close(); 
}
```

### 3.3 # 和 $

####  # 占位符

语法： `#{字符}`

- 告诉 mybatis 使用实际的参数值代替。
- 使用 PrepareStatement 对象执行 sql 语句
-  #{…} 代替 sql 语句的 「?」

`#{}` 特点：

1. 使用的 `PrepareStatement` 对象，执行sql语句，效率高。
2. 使用的 PrepareStatement 对象，**能避免sql注入**， sql语句执行更安全。
3.  `#{}` 常常**作为列值**使用的， 位于等号的右侧；`#{}`位置的值和数据类型有关的。

mapper 文件：

```xml
<select id="selectById"  parameterType="integer" resultType="com.bjpowernode.domain.Student">
    select id,name,email,age from student where id=#{studentId}
</select>
```

转为 MyBatis 的执行是：mybatis出创建PrepareStatement对象，执行sql语句

- where id=? 就是 where id= #{studentId} 
- ps.setInt(1,1005) ，1005 会替换掉 #{studentId}

```java
String sql="select id,name,email,age from student where id=?";
PreparedStatement pst = conn.prepareStatement(sql);
// 传递参数
ps.setInt(1, 1005);
// 执行sql语句
ResultSet rs  = pst.executeQuery(); 
```

#### $ 占位符

语法 :  ${字符}

- `${}` 表示字符串连接，把sql语句的其他内容和 `${}`内容使用字符串连接的方式连在一起
- 使用 `Statement` 把 sql 语句和 ${} 的 内容连接起来。

${} 的特点

1. 使用Statement对象，执行sql语句，效率低
2. ${}占位符的值，使用的字符串连接方式， **有sql注入的风险**， 有代码安全的问题
3. `${}` 数据是原样使用的， **不会区分数据类型**。
4. `${}` 常用作表名或者列名， 在能保证数据安全的情况下使用 `${}`

例 1： 分别使用 id、email 列查询 Student

接口方法：

```java
Student findById(int id);
Student findByEmail(String email);
```

mapper 文件：

```xml
<select id="findById" resultType="com.xxxx.domain.Student">
    select * from student where id=${studentId}
</select>
<select id="findByEmail" resultType="com.xxxx.domain.Student">
    select * from student where email=${stuentEmail}
</select>
```

==相当于mybatis创建Statement对象==， 执行sql语句

```java
Statement stmt  = conn.createStatement(sql);
ResultSet rs  = stmt.executeQuery();
```

测试方法

```java
@Test
public void testFindStuent(){
    Student student1 = studentDao.findById(1002);
    System.out.println("findById:"+student1);
    Student student2 = studentDao.findByEmail("zhou@126.net");
    System.out.println("findByEmail:"+student2);
}
```

例 2：通用方法，使用不同列作为查询条件

接口方法：

- 使用 命名参数 `@Param` 

```java
Student findByDiffField(@Param("col") String colunName, @Param("cval") Object value);
```

mapper 文件：

```xml
<select id="findByDiffField" resultType="com.bjpowernode.domain.Student">
    select * from student where ${col} = #{cval}
</select>
```

测试方法

```java
@Test
public void testFindDiffField(){
    Student student1 = studentDao.findByDiffField("id", 1002);
    System.out.println("按 id 列查询:"+student1);
    Student student2 = studentDao.findByDiffField("email", "zhou@126.net");
    System.out.println("按 email 列查询:"+student2);
}
```

### 3.4 封装 MyBatis 输出结果

#### resultType

resultType 是执行 sql 得到 **ResultSet** 转换的类型，使用**类型的完全限定名**或别名。 

- 注意如果返回的是集合，那应该设置为**集合包含的类型**，而不是集合本身。
- resultType 和 resultMap，不能同时使用。

![image-20210219114349279](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210219114349.png)

resultType：在执行select时使用， 作为 `<select>` 标签的属性出现的表示结果类型；mysql执行sql语句得到 java对象的类型，它的值有两类：

1. java类型的全限定名称
2. 使用别名

##### A、简单类型

resultType 表示简单类型

接口方法：

```java
int countStudent();
```

mapper 文件：

```xml
<!--
  执行sql语句，得到是一个值（一行一列）
-->
<select id="countStudent" resultType="int">
    select count(*) from student
</select>
```

测试方法：

```java
@Test
public void testRetunInt(){
    int count = studentDao.countStudent();
    System.out.println("学生总人数："+ count);
}
```

##### B、对象类型

resultType 表示 java 自定义对象

接口方法：

```java
Student selectById(int id);
```

mapper 文件：

- 现在使用 java类型的全限定名称，表示的意思 mybatis 执行sql，把 ResultSet 中的数据转为 Student 类型的对象。

```xml
<select id="selectById" resultType="com.xxxx.domain.Student">
    select id,name,email,age from student where id=#{studentId}
</select>
```

 mybatis框架会做以下的处理： 使用构造方法创建对象：

1. 调用 `com.xxxx.domain.Student` 的无参数构造方法，创建对象

   - ```java
     Student student = new Student(); //使用反射创建对象
     ```

2. 调用 setXXX() 给==同名的列赋值给同名的属性==

   - ```java
     student.setId(rs.getInt("id"));
     student.setName(rs.getString("name"));
     ```

   - ![image-20210219114702573](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210318174002.png)

3. 得到java对象；如果dao接口返回值是List集合，mybatis把 student 对象放入到List集合。

注意：Dao 接口方法返回是**集合类型**，需要指定集合中的类型，不是集合本身。

![image-20210219114745638](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210219114745.png)

所以执行`Student mystudent = dao.selectById(1001)` 得到 数据库中 id=1001这行数据；

-  这行数据的列值，赋值给了mystudent对象的属性。 
- 得到 mystudent对象，就相当于是 id=1001这行数据。

##### C、Map

resultType表示一个 map 结构

sql 的查询结果作为 Map 的 key 和 value，推荐使用 Map。 

- 注意：Map 作为接口返回值，sql 语句的查询结果**最多只能有一条记录**。大于一条记录是错误。

接口方法：

```java
// 查询结果返回是一个Map
Map<Object,Object> selectReturnMap(int id);
```

mapper 文件：

```xml
<!--
   执行 sql 得到一个Map结构数据，mybatis执行sql，把ResultSet转为map
   sql执行结果，列名做map的key,列值作为value
   sql执行得到是一行记录，转为map结构是正确的
   dao接口返回是一个map，sql语句最多能获取一行记录，多余一行是错误
-->
<select id="selectReturnMap" resultType="java.util.HashMap">
    select name,email from student where id = #{studentId}
</select>
```

测试方法：

```java
@Test
public void testReturnMap(){
    Map<Object,Object> retMap = studentDao.selectReturnMap(1002);
    System.out.println("查询结果是 Map:"+retMap);
}
```

#### resultMap

resultMap 可以自定义 sql 的列名和 java 对象属性的映射关系。更灵活的把列值赋值给指定属性。 

- 常用在列名和 java 对象属性名不一样的情况。

使用方式： 

1. 先定义 resultMap，指定列名和属性的对应关系。 
2. 在select标签使用resultMap属性，指定上面定义的resultMap的id值接口方法：

```java
List<Student> selectUseResultMap(QueryParam param);
```

mapper 文件：

```xml
<!-- 创建 resultMap
 	id:自定义的唯一名称，在<select>使用
 	type:期望转为的 java 对象的全限定名称或别名
-->
<resultMap id="studentMap" type="com.bjpowernode.domain.Student">
    <!--定义列名和属性名的对应-->
    <!-- 主键字段使用 id -->
    <id column="id" property="cid" />
    <!--非主键字段使用 result-->
    <result column="name" property="cname"/>
    <result column="email" property="email" />
    <result column="age" property="age" />
</resultMap>

<!--resultMap: resultMap 标签中的 id 属性值-->
<select id="selectUseResultMap" resultMap="studentMap">
    select id,name,email,age from student where name=#{queryName} or
    age=#{queryAge}
</select>
```

测试方法：

```java
@Test
public void testSelectUseResultMap(){
    QueryParam param = new QueryParam();
    param.setQueryName("李力");
    param.setQueryAge(20);
    List<Student> stuList = studentDao.selectUseResultMap(param);
    stuList.forEach( stu -> System.out.println(stu));
}
```

#### 自定义别名

mybatis提供的对 java 类型定义简短，好记名称；但是更加==推荐使用全限定名称==。

自定义别名的步骤：

1. 在 **mybatis 主配置文件**，使用 `typeAliases` 标签声明别名
2. 在mapper文件中，resultType="别名"

![image-20210318175952462](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210318175952.png)

1、声明别名（mybatis主配置文件）

- 可以点开 Configuration 查看声明顺序

![image-20210318180258193](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210318180258.png)



![image-20210318180418197](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210318180418.png)

第一种方式：

```xml
<!-- 自定义别名 -->
<typeAliases>
    <!--第一种语法格式
        type:java类型的全限定名称（自定义类型）
        alias:自定义别名
	    优点： 别名可以自定义
         缺点： 每个类型必须单独定义
    -->
    <typeAlias type="com.bjpowernode.domain.Student" alias="stu" />
    <!--<typeAlias type="com.bjpowernode.domain.Student" alias="stu" />
        <typeAlias type="com.bjpowernode.vo.QueryParam" alias="qp" />-->
</typeAliases>
```

mapper文件中使用

```xml
<!-- 使用 resultType="别名" -->
<select id="selectById"  parameterType="integer" resultType="stu">
    select id,name,email,age from student where id=#{studentId}
</select>
```



第二种方式：

- `name=包名`：mybatis会把这个包中所有类名作为别名（不用区分大小写）
  - 优点：使用方便，一次给多个类定义别名
  - 缺点: 别名不能自定义，==必须是类名==。

```xml
<!-- 自定义别名 -->
<typeAliases>
    <!--第二种方式 -->
    <package name="com.bjpowernode.domain" />
    <package name="com.bjpowernode.vo" />
</typeAliases>
```

mapper文件中使用

```xml
<!-- 使用 resultType="类名" -->
<select id="selectById"  parameterType="integer" resultType="Student">
    select id,name,email,age from student where id=#{studentId}
</select>
```



#### 实体类属性名和列名不同

 列名 和 java对象属性名称不一样解决方式

1. 使用resultType:  使用==列别名==，让别名和java对象属性名称一样
2. 使用resultMap： 自定义列名和属性名称对应关系

##### 列别名和 `<resultType>`

操作步骤： 

1、创建新的实体类 PrimaryStudent

```java
package com.bjpowernode.domain;
/**
* <p>Description: 实体类 </p>
*/
public class PrimaryStudent {
    // 对象属性名 与 列名不同
    private Integer stuId;
    private String stuName;
    private Integer stuAge;
    // set , get 方法
}
```

2、接口方法：

```java
List<PrimaryStudent> selectUseFieldAlias(QueryParam param);
```

3、mapper 文件

```xml
<!-- 列名 as 属性名 -->
<select id="selectUseFieldAlias"  resultType="com.bjpowernode.domain.PrimaryStudent">
    select id as stuId, name as stuName,age as stuAge from student where name=#{queryName} or age=#{queryAge}
</select>
```

4、测试方法：

```java
@Test
public void testSelectUseFieldAlias(){
    QueryParam param = new QueryParam();
    param.setQueryName("李力");
    param.setQueryAge(20);
    List<PrimaryStudent> stuList;
    stuList = studentDao.selectUseFieldAlias(param);
    stuList.forEach( stu -> System.out.println(stu));
}
```



##### `<resultMap>`

1、接口方法：

```java
List<PrimaryStudent> selectUseDiffResultMap(QueryParam param);
```

2、mapper 文件：

```xml
<!-- 创建 resultMap
 	id:自定义的唯一名称，在<select>使用
 	type:期望转为的 java 对象的全限定名称或别名
-->
<resultMap id="primaryStudentMap" type="com.bjpowernode.domain.PrimaryStudent">
    <!-- column 指定表中的列名 -->
    <!-- property 指定自定义的类的属性名 -->
    <id column="id" property="stuId" />
    <result column="name" property="stuName"/>
    <result column="age" property="stuAge" />
</resultMap>

<!--resultMap: resultMap 标签中的 id 属性值-->
<select id="selectUseDiffResultMap" resultMap="primaryStudentMap">
    select id,name,email,age from student where name=#{queryName} or age=#{queryAge}
</select>
```

3、测试方法：

```java
@Test
public void testSelectUseDiffResultMap(){
    QueryParam param = new QueryParam();
    param.setQueryName("李力");
    param.setQueryAge(20);
    List<PrimaryStudent> stuList;
    stuList = studentDao.selectUseDiffResultMap(param);
    stuList.forEach( stu -> System.out.println(stu));
}
```

### 3.4 模糊 like

模糊查询的实现有两种方式：

1.  java 代码中给查询数据加上 `%` ; 
2.  在 mapper 文件 sql 语句的条件位置加上 `%`

例 1： java 代码中提供要查询的 `%力%`

1、接口方法：

```java
List<Student> selectLikeFirst(String name);
```

2、mapper 文件：

```xml
<select id="selectLikeFirst" resultType="com.bjpowernode.domain.Student">
    select id,name,email,age from student where name like #{studentName}
</select>
```

3、测试方法：

```java
@Test
public void testSelectLikeOne(){
    String name="%力%";
    List<Student> stuList = studentDao.selectLikeFirst(name);
    stuList.forEach( stu -> System.out.println(stu));
}
```

例 2：mapper 文件中使用 like name "%" #{xxx} "%"

- sql语句 like的格式：  where name like `"%"空格 #{name}空格"%"`

1、接口方法：

```java
List<Student> selectLikeSecond(String name);
```

2、mapper 文件：

```xml
<select id="selectLikeSecond" resultType="com.bjpowernode.domain.Student">
    select id,name,email,age from student where name like "%" #{studentName} "%"
</select>
```

3、测试方法：

```java
@Test
public void testSelectLikeSecond(){
    String name="力";
    List<Student> stuList = studentDao.selectLikeSecond(name);
    stuList.forEach( stu -> System.out.println(stu));
}
```

## 四、MyBatis 框架动态 SQL

**动态 SQL**：通过 MyBatis 提供的各种标签对条件作出判断以实现动态拼接 SQL 语句。

- 条件判断使用的表达式为 OGNL 表达式。
- 常用的**动态 SQL 标签**有`<if>`、`<where>`、`<choose/>`、`<foreach>`等。
- MyBatis 的动态 SQL 语句，与 JSTL 中的语句非常相似。 

动态 SQL，主要用于解决查询条件不确定的情况：在程序运行期间，根据用户提交的查询条件进行查询。

- 提交的查询条件不同，执行的 SQL 语句不同。
- 若将每种可能的情况均逐一列出，对所有条件进行排列组合，将会出现大量的 SQL 语句。

此时，可使用动态 SQL 来解决这样的问题

### 4.1 环境准备

1. 创建新的 maven 项目，加入 mybatis ， mysql 驱动依赖
2. 创建实体类 Student 、 StudentDao 接口、StudentDao.xml、mybatis.xml、测试类
3. 使用之前的表 Student。

在 mapper 的动态 SQL 中若出现大于号（>）、小于号（<）、大于等于号（>=），小于等于号（<=）等符号，最好将其转换为实体符号。否则，XML 可能会出现解析出错问题。

- 特别是对于小于号（<），在 XML 中是绝不能出现的。否则解析 mapper 文件会出错。

实体符号表：

![image-20210219120101094](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210219120101.png)

### 4.2  `<if>` 标签

对于该标签的执行，当 test 的值为 true 时，会将其包含的 SQL 片断拼接到其所在的 SQL 语句中。 语法： `<if test="条件">`  sql 语句的部分  `</if>`

1、接口方法：

```java
List<Student> selectStudentIf(Student student);
```

2、mapper 文件

- 需要注意全限定名称是否正确

```xml
<!--if
    test: 使用对象的属性值作为条件
-->
<select id="selectIf" resultType="com.bjpowernode.domain.Student">
    select * from student
    where
    <if test="name != null and name !=''">
        name = #{name}
    </if>
    <if test="age > 0 ">
        or age &gt; #{age}
    </if>
</select>
```

3、测试方法：

```java
@Test
public void testIf(){
    //1.获取SqlSession
    SqlSession session = MyBatisUtil.getSqlSession();
    //2.获取dao的代理
    StudentDao dao = session.getMapper(StudentDao.class);

    Student student = new Student();
    student.setName("李四");
    student.setAge(20);

    List<Student> students = dao.selectIf(student);
    students.forEach( stu-> System.out.println("stu=="+stu));
    //3.关闭SqlSession对象
    session.close();
}
```

![image-20210324173901394](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210324173901.png)

为了避免其中一个条件满足，而另一个条件不满足，导致出现以下的错误语句：

```sql
select * from student where  or age > ? 
```

需要在前面添加判断条件如：

- 1 =1 

```xml
<select id="selectIf" resultType="com.bjpowernode.domain.Student">
    select * from student
    where 1 = 1
    <if test="name != null and name !=''">
        or name = #{name}
    </if>
    <if test="age > 0 ">
        or age &gt; #{age}
    </if>
</select>
```



### 4.3  `<where>` 标签

`<if/>`标签的中存在一个比较麻烦的地方：需要在 where 后手工添加 1=1 的子句。

- 因为，若 where 后的所有`<if/>`条件均为 false，而 where 后若又没有 1=1 子句，则 SQL 中就会只剩下一个空的 where，SQL出错。
- 所以，在 where 后，需要添加永为真子句 1=1，以防止这种情况的发生。但当数据量很大时，会严重影响查询效率。

使用`<where/>`标签，在有查询条件时，可以自动添加上 where 子句；没有查询条件时，不会添加where 子句。

- 需要注意的是，第一个`<if/>`标签中的 SQL 片断，可以不包含 and。不过，写上 and 或者 or 也不错，系统会将多出的 and 和 or 去掉。
- 但其它`<if/>`中 SQL 片断的 and，**必须要求写上**。否则 SQL 语句将拼接出错

语法：`<where>` 其他动态 sql `</where>`

```xml
<where>
    <if test="条件1">sql语句1</if>
    <if test="条件2">sql语句2</if>
</where>
```

1、接口方法：

```java
List<Student> selectStudentWhere(Student student);
```

2、mapper 文件

```xml
<select id="selectStudentWhere" resultType="com.bjpowernode.domain.Student">
    select id,name,email,age from student
    <where>
        <!-- 第一条IF语句会自动去掉多余的 and 和 or -->
        <if test="name != null and name !='' ">
            and name = #{name}
        </if>
        <!-- 第二条IF语句之后必须添加连接符 -->
        <if test="age > 0 ">
            and age &gt; #{age}
        </if>
    </where>
</select>
```

3、测试方法：

```java
@Test
public void testWhere(){
    //1.获取SqlSession
    SqlSession session = MyBatisUtil.getSqlSession();
    //2.获取dao的代理
    StudentDao dao = session.getMapper(StudentDao.class);

    Student student = new Student();
    student.setName(null);
    student.setAge(20);

    List<Student> students = dao.selectWhere(student);
    students.forEach( stu-> System.out.println("stu=="+stu));
    //3.关闭SqlSession对象
    session.close();
}
```

紧跟着 where 的 or 或 where 被删除了，如果没有条件满足，则不会有where 语句。

![image-20210324185910704](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210324185910.png)

### 4.4   `<foreach>` 循环

`<foreach/> `标签用于实现对于数组与集合的遍历，一般使用在 `in` 语句中。

需要注意：

- collection 表示要遍历的集合类型，list、array 等。
- open、close、separator 为对遍历内容的 SQL 拼接。

语法：

```xml
<foreach collection="集合类型" open="开始的字符" close="结束的字符"
         item="集合中的成员" separator="集合成员之间的分隔符">
    #{item 的值}
</foreach>
```

标签属性：

- `collection`： 表示循环的对象是数组还是list集合。  
  - 如果dao接口方法的形参是数组 collection="array"
  - 如果dao接口形参是 List， collection="list"
- `open`：循环开始时的字符，类似于 sql.append("(");
- `close`：循环结束时字符，类似于sql.append(")");
- `item`：集合成员， 自定义的变量。   
  - Integer item  = idlist.get(i); // item是集合成员
- `separator`：集合成员之间的分隔符。 
  -  sql.append(","); //集合成员之间的分隔符
- `#{item 的值}`：获取集合成员的值。

相当于

```java
@Test
public void testFor(){
    List<Integer> idlist = new ArrayList<>();
    idlist.add(1001);
    idlist.add(1002);
    idlist.add(1003);
    // 查询 id 在 idlist中的student
    // select * from student where id in (1001,1002,1003)
    StringBuffer sql= new StringBuffer("");
    sql.append("select * from student where id in ");
    
    //使用循环， 把List数据 追加到 sql 字符串中。
    //循环之前加入 (
    sql.append("(");
    for(int i=0;i< idlist.size();i++){
        Integer item  = idlist.get(i);// item是集合成员
        sql.append(item);//添加成员到 sql字符串
        sql.append(","); //集合成员之间的分隔符
    }
    // 删除最后的 ,
    sql.deleteCharAt( sql.length()-1) ;
    //循环之后，加入 )
    sql.append(")");
    
    System.out.println("sql==="+sql);
}
```

#### 遍历 List<简单类型>

表达式中的 List 使用 list 表示，其大小使用 list.size 表示。

需求：查询学生 id 是 1001,1002,1003 

1、接口方法：

```java
List<Student> selectForList(List<Integer> idList);
```

2、mapper 文件

- 需要判断是否为空

```xml
<select id="selectForList"
        resultType="com.bjpowernode.domain.Student">
    select id,name,email,age from student
    <if test="list !=null and list.size > 0 ">
        where id in
        <foreach collection="list" open="(" close=")"
                 item="stuid" separator=",">
            #{stuid}
        </foreach>
    </if>
</select>
```

3、测试方法：

```java
@Test
public void testSelectForList(){
    //1.获取SqlSession
    SqlSession session = MyBatisUtil.getSqlSession();
    //2.获取dao的代理
    StudentDao dao = session.getMapper(StudentDao.class);
    List<Integer> idList = new ArrayList<>();
    idList.add(1001);
    idList.add(1002);
    idList.add(1003);

    List<Student> students = dao.selectForList(idList);
    students.forEach( stu-> System.out.println("stu=="+stu));
    //3.关闭SqlSession对象
    session.close();
}
```



![image-20210324191706467](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210324191706.png)

#### 遍历 List<对象类型>

1、接口方法：

```java
List<Student> selectStudentForList2(List<Student> stuList);
```

2、mapper 文件

- stuobject 指代一个对象
- `#{stuobject.id}` 是取出对象的属性

```xml
<select id="selectStudentForList2"
        resultType="com.bjpowernode.domain.Student">
    select id,name,email,age from student
    <if test="list !=null and list.size > 0 ">
        where id in
        <foreach collection="list" open="(" close=")"
                 item="stuobject" separator=",">
            #{stuobject.id}
        </foreach>
    </if>
</select>
```

3、测试方法：

```java
@Test
public void testSelectForeachTwo(){
    //1.获取SqlSession
    SqlSession session = MyBatisUtil.getSqlSession();
    //2.获取dao的代理
    StudentDao dao = session.getMapper(StudentDao.class);
    
    List<Student> list  = new ArrayList<>();
    
    Student s1 = new Student();
    s1.setId(1001);
    Student s2 = new Student();
    s2.setId(1005);
    list.add(s1);
    list.add(s2);
    
    List<Student> students  = dao.selectForeach2(list);
    students.forEach( stu-> System.out.println("stu=="+stu));
    //3.关闭SqlSession对象
    session.close();
}
```

### 4.5  sql 标签

`<sql/>` 标签用于定义 SQL 片断，以便其它 SQL 标签复用。

- 而其它标签使用该 SQL 片断，需要使用 `<include/>`子标签。
- 该`<sql/>`标签可以定义 SQL 语句中的任何部分，所以`<include/>`子标签可以放在动态 SQL 的任何位置。

使用方式：

1、在mapper文件中定义sql代码片段：

- ` <sql id="唯一字符串">  部分sql语句  </sql>`

2、在其他的位置，使用 `<include refid= "唯一字符串"/>`引用某个代码片段

1、接口方法：

```java
List<Student> selectStudentSqlFragment(List<Student> stuList);
```

2、mapper 文件

```xml
<!--创建 sql 片段 id:片段的自定义名称-->
<sql id="studentSql">
    select id,name,email,age from student
</sql>
<select id="selectStudentSqlFragment"
        resultType="com.bjpowernode.domain.Student">
    <!-- 引用 sql 片段 -->
    <include refid="studentSql"/>
    <if test="list !=null and list.size > 0 ">
        where id in
        <foreach collection="list" open="(" close=")"
                 item="stuobject" separator=",">
            #{stuobject.id}
        </foreach>
    </if>
</select>
```

3、测试方法：

```java
@Test
public void testSelectSqlFragment(){
    //1.获取SqlSession
    SqlSession session = MyBatisUtil.getSqlSession();
    //2.获取dao的代理
    StudentDao dao = session.getMapper(StudentDao.class);

    List<Student> list = new ArrayList<>();
    Student s1 = new Student();
    s1.setId(1002);
    list.add(s1);
    s1 = new Student();
    s1.setId(1005);
    list.add(s1);
    List<Student> students = dao.selectStudentSqlFragment(list);

    students.forEach( stu-> System.out.println("stu=="+stu));
    //3.关闭SqlSession对象
    session.close();
}
```

![image-20210324194101143](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210324194101.png)

---

本文参考：

> B 站动力节点SSM框架全套教程：https://www.bilibili.com/video/BV1Tv411b7Dp

