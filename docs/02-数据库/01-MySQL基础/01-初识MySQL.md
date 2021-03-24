# MySQL数据库

## 整体大纲

![在这里插入图片描述](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20201227112455.png)

```rust
一、为什么要学习数据库
二、数据库的相关概念      
    DBMS、DB、SQL
三、数据库存储数据的特点
四、初始MySQL
    MySQL产品的介绍        
    MySQL产品的安装          ★        
    MySQL服务的启动和停止     ★
    MySQL服务的登录和退出     ★      
    MySQL的常见命令和语法规范      
五、DQL语言的学习    ★              
    基础查询        ★             
    条件查询        ★            
    排序查询        ★                
    常见函数        ★               
    分组函数        ★              
    分组查询        ★            
    连接查询        ★           
    子查询          √                  
    分页查询        ★              
    union联合查询   √           
    
六、DML语言的学习    ★             
    插入语句                        
    修改语句                        
    删除语句                        
七、DDL语言的学习  
    库和表的管理      √              
    常见数据类型介绍   √          
    常见约束         √         
八、TCL语言的学习
    事务和事务处理                 
九、视图的讲解        √
十、变量                      
十一、存储过程和函数   
十二、流程控制结构  
```



## 一、MySQL数据库

### 数据库的好处

- 实现**数据持久化**
- 使用完整的管理系统统一管理，易于查询

### 数据库的相关概念

#### DB

- **数据库（database）**：存储数据的“仓库”。保存一组有组织的数据的容器

#### DBMS

- ==数据库管理系统（Database Management System）==：又称为数据库软件（产品），用于管理DB中的数据。

常见的数据库管理系统：MySQL、Oracle、DB2、SqlServer等。

#### SQL

==结构化查询语言（Structure Query Language）==：用于和DBMS通信的语言

SQL的优点：

1. 简单易学；
2. 不是某个特定数据库供应商专有的语言，几乎所有DBMS都支持SQL；
3. 虽然简单，但实际上是一种强有力的语言，灵活使用其语言元素，可以进行非常复杂和高级的数据库操作。

> 以上三者之间的关系：

![在这里插入图片描述](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20201227112751.png)

----

### 数据库存储数据的特点

1. 将数据放到表中，表再放到库中
2. 一个数据库中可以有多个表，每个表都有一个的名字，用来标识自己。表名具有唯一性。
3. 表具有一些特性，这些特性定义了数据在表中如何存储，类似java中 “类”的设计。
4. 表由列组成，也称为==字段==。所有表都是由一个或多个列组成的，每一列类似java 中的「**属性**」。
5. 表中的数据是按行存储的，每一行类似于java中的“对象”。

## 二、MySQL软件

MySQL数据库隶属于MySQLAB公司，总部位于瑞典，后被oracle收购。

优点：

1. 成本低：开放源代码，一般可以免费试用；
2. 简单：很容易安装和使用；
3. 性能高：执行很快。

### MySQL产品的安装

DBMS分为两类：

1. 基于共享文件系统的DBMS （Access）；
2. 基于客户机——服务器的DBMS（MySQL、Oracle、SqlServer）。

MySQL版本可分为：社区版（免费），企业版（收费）。

