# MySQL-DML语言

```
DML语言的学习    ★             
    插入语句                        
    修改语句                        
    删除语句      
```



==数据操纵语言（Data Manipulation Language==，DML)，用于改变数据库中的数据，主要包括 `INSERT`、`UPDATE` 和 `DELETE`语句；

- 插入：`INSERT`
- 修改：`UPDATE`
- 删除： `DELETE`

## 插入语句

### 经典的插入

语法：

```mysql
INSERT INTO 表名 (字段名, ...) VALUES (值, ...) ;
```

特点

1.  要求值的类型和字段的类型要一致或兼容；
   - **字符类型**、**日期类型**需要使用单引号引起来
2. 字段的个数和顺序不一定与原始表中的字段个数和顺序一致但必须保证值和字段一一对应；
3. 假如表中有可以为null的字段，注意可以通过以下两种方式插入null值：
   1. 字段和值都省略
   2. ==字段写上，值使用null==
4. 字段和值的个数必须一致
5. 字段名可以省略，默认所有列
6.  **支持插入多行**



1、插入的值的类型要与列的类型一致或兼容

```mysql
INSERT INTO `beauty` (
  `id`,
  `name`,
  `sex`,
  `borndate`,
  `phone`,
  `photo`,
  `boyfriend_id`
) 
VALUES
  (
    13,             # int(11)
    '唐艺昕',        # varchar(50)
    '女',           # char(1)
    '1990-4-23',    # datetime
    '18988888888',  # varchar(11)
    NULL,           # 字段写上，值使用null
    2               # int(11)
  ) ;

```

2、==不可以为null的列必须插入值==。可以为null的列如何插入值？

- 方法一：字段写上，值使用null

```mysql
 INSERT INTO beauty(id,NAME,sex,borndate,phone,photo,boyfriend_id)
             VALUES(13,'唐艺昕','女','1990-4-23','1898888888',NULL,2);
```

- 方法二：字段和值都省略

```mysql
INSERT INTO beauty(id,NAME,sex,phone)
    VALUES(15,'娜扎','女','1388888888');
```

3、列的顺序可以调换

```mysql
INSERT INTO `beauty` (`name`,`sex`, `id`, `phone`) 
VALUES
  ('蒋欣', '女', 16, '1488888888') ;
```



4、列数和值的个数**必须**一致

```mysql
INSERT INTO `beauty` (`name`, `sex`, `id`, `phone`) 
VALUES
  ('关晓彤','女',17,'1108888888') ;
```



5、可以省略列名，==默认所有列==，而且列的顺序和表中列的顺序一致

```mysql
INSERT INTO `beauty`  # 默认所有列
VALUES
  (
    18,
    '张飞',
    '男',
    NULL,
    '1198888888',
    NULL,
    NULL
  ) ;
```



### 指定插入

- 语法：`INSERT INTO 表名 SET 列名=值,列名=值,...`
- 不支持多行插入
- 不支持子查询

```mysql
INSERT INTO `beauty` SET `id` = 19,
`name` = '刘涛',
`phone` = '9998888888' ;
```



### 两种方式的区别

1、方式一支持一次插入多行，语法如下：

- `INSERT INTO 表名 [(字段名,..)] VALUES(值,..),(值,...),...;`

2、方式一支持子查询，语法如下：

- `INSERT INTO 表名 查询语句;`



案例1：方式一支持插入多行，==方式二不支持多行插入==。

```mysql
INSERT INTO beauty
VALUES(23,'唐艺昕1','女','1990-4-23','1898888888',NULL,2)
,(24,'唐艺昕2','女','1990-4-23','1898888888',NULL,2)
,(25,'唐艺昕3','女','1990-4-23','1898888888',NULL,2);
```

案例2：方式一支持子查询，方式二不支持

```mysql
INSERT INTO beauty (id, NAME, phone) 
SELECT 
  26,
  '宋茜',
  '11809866' ;

# 再次使用子查询
INSERT INTO beauty (`id`, `name`, `phone`) 
SELECT 
  `id`,
  `boyName`,
  '1234567' 
FROM
  boys 
WHERE id < 3 ;

```

## 修改语句

### 修改单表的记录★

语法：

```mysql
UPDATE 
  表名 
SET
  列 = 新值,
  列 = 新值,
  ...
WHERE 筛选条件 ;
```

案例1：修改beauty表中姓唐的女神的电话为13899888899

```mysql
UPDATE 
  `beauty` 
SET
  `phone` = '13899888899' 
WHERE `name` LIKE '唐%' ;
```

案例2：修改boys表中id号为2的名称为张飞，魅力值 10

```mysql
UPDATE 
  `boys` 
SET
  `boyName` = '张飞',
  `userCP` = 10 
WHERE `id` = 2 ;
```



### 修改多表的记录

#### sql92 语法

- ```mysql
  UPDATE 
    表1 AS 别名,
    表2 AS 别名 
  SET
    列 = 值,
    ...
  WHERE 连接条件   # 内连接
    AND 筛选条件 ;
  ```


#### sql99 语法

- ```mysql
  UPDATE 表1 AS 别名 
    INNER | LEFT |  RIGHT JOIN 表2 别名 
    ON 连接条件 
  SET 列 = 值,...
  WHERE 筛选条件 ;
  ```

案例1：修改张无忌的女朋友的手机号为114

```mysql
UPDATE 
  `beauty` AS be 
  INNER JOIN `boys` AS bo 
    ON be.`boyfriend_id` = bo.`id` 
SET be.`phone` = '115',
    bo.`userCP` = 999 
WHERE bo.`boyName` = '张无忌' ;
```



