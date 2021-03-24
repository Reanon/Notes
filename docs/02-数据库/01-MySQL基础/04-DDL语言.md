# DDL语言

==数据定义语言（Data Definition Language)==，用于库和表的创建、修改、删除。主要包括

- `CREATE`：创建
- `DROP`：删除
- `ALTER`：修改

## 库的管理

### 创建库

语法：

```mysql
CREATE DATABASE [IF NOT EXISTS 库名] CHARACTER SET 字符集名 ;
```



案例：创建库Books

- 指定字符集：utf8
- 字符集关键字：`CHARACTER SET`

```mysql
CREATE DATABASE IF NOT EXISTS Books CHARACTER SET utf8 ;
```

### 修改库

语法：

```mysql
ALTER DATABASE 库名 CHARACTER SET 字符集名;
```



案例：更改库的字符集

```sql
ALTER DATABASE `books` CHARACTER SET gbk;
```

### 删除库

语法：

```mysql
DROP DATABASE IF EXISTS 库名;
```

案例：库的删除

- `IF EXISTS`

```mysql
DROP DATABASE books;

# 安全 库的删除
DROP DATABASE IF EXISTS books;
```

## 表的管理

### 表的创建 ★

语法：

- 长度和约束是可选的

```mysql
-- 1. # 单行注释
-- 2. /*...*/ 多行注释
CREATE TABLE 表名(
	列名 列的类型 [(长度) 约束],
	列名 列的类型 [(长度) 约束],
	列名 列的类型 [(长度) 约束],
	...
	列名 列的类型 [(长度) 约束]
)[表类型] [字符集类型] [注释]
-- 字符集类型：CHARSET=utf8
```



案例1：创建表Book

```mysql
CREATE TABLE book (
	id INT,             # 编号
	bName VARCHAR(20),  # 图书名
	price DOUBLE,       # 价格
	authorId INT,       # 作者编号
	publishDate DATETIME# 出版日期
);
DESC book;
```

案例2：创建表author

```mysql
CREATE TABLE IF NOT EXISTS author (
  id INT,
  au_name VARCHAR (20),
  nation VARCHAR (10)
) ;
DESC author ;
```

案例3： 创建一个school数据库

- 创建学生表(列,字段)
- 学号int 登录密码varchar(20) 姓名,性别varchar(2),出生日期(datatime),家庭住址,email

详细模板

```mysql
CREATE TABLE `表名`(
   `字段名` 列类型 [属性] [索引] [注释],
   `字段名` 列类型 [属性] [索引] [注释],
   ...
   `字段名` 列类型 [属性] [索引] [注释]
   
)[表类型] [字符集类型] [注释]
```



```mysql
CREATE TABLE IF NOT EXISTS `student` (
  `id` INT(4) NOT NULL AUTO_INCREMENT COMMENT '学号',
  `name` VARCHAR(30) NOT NULL DEFAULT '匿名' COMMENT '姓名',
  `pwd` VARCHAR(20) NOT NULL DEFAULT '123456' COMMENT '密码',
  `sex` VARCHAR(2) NOT NULL DEFAULT '女' COMMENT  '性别',
  `brithday` DATETIME DEFAULT NULL COMMENT '出生日期',
  `address` VARCHAR(100) DEFAULT NULL COMMENT '家庭住址',
  `email` VARCHAR(50) DEFAULT NULL COMMENT '邮箱',
  PRIMARY KEY (`id`)
)ENGINE INNODB DEFAULT CHARSET=utf8
```



### 表的修改

```mysql
ALTER TABLE 表名 
  ADD | DROP | MODIFY / CHANGE COLUMN 列名 [列类型约束] ;
```

- 修改列名：`CHANGE COLUMN`
- 修改列的类型或约束：`MODIFY COLUMN`
- 添加新列：`ADD COLUMN`
- 册除列：`DROP COLUMN`
- 修改表名：`RENAME [TO]`

#### 修改列名

语法：

```mysql
ALTER TABLE 表名 CHANGE COLUMN 旧列名 新列名 类型;
```

案例

```mysql
ALTER TABLE book CHANGE COLUMN `publishDate` pubDate DATETIME;
```



#### 修改列的类型或约束

语法：

```mysql
ALTER TABLE 表名 MODIFY COLUMN 列名 新类型 [新约束];
```

案例