- Windows平台下下载：[http://dev.mysql.com/downloads/mysql](http://dev.mysql.com/downloads/mysql)

- 完整安装卸载步骤：[https://blog.csdn.net/Y_BlueBlack/article/details/81433401](https://blog.csdn.net/Y_BlueBlack/article/details/81433401)

### MySQL服务的启动和停止

方式一：右击此电脑 — 管理 — 服务 — 启动或停止 MySQL服务

方式二：通过管理员身份运行 CMD

```sql
--启动服务名
net start mysql 
--停止服务名
net stop mysql 
```

### MySQL服务端的登录和退出

方式一：通过mysql自带的客户端

- 只限于root用户

方式二：通过windows自带的客户端

登录：`mysql [–h 主机名 -P 端口号] –u 用户名 –p密码`

- -p 紧跟着密码
- –h 主机名 -P 端口号 ：可以省略
- 例：mysql -h localhost -P 3306 -u root -p，之后输入密码

退出：`exit` 或 `ctrl+C` 

```bash
# 进入mysql, 在命令行中输入 mysql–u root –p####  
# 其中：####表示密码
C:\Users\Reanon>mysql -h localhost -P 3306 -u root -p
Enter password: *******
...
mysql>
```



### MySQL常见命令

1、查看当前所有的数据库：`show databases`; 

```bash
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| school             |
| sys                |
+--------------------+
```

2、 打开指定的库：`use 库名`

```bash
mysql> use school;
Database changed
```

3、查看当前库的**所有表**：`show tables`;

```bash
mysql> show tables;
+------------------+
| Tables_in_school |
+------------------+
| category         |
| grade            |
| result           |
| student          |
| subject          |
+------------------+
5 rows in set (0.00 sec)
```

4、查看其它库的所有表：`show tables from 库名`;

```bash
mysql> show tables from mysql;
+---------------------------+
| Tables_in_mysql           |
+---------------------------+
| columns_priv              |
...
| time_zone_transition      |
| time_zone_transition_type |
| user                      |
+---------------------------+
31 rows in set (0.00 sec)
```

5 、查询当前所在数据库：`select database()`;

```bash
mysql> select database();
+------------+
| database() |
+------------+
| school     |
+------------+
1 row in set (0.00 sec)
```

6、创建表：

```sql
create table 表名(

    列名 列类型,
    列名 列类型，
    ...
);
```

```sql
mysql> create table stuinfo(
    -> id int,
    -> name varchar(20));
Query OK, 0 rows affected (0.03 sec)
```



7、查看表结构：`desc 表名`;

```bash
mysql> desc stuinfo;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id    | int(11)     | YES  |     | NULL    |       |
| name  | varchar(20) | YES  |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+
2 rows in set (0.00 sec)
```

8、查看表中的所有记录：`select * from 表名`;

```bash
mysql> select * from stuinfo;
Empty set (0.00 sec)
```



9、向表中插入记录：`insert into 表名(列名列表)  values(列对应的值的列表)`;

- 注意：插入 **varchar**  或 **date** 型的数据要用==单引号==引起来

```sql
insert into math (id,name) values(1,"ton");
```

```bash
mysql> insert into stuinfo (id,name) values(1,"tom");
Query OK, 1 row affected (0.01 sec)

mysql> select * from stuinfo;
+------+------+
| id   | name |
+------+------+
|    1 | tom  |
+------+------+
1 row in set (0.00 sec)
```



10、修改记录：`update 表名 set 列1=列1的值, 列2=列2的值 where ..`

```bash
mysql> update stuinfo set name="Tom" where id =1;
Query OK, 1 row affected (0.01 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> select * from stuinfo;
+------+------+
| id   | name |
+------+------+
|    1 | Tom  |
+------+------+
```

11、删除记录：`delete from 表名 where ...`

```bash
mysql> delete from stuinfo where id=1;
Query OK, 1 row affected (0.01 sec)

mysql> select * from stuinfo;
Empty set (0.00 sec)
```

12、删除数据表：`drop table 表名`;

```bash
mysql> drop table stuinfo;
Query OK, 0 rows affected (0.01 sec)

mysql> show tables;
+------------------+
| Tables_in_school |
+------------------+
| category         |
| grade            |
| result           |
| student          |
| subject          |
+------------------+
5 rows in set (0.00 sec)
```



13、查看服务器的版本

1. 登录到mysql服务端：`select version()`;
2. 没有登录到mysql服务端，在cmd命令界面输入：`mysql --version` 或者 `mysql --V`

```bash
# 方式一
mysql> select version();
+-----------+
| version() |
+-----------+
| 5.7.19    |
+-----------+
1 row in set (0.00 sec)

# 方式二
C:\Users\Reanon>mysql --version
mysql  Ver 14.14 Distrib 5.7.19, for Win64 (x86_64)gg

# 方式三
C:\Users\Reanon>mysql -V
mysql  Ver 14.14 Distrib 5.7.19, for Win64 (x86_64)
```



### MySQL的语法规范

1. 不区分大小写，但建议==关键字大写，表名、列名小写==
2. 每条命令可以用`;` 或 `\g` 结尾；==最好用分号结尾==
3. 每条命令根据需要，可以进行缩进或换行
4. 注释：
   - 单行注释：# 注释文字；
   - 单行注释：-- 注释文字；
   - 多行注释：/* 注释文字  */

图形化用户界面客户端

- 介绍：主要分为两种——SQLyog 12、Navicat 12 for mysql
- 安装：[https://blog.csdn.net/lihua5419/article/details/73881837/](https://blog.csdn.net/lihua5419/article/details/73881837/)
- 卸载：方法一：控制面板选中SQLyog直接卸载；方法二：卸载时担心注册表清除不干净，百度geek下载工具，下载免费版即可。下载后，放到桌面，双击后可直接选中要卸载的软件进行卸载,最后可根据选择是否清除注册表。


## 二、SQL语言的分类

> 大部分用到的是**DQL**和**DML**，偶尔会用到DDL，一般的开发人员很少会用到DCL。

### DQL

- ==数据查询语言（Data Query Language）==，用于检索数据库中的数据，主要是`SELECT`语句；

### DML

- ==数据操纵语言（Data Manipulation Language)==，用于改变数据库中的数据，主要包括 `INSERT`、`UPDATE` 和 `DELETE`语句；

### DDL

- ==数据定义语言（Data Definition Language)==，用于库和表的创建、修改、删除。主要包括`CREATE`、`DROP`、`ALTER`语句；

### DCL

- 数据控制语言（Data Control Language)，用于定义用户的访问权限和安全级别。主要包括 `GRANT` 和 `REVOKE`语句；

### TCL

- 事务控制语言（Transaction Control Language)，用于维护数据的一致性，包括`COMMIT`、`ROLLBACK`和`SAVEPOINT`语句。



### SQL的常见命令

```sql
-- 查看所有的数据库
show databases;

-- 打开指定 的库
use 库名;

-- 显示当前库中的所有表
show tables; 

-- 显示指定库中的所有表
show tables from 库名;

-- 创建表
create table 表名(
    字段名 字段类型,   
    字段名 字段类型
); 

-- 查看指定表的结构
desc 表名; 

-- 显示表中的所有数据
select * from 表名;
```