案例2：修改没有男朋友的女神的男朋友编号都为2号

左外连接

![image-20201230181307342](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210101143802.png)

```mysql
# 左外连接：选取A表中有B表中没有的
SELECT 
  * 
FROM
  `beauty` AS be 
  LEFT JOIN `boys` AS bo 
    ON bo.`id` = be.`boyfriend_id` 
WHERE bo.`boyName` IS NULL ;

# 更新，WHERE要在最后才行
UPDATE 
  `beauty` AS be 
  LEFT JOIN `boys` AS bo 
    ON bo.`id` = be.`boyfriend_id` 
SET be.`boyfriend_id` = 2 
WHERE bo.`boyName` IS NULL ;
```



## 删除语句

### delete

语法：

#### 单表的删除

语法：

```mysql
DELETE 
FROM
  表名 
WHERE 筛选条件 
```



#### 多表的删除

##### sql92语法

```mysql
DELETE 
  表1的别名,
  表2的别名 
FROM
  表1 别名,
  表2 别名 
WHERE 连接条件 
  AND 筛选条件 ;
```

##### sql99语法

```mysql
DELETE 
  表1的别名,
  表2的别名 
FROM
  表1 AS 别名 
  INNER | LEFT | RIGHT JOIN 表2 AS 别名 
  ON 连接条件 
WHERE 筛选条件 ;
```



> 单表的删除

案例1：删除手机号以6结尾的女神信息

```mysql
DELETE 
FROM
  `beauty` 
WHERE `phone` LIKE '%6' ;
```



> 多表的删除

1、删除张无忌的女朋友的信息

```mysql
DELETE 
  be 
FROM
  `beauty` AS be 
  INNER JOIN `boys` AS bo 
    ON be.`boyfriend_id` = bo.`id` 
WHERE bo.`boyName` = '张无忌' ;
```

2、删除黄晓明的信息以及他女朋友的信息

```mysql
DELETE 
  be,
  bo 
FROM
  `beauty` AS be 
  INNER JOIN `boys` AS bo 
    ON bo.`id` = be.`boyfriend_id` 
WHERE bo.`boyName` = '黄晓明' ;
```



### truncate

语法：`TRUNCATE TABLE 表名`;

案例：将魅力值 >100 的男神信息删除

```mysql
TRUNCATE TABLE `boys` ; # 清空数据表
```



### 两种方式的区别

【面试题】★

1. delete可以加where 条件，truncate不能加WHERE条件
2. truncate 删除，效率高一点
3. 假如要删除的表中有自增长列
   -  如果用delete删除后，再插入数据，自增长列的值从**断点开始**
   - 而truncate删除后，再插入数据，自增长列的值**从1开始**。
4. truncate删除没有返回值，**delete删除有返回值**
5. truncate删除不能回滚，**delete删除可以回滚**



## 测试

1、运行以下脚本创建表my_employees

```mysql
CREATE TABLE my_employees (
  Id INT (10),
  First_name VARCHAR (10),
  Last_name VARCHAR (10),
  Userid VARCHAR (10),
  Salary DOUBLE (10, 2)
) ;

CREATE TABLE users (
  id INT,
  userid VARCHAR (10),
  department_id INT
);
```

2、显示表my_employees的结构

```mysql
DESC `my_employees`;
```



3、向my_employees表中插入下列数据

![image-20210101151028962](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210101151037.png)

```mysql
INSERT INTO `my_employees` (
  `Id`,
  `First_name`,
  `Last_name`,
  `Userid`,
  `Salary`
) 
VALUES
  (1,'patel','Ralph','Rpatel',895),
  (2,'Dancs','Betty','Bdancs',860),
  (3,'Biri','Ben','Bbiri',1100),
  (4,'Newman','Chad','Cnewman',750),
  (5,'Ropeburn','Audrey','Aropebur',1550);
```



4、向users表中插入数据

![image-20210101151049991](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210101151050.png)

```mysql
INSERT INTO `users` (`id`,`userid`,`department_id`)
VALUES
  (1,'Rpatel',10),
  (2,'Bdancs',10),
  (3,'Bbiri',20),
  (4,'Cnewman',30),
  (5,'Aropebur',40);
  
# 方式二，联合查询
INSERT INTO `my_employees`
SELECT 
  1,'patel','Ralph','Rpatel',895 
UNION
SELECT 
  2,'Dancs','Betty','Bdancs',860 
UNION
SELECT 
  3,'Biri','Ben','Bbiri',1100 
UNION
SELECT 
  4,'Newman ',' Chad',' Cnewman ',750 
UNION
SELECT 
  5,'Ropeburn','Audrey','Aropebur ',1550;
```



5、将3号员工的 last_name修改为“drelxer"

```mysql
UPDATE 
  `my_employees` 
SET
  `Last_name` = 'drelxer' 
WHERE `Id` = 3 ;
```

6、将所有工资少于900的员工的工资修改为1000

```mysql
UPDATE 
  `my_employees` 
SET
  `Salary` = 1000 
WHERE `Salary` < 900 ;
```

7、将userid为 Bbiri的user表和 my_employees表的记录全部删除

```mysql
DELETE 
  u,
  e 
FROM
  `users` AS u 
  INNER JOIN `my_employees` AS e 
    ON u.`userid` = e.`Userid` 
WHERE u.`userid` = 'Bbiri' ;
```

8、删除所有数据

```mysql
DELETE FROM `my_employees`;
DELETE FROM `users`;
```

9、检查所作的修正

```mysql
SELECT * FROM `my_employees`;
SELECT * FROM `users`;
```

10、清空表my_employees

```mysql
TRUNCATE TABLE `my_employees`;
```