```mysql
ALTER TABLE book MODIFY COLUMN `pubDate` TIMESTAMP ;
```



#### 添加列

语法：

```mysql
ALTER TABLE 表名 ADD COLUMN 列名 类型 [FIRST | AFTER 字段名];
```

案例：添加年薪

```mysql
ALTER TABLE `author` ADD COLUMN annual DOUBLE; 
DESC `author`;
```



#### 删除列

语法：

```mysql
ALTER TABLE 表名 DROP COLUMN 列名;
```

案例

```mysql
ALTER TABLE `author` DROP COLUMN `annual` ;
DESC `author`;
```



#### 修改表名

语法：

```mysql
ALTER TABLE 表名 RENAME [TO] 新表名;
```

案例：修改表名

```mysql
ALTER TABLE `author` RENAME TO book_author;
DESC book_author;

# 改回来
ALTER TABLE book_author RENAME TO author;
DESC author;
```



### 表的删除

```mysql
DROP TABLE [IF EXISTS] 表名;
```



案例1：删除表

```mysql
DROP TABLE IF EXISTS author;
SHOW TABLES;
```



> 新建库的通用的写法

```mysql
DROP DATABASE IF EXISTS 旧库名;
CREATE DATABASE 新库名 ;
```

> 新建表的通用写法 

```mysql
DROP TABLE IF EXISTS 旧表名;
CREATE TABLE  表名();
```

### 复制表

引入插入数据

```mysql
INSERT INTO author 
VALUES
  (1, '村上春树', '日本'),
  (2, '莫言', '中国'),
  (3, '冯唐', '中国'),
  (4, '金庸', '中国') ;
SELECT * FROM author;
```

#### 复制表的结构

```mysql
CREATE TABLE 表名 LIKE 旧表;
```

案例1：仅仅复制表的结构

- 只有字段没有数据

```mysql
CREATE TABLE copy LIKE author;
SHOW TABLES;
SELECT * FROM copy;
```



#### 复制表的结构+数据

```mysql
CREATE TABLE 表名 
SELECT 查询列表 FROM 旧表 [WHERE 筛选];
```

案例2：复制表的结构+数据

```mysql
CREATE TABLE copy2 
SELECT 
  *     # 复制所有数据
FROM
  author ;

# 查看是否复制成功
SHOW TABLES;
SELECT * FROM copy2;
```

#### 复制部分数据

案例3：只复制部分数据

```mysql
CREATE TABLE copy3 
SELECT 
  id,
  au_name 
FROM
  author 
WHERE nation = '中国' ;

SHOW TABLES;
SELECT * FROM copy3;
```

#### 复制部分字段

案例4：仅仅复制某些字段

- 没有数据，只有部分结构（字段）

```mysql
CREATE TABLE copy4 
SELECT 
  id,
  au_name 
FROM
  author 
WHERE 0 ; # 指定无法满足的条件

SHOW TABLES;
SELECT * FROM copy4;
```

### 测试

1、创建表dept1

![image-20210105192915789](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210105192915.png)

```mysql
CREATE TABLE dep1 (id INT (7) ,`name` VARCHAR (25));
DESC dep1;
```

2、将表departments中的数据插入新表dept2中

```mysql
# 插入dept1中
INSERT INTO `dep1` (`id`, `name`) 
SELECT 
  `department_id`,
  `department_name` 
FROM
  `departments` ;
  
# 创建表dep2
CREATE TABLE dep2 
SELECT 
  `department_id`,
  `department_name` 
FROM
  `departments` ;
```

3、创建表emp5

![image-20210105192950019](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210105192950.png)



```mysql
CREATE TABLE emp5 (
  id INT (7),
  First_name VARCHAR (25),
  Last_name VARCHAR (25),
  Dept_id INT (7)
);
DESC emp5;
```

4、将列Last_name的长度增加到50

```mysql
ALTER TABLE emp5 
  MODIFY COLUMN `Last_name` VARCHAR (50) ;
DESC emp5;
```

5、根据表employees创建employees2

```mysql
CREATE TABLE `employees2` 
SELECT 
  * 
FROM
  `employees` ;
DESC `employees2` ;
```

6、删除表emp5

```mysql
DROP TABLE IF EXISTS `emp5`;
```

7、将表employees2重命名为emp5

```mysql
ALTER TABLE `employees2` RENAME TO `emp5`;
```

8、在表dept和 emp5中添加新列test_column，并检查所作的操作

```mysql
ALTER TABLE `emp5` 
  ADD COLUMN test_column INT (2) ;
DESC `emp5`;

ALTER TABLE `dep2`  
  ADD COLUMN test_column INT;  # 默认为INT(11)
DESC `dep2` ;
```

9、直接删除表emp5中的列dept_id

```mysql
ALTER TABLE `emp5` DROP COLUMN `department_id`;
```

## 数据类型

[尚硅谷MySQL数据库全套完整版（sql数据库优化）_哔哩哔哩 (゜-゜)つロ 干杯~-bilibili](https://www.bilibili.com/video/BV1xW411u7ax?p=117)

### 数值型

#### 整型

> 分类

```mysql
TINYINT SMALLINT MEDIUMINT  INT/INTEGER  BIGINT
  1	       2         3	        4	       8
```

| 类型      | 字节  | 有符号值                   | 无符号值           | 无符号时最大取值     |
| --------- | ----- | -------------------------- | ------------------ | -------------------- |
| TINYINT   | 1     | -128 ~ 127                 | 0 ~ 255            | 255                  |
| SMALLINT  | 2     | $-2^{15}$ ~ $2^{15} -1$    | 0 ~ $2^{16} -1$    | 65535                |
| MEDIUMINT | 3     | $-2^{23}$ ~ $2^{23} -1$    | 0 ~ $2^{24} -1$    | 16777215             |
| ==INT==   | **4** | ==$-2^{31}$ ~ $2^{31}-1$== | ==0 ~ $2^{32}-1$== | 4294967295           |
| BIGINT    | 8     | $-2^{63}$ ~ $2^{63}-1$     | 0 ~ $2^{64}-1$     | 18446744073709551616 |

特点：

-  如果不设置无符号还是有符号，默认是有符号，如果想设置无符号，需要添加`UNSIGNED`关键字
- 如果插入的数值超出了整型的范围，会报out of range异常，并且插入临界值
-  如果不设置长度，会有默认的长度；
- 长度代表了显示的最大宽度，如果不够会用0在左边填充，但必须搭配`ZEROFILL`使用！



案例1：如何设置无符号和有符号

- 创建表

```mysql
DROP TABLE IF EXISTS tab_int ;

CREATE TABLE tab_int (
    t1 INT (7) ZEROFILL, # ZEROFILL 默认就是 unsigned
    t2 INT (7) ZEROFILL
) ;

DESC tab_int ;
```

- 插入数据s

```mysql
# Column count doesn't match value...
INSERT INTO tab_int VALUES (-123456) ;

# Out of range ...
INSERT INTO tab_int VALUES (-123456, -123456) ;

# Out of range value ...
INSERT INTO tab_int VALUES (2147483648, 4294967296) ;

INSERT INTO tab_int VALUES (123,123) ;

SELECT * FROM tab_int ;
```

#### 浮点型

> 分类

| 类型名称     | 说明               | 存储需求   |
| ------------ | ------------------ | ---------- |
| FLOAT        | 单精度浮点数       | 4个字节    |
| DOUBLE       | 双精度浮点数       | 8个字节    |
| DECIMAL(M,D) | 压缩的“严格”定点数 | M+2 个字节 |

定点数：

- DEC(M,D)
-  DECIMAL(M,D)

浮点数：

- FLOAT(M,D)：4
- DOUBLE(M,D)：8

特点：

- ==M代表整数部位+小数部位的个数，D代表小数部位==。M和D都可以省略。
  - 如果是decimal，则M默认为10，D默认为0。
  - 如果是float和double，则会根据插入的数值的精度来决定精度
- 如果超出范围，则报out or range异常，并且插入临界值
- 如果==精度要求较高，则优先考虑使用定点数==

原则：

- 所选择的类型越简单越好，能保存数值的类型越小越好



案例1：测试M和D

```mysql
# 测试M和D
DROP TABLE tab_float ;

CREATE TABLE tab_float (f1 FLOAT, f2 DOUBLE, f3 DECIMAL) ;

SELECT * FROM tab_float ;

DESC tab_float ;

INSERT INTO tab_float VALUES(123.4523,123.4523,123.4523);
INSERT INTO tab_float VALUES(123.456,123.456,123.456);
INSERT INTO tab_float VALUES(123.4,123.4,123.4);
INSERT INTO tab_float VALUES(1523.4,1523.4,1523.4);

```



### 字符型

| 类 型             | 大 小                                | 描 述                                                        |
| ----------------- | ------------------------------------ | ------------------------------------------------------------ |
| CAHR(Length)      | Length字节                           | 定长字段，长度为0~255个字符                                  |
| `VARCHAR(Length)` | String 长度+1字节或String 长度+2字节 | 变长字段，长度为0~65 535个字符                               |
| TINYTEXT          | String 长度+1字节                    | 字符串，最大长度为255个字符 2^8 - 1                          |
| TEXT              | String 长度+2字节                    | 字符串，最大长度为65 535个字符：2^16 - 1                     |
| MEDIUMINT         | String 长度+3字节                    | 字符串，最大长度为16 777 215个字符 2^32                      |
| LONGTEXT          | String 长度+4字节                    | 字符串，最大长度为4 294 967 295个字符                        |
| TINYINT(Length)   | 1字节                                | 范围：-128~127，或者0~255（无符号）                          |
| SMALLINT(Length)  | 2字节                                | 范围：-32 768~32 767，或者0~65 535（无符号）                 |
| ENUM              | 1或2字节                             | Enumeration(枚举)的简写，这意味着每一列都可以具有多个可能的值之一 |
| SET               | 1、2、3、4或8字节                    | 与ENUM一样，只不过每一列都可以具有多个可能的值               |

#### 较短的文本

- `CHAR`
- `VARCHAR`

|         |     写法     |               M的意思               |      特点      | 空间的耗费 | 效率 |
| ------- | :----------: | :---------------------------------: | :------------: | ---------- | :--: |
| char    |  `char(M)`   | 最大的**字符数**，可以省略，默认为1 | 固定长度的字符 | 比较耗费   |  高  |
| varchar | `varchar(M)` |    最大的**字符数**，不可以省略     | 可变长度的字符 | 比较节省   |  低  |

- `BINARY` 和 `VARBINARY` 用于保存较短的二进制
- `ENUM` 用于保存枚举
- `SET` 用于保存集合

#### 较长的文本

- `TEXT`
- `BLOB`：较大的二进制



案例1：Enum类型

```mysql
# 枚举类型
CREATE TABLE tab_char(
	c1 ENUM('a','b','c')
);

INSERT INTO tab_char VALUES('a');
INSERT INTO tab_char VALUES('b');
INSERT INTO tab_char VALUES('c');
INSERT INTO tab_char VALUES('m');
INSERT INTO tab_char VALUES('A');

SELECT * FROM tab_char;
```



案例2：Set类型

说明：和Enum类型类似，里面可以保存0~64个成员。

和Enum类型最大的区别是：==SET类型一次可以选取多个成员==，而Enum只能选一个。根据成员个数不同，存储所占的字节也不同。

| 成员数 | 字节数 |
| :----: | :----: |
|  1~8   |   1    |
|  9~16  |   2    |
| 17~24  |   3    |
| 25~32  |   4    |
| 33~64  |   5    |

```mysql
# 集合类型
CREATE TABLE tab_set(
	s1 SET('a','b','c','d')
);

INSERT INTO tab_set VALUES('a');
INSERT INTO tab_set VALUES('A,B');
INSERT INTO tab_set VALUES('a,c,d');

SELECT * FROM tab_set;
```

### 日期型

- 必须用单引号引起来。

> 分类

| 数据类型    | 字节数 | 格式                | 备注                      |
| ----------- | ------ | ------------------- | ------------------------- |
| DATE        | 3      | yyyy-MM-dd          | 存储日期值                |
| TIME        | 3      | HH:mm:ss            | 存储时分秒                |
| YEAR        | 1      | yyyy                | 存储年                    |
| `DATETIME`  | 8      | yyyy-MM-dd HH:mm:ss | 存储日期+时间             |
| `TIMESTAMP` | 4      | yyyy-MM-dd HH:mm:ss | 存储日期+时间，可作时间戳 |

- DATE：只保存日期；
- TIME：只保存时间；
- YEAR：只保存年；
- `DATETIME`：保存日期 + 时间；
- `TIMESTAMP`：保存日期 + 时间；

特点

```sql
			字节		范围			时区等的影响
DATETIME	 8		1000——9999	       不受
TIMESTAMP	 4	    1970-2038	        受
```

案例

```mysql
CREATE TABLE tab_date(
	t1 DATETIME,
	t2 TIMESTAMP
);

INSERT INTO tab_date VALUES(NOW(),NOW());

SELECT * FROM tab_date;
# 查看时区：SYSTEM
SHOW VARIABLES LIKE 'time_zone';

# 更改时区
SET time_zone='+9:00';
```



## 常见的约束

一种限制，用于限制表中的数据，为了保证表中的数据的准确和可靠性。

> 分类

六大约束：

- `NOT NULL`：非空，用于保证该字段的值不能为空；比如姓名、学号等
- `DEFAULT`：默认，用于保证该字段有默认值；比如性别
- `PRIMARY KEY`：主键，用于保证该字段的值具有唯一性，并且非空；比如学号、员工编号等
- `UNIQUE`：唯一，用于保证该字段的值具有唯一性，**可以为空**；比如座位号	
- CHECK：检查约束——【mysql中不支持】；比如年龄、性别
- `FOREIGN KEY`：外键约束，用于限制两个表的关系，用于**保证该字段的值必须来自于主表的关联列的值**，==在从表添加外键约束==，用于引用主表中某列的值；
  - 比如学生表的专业编号，员工表的部门编号，员工表的工种编号



> 添加约束的时机

1、创建表时；

2、修改表时

> 约束的添加分类

1、列级约束：六大约束语法上都支持，但外键约束没有效果

2、表级约束：除了非空、默认；其他的都支持

> 语法

```sql
CREATE TABLE 表名{
	字段名 字段类型 列级约束,
	字段名 字段类型,
	表级约束
};
```

-----

### 创建表时添加约束

```mysql
# 先新建一个库
CREATE DATABASE students;
```

#### 添加列级约束

语法：直接在 「字段名」 和 「类型」后面追加约束类型即可。

- 只支持：默认 `DEFAULT`、非空 `NOT NULL`、主键 `PRIMARY KEY`、唯一 `UNIQUE`

案例

```mysql
USE students;

# 先创建主表
CREATE TABLE major (
  id INT PRIMARY KEY,
  majorName VARCHAR (20)
) ;

DROP TABLE IF EXISTS stuinfo;
CREATE TABLE stuinfo (
  # 主键
  id INT PRIMARY KEY,
  # 非空
  stuName VARCHAR (20) NOT NULL,
  # 检查,这里并不支持
  gender CHAR(1) CHECK (gender = '男' 
    OR gender = '女'),
  # 唯一
  seat INT UNIQUE,
  # 默认约束
  age INT DEFAULT 18,
  # 外键,引用外表major 的 id 字段,这里并不支持
  majorId INT REFERENCES major (id)
) ;
```



查看stuinfo中的所有索引，包括主键、外键、唯一

```mysql
SHOW INDEX FROM stuinfo;
DESC stuinfo;
```



#### 添加表级约束

> 语法

在各个字段的最下面：

```mysql
CONSTRAINT 约束名 约束类型 (字段名) 
```



案例

```mysql
DROP TABLE IF EXISTS stuinfo;
CREATE TABLE stuinfo (
  id INT,
  stuname VARCHAR (20),
  gender CHAR(1),
  seat INT,
  age INT,
  majorid INT,
  
  # 表级约束：CONSTRAINT 约束名 约束类型 (字段名) 
  # 主键
  CONSTRAINT pk PRIMARY KEY (id),
  # 唯一键
  CONSTRAINT uq UNIQUE (seat),
  # 检查，这里无效
  CONSTRAINT ck CHECK (gender = '男' 
    OR gender = '女'),
  # 外键	
  CONSTRAINT fk_stuinfo_major FOREIGN KEY (majorid) REFERENCES major (id) 
) ;

SHOW INDEX FROM stuinfo;
```

#### 通用的写法 ★

```mysql
CREATE TABLE IF NOT EXISTS  表名 (
  字段名 字段类型 NOT NULL,    # 非空
  字段名 字段类型 PRIMARY KEY, # 主键
  字段名 字段类型 UNIQUE,      # 唯一
  字段名 字段类型 DEFAULT 值,  # 默认
  CONSTRAINT 约束名 FOREIGN KEY (字段名) REFERENCES 主表（被引用列）
) ;
```

案例

```mysql
DROP TABLE IF EXISTS stuinfo;

CREATE TABLE IF NOT EXISTS stuinfo (
  # 主键
  id INT PRIMARY KEY,
  # 非空
  stuName VARCHAR (20) NOT NULL,
  gender CHAR(1),
  # 唯一
  seat INT UNIQUE,
  # 默认约束
  age INT DEFAULT 18,
  majorid INT,
  
  # 外键,引用外表major	
  CONSTRAINT fk_stuinfo_major FOREIGN KEY (majorid) REFERENCES major (id) 
) ;

SHOW INDEX FROM stuinfo;
```



> 注意

- 列级约束可以在一个字段上追加多个，中间用空格隔开，没有顺序要求

|          | 支持类型       | 可以起约束名       |
| -------- | -------------- | ------------------ |
| 列级约束 | 除了外键       | 不可以             |
| 表级约束 | 除了非空和默认 | 可以，但对主键无效 |



主键和唯一的区别

-  多个主键组合为一个主键

```mysql
		保证唯一性      是否允许为空    一个表中可以有多少个       是否允许组合
主键		 √				×			 至多有1个            √，但不推荐
唯一		 √			    √			可以有多个            √，但不推荐
```

外键

- 要求在从表设置外键关系
- 从表的外键列的类型和主表的关联列的类型要求一致或兼容，名称无要求
- ==主表的关联列必须是一个key== 
  - key：一般是主键或唯一
- 插入数据时，先插入主表，再插入从表；
- 删除数据时，先删除从表，再删除主表



----

### 修改表时添加约束

> 语法

1、添加列级约束

```mysql
ALTER TABLE 表名 MODIFY COLUMN 字段名 字段类型 新约束;
```

2、添加表级约束

```mysql
ALTER TABLE 表名 ADD [CONSTRAINT 约束名] 约束类型(字段名) [外键的引用];
```

案例

1、创建表

```mysql
DROP TABLE IF EXISTS stuinfo;
CREATE TABLE IF NOT EXISTS stuinfo(
	id INT,
	stuname VARCHAR(20),
	gender CHAR(1),
	seat INT,
	age INT,
	majorid INT
);
DESC stuinfo;
```

#### 添加非空约束

```mysql
ALTER TABLE stuinfo 
  MODIFY COLUMN stuname VARCHAR (20) NOT NULL ;
```

#### 添加默认约束

```mysql
ALTER TABLE stuinfo 
  MODIFY COLUMN age INT DEFAULT 18 ;
```

#### 添加主键

==列级约束==

```mysql
ALTER TABLE stuinfo 
  MODIFY COLUMN id INT PRIMARY KEY ;
```

表级约束

```mysql
ALTER TABLE stuinfo ADD PRIMARY KEY (id) ;
```

#### 添加唯一

列级约束

```mysql
ALTER TABLE stuinfo 
  MODIFY COLUMN seat INT UNIQUE ;
```

表级约束

```mysql
ALTER TABLE stuinfo 
  ADD UNIQUE (seat) ;
```

#### 添加外键

```mysql
ALTER TABLE stuinfo 
  # 注意小括号
  ADD CONSTRAINT fk_stuinfo_major FOREIGN KEY (majorid) REFERENCES major (id);
```



----

### 修改表时删除约束

#### 删除非空约束

```mysql
ALTER TABLE stuinfo MODIFY COLUMN stuname VARCHAR(20) NULL;
```

#### 删除默认约束

```mysql
ALTER TABLE stuinfo 
  MODIFY COLUMN age INT ;
```

#### 删除主键

```mysql
ALTER TABLE stuinfo 
  DROP PRIMARY KEY ;
```

#### 删除唯一

```mysql
# 先查看一下 索引
SHOW INDEX FROM stuinfo ;

#　删除索引
ALTER TABLE stuinfo 
  DROP INDEX seat ;
```

#### 删除外键

```mysql
ALTER TABLE stuinfo 
  DROP FOREIGN KEY fk_stuinfo_major ;
```

#### 测试

１、向表emp2的id列中添加PRIMARY KEY约束（my_emp_id_pk）

```mysql
ALTER TABLE emp2 
  ADD CONSTRAINT my_emp_id_pk PRIMARY KEY (id) ;
```

２、向表dept2的id列中添加PRIMARY KEY约束（my_dept_id_pk）

```mysql
ALTER TABLE dept2 
  ADD CONSTRAINT my_dept_id_pk PRIMARY KEY (id) ;
```

３、向表emp2中添加列dept_id，并在其中定义FOREIGN KEY约束，与之相关联的列是dept2表中的id列。

```mysql
# 添加列
ALTER TABLE emp2 ADD COLUMN dept_id INT;
# 添加外键
ALTER TABLE emp2 
  CONSTRAINT fk_emp2_dept2 FOREIGN KEY (dept_id) REFERENCES dept2 (id) ;
```

### 自增长列(标识列)

`AUTO_INCREMENT`：可以不用手动的插入值，系统提供默认的序列值

特点：

1. 不用手动插入值，可以自动提供序列值，默认从1开始，步长为1
   - 如果要更改起始值：手动插入值

   - 如果要更改步长：更改系统变量 `SET auto_increment_increment=值`；
2. ==一个表至多有一个自增长列==
3. 自增长列只能支持数值型
4. 自增长列必须为一个key

#### 创建表时设置标识列

```mysql
DROP TABLE IF EXISTS tab_identity;

CREATE TABLE tab_identity (
  id INT PRIMARY KEY AUTO_INCREMENT,
  `name` VARCHAR(20)
) ;

# 清空表
TRUNCATE TABLE tab_identity;

INSERT INTO tab_identity(id,NAME) VALUES(NULL,'john');

INSERT INTO tab_identity(`name`) VALUES('john');
```



查看自增长的值

```mysql
SHOW VARIABLES LIKE '%auto_increment%';
```



![image-20210105225233506](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20210105225233.png)

设置步长

```mysql
# 设置步长为3
SET auto_increment_increment=3;
```



### 语法总结

> 修改表时添加或删除约束的具体语法总结

#### 非空

添加非空

```mysql
ALTER TABLE 表名 MODIFY COLUMN 字段名 字段类型 NOT NULL;
```

删除非空

```mysql
ALTER TABLE 表名 MODIFY COLUMN 字段名 字段类型 ;
```

#### 默认

添加默认

```mysql
ALTER TABLE 表名 MODIFY COLUMN 字段名 字段类型 DEFAULT 值;
```

删除默认

```mysql
ALTER TABLE 表名 MODIFY COLUMN 字段名 字段类型 ;
```

#### 主键

添加主键

```mysql
ALTER TABLE 表名 ADD [ CONSTRAINT 约束名] PRIMARY KEY(字段名);
```

删除主键

```mysql
ALTER TABLE 表名 DROP PRIMARY KEY;
```

#### 唯一

添加唯一

```mysql
ALTER TABLE 表名 ADD [CONSTRAINT 约束名] UNIQUE(字段名);
```

删除唯一

```mysql
ALTER TABLE 表名 DROP INDEX 索引名;
```

#### 外键

添加外键

```mysql
ALTER TABLE 表名 ADD [CONSTRAINT 约束名] FOREIGN KEY(字段名) REFERENCES 主表（被引用列）;
```

删除外键

```mysql
ALTER TABLE 表名 DROP FOREIGN KEY 约束名;
```

#### 自增长列

创建表时设置自增长列

```mysql
CREATE TABLE 表(
	字段名 字段类型 约束 AUTO_INCREMENT
);
```

修改表时设置自增长列

```mysql
ALTER TABLE 表名 MODIFY COLUMN 字段名 字段类型 约束 AUTO_INCREMENT;
```

删除自增长列

```mysql
ALTER TABLE 表名 MODIFY COLUMN 字段名 字段类型 约束;
```



### 数据库字段属性

#### UnSigned

- 无符号的
- 声明该数据列**不允许负数** .

#### ZEROFILL

- **0 填充**的
- 不足位数的用0来填充，如INT(3)，5则为005

#### AUTO_INCREMENT

- 自动增长的，每添加一条数据，自动在上一个记录数上加 1(默认)
- **通常用于设置主键 , 且为整数类型**
- 可定义起始值和步长
- 当前表设置步长(`AUTO_INCREMENT=100`)： 只影响当前表
- SET @@auto_increment_increment=5 ; 影响所有使用自增的表(全局)

#### NULL 和 NOT NULL

- 默认为NULL，即没有插入该列的数值
- 如果==设置为NOT NULL，则该列必须有值==

#### DEFAULT

- 默认，用于设置默认值
- 例如：性别字段，默认为"男" ， 否则为 “女” ; 若无指定该列的值 ，则默认值为"男"的值