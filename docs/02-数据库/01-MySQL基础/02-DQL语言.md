# MySQL-DQL语言

```sql
DQL语言的学习    ★              
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
```



## 基础查询

> 语法：`select 查询列表 from 表名`;

类似于System.out.println(打印东西);

> 特点

- 查询列表可以是：表中的字段、常量值、表达式、函数。
- 查询的结果可以是一个虚拟表格。

### 1、查询表中的单个字段

- 字段可以用反引号 ` 包起来。

```mysql
# 指定查询的数据库
USE myemployees;
# 从表中选取字段
SELECT `last_name` FROM employees;
```

### 2、查询表中多个字段

```mysql
SELECT last_name,salary,email FROM employees;
```

### 3、查询表中的所有字段

```mysql
SELECT * FROM employees;
```

### 4、查询常量

- 注意：**字符型**和**日期型**的常量值必须用==单引号==引起来，数值型不需要

```mysql
SELECT 100;

-- 字符型和日期型的常量值必须用单引号引起来
SELECT 'join';
```

### 5、查询函数

- `select 函数名(实参列表);`

```mysql
SELECT VERSION();
```

### 6、查询表达式 

```mysql
SELECT 100%98; -- 2
```

### 7、起别名

- 便于理解
- 如果要查询的字段有重名的情况，使用别名区分

#### 方式一：使用AS

```mysql
SELECT 100%98 AS 结果;

SELECT last_name AS 姓, first_name AS 名 FROM employees;
```

#### 方式二：使用空格

```mysql
SELECT 100%98 结果;
# 原列名[空格]新列名
SELECT last_name 姓, first_name 名 FROM employees;
```

案例1：查询salary，结果显示 out put

- 如果别名中有特殊符号，最好添加上**双引号**

```mysql
SELECT salary AS "out put" FROM employees;
```

### 8、去重 DISTINCT

- 使用关键字：`DISTINCT`

```mysql
SELECT DISTINCT `department_id` FROM employees;
```

### 9、+ 号的作用

mysql中的 +号：==只能作为运算符==

```mysql
# 两个操作数都为数值型,做加法运算
select 100+90; -- 190

# 其中一方为字符型,试图将字符型数值转换为数值型，如果转换成功,则继续做加法运算
select '123+90'; -- 213

# 如果转换失败,则将字符型数值转换成0
select 'john'+90; -- 90

# 只要其中一方为null,则结果肯定为null.
select NULL+123; -- NULL 
```

### 10、拼接 concat 

- 拼接字符：`SELECT CONCAT(字符1，字符2，字符3,...);`

案例：查询员工的名和姓，并显示为姓名

```mysql
SELECT CONCAT(last_name, first_name) AS 姓名 FROM employees;
```

### 11、判空 ifnull 

- `ifnull(v1,v2)`：判断某字段或表达式v1是否为null，如果为null ==返回指定的值==v2，否则返回原本的值v1。

```mysql
# 如果commission_pct为null则返回指定值0
SELECT IFNULL(commission_pct,0)  FROM employees;
```

### 12、isnull函数

- `ISNULL(expr)`：判断某字段或表达式是否为null，如果是NULL，则返回1；否则返回0

```mysql
SELECT ISNULL(NULL); -- 1    
SELECT ISNULL(1);  -- 0
SELECT ISNULL(1 + NULL); -- 1;
SELECT ISNULL(1 / 0 ); -- 1;
```



## 条件查询

语法：`select 查询列表 from 表名 where 筛选条件;`

分类    

```mysql
一、按条件表达式筛选
	条件运算符: > < = != <> >= <= <=>安全等于
二、按逻辑表达式筛选
	逻辑运算符: &&   ||    !
	          and   or   not
	
	&& 和 and 两个条件都为true，结果为true，反之为false
	|| 和 or:只要有一个条件为true，结果为true，反之为false
	! 或 not:如果连接的条件本身为false，结果为true，反之为false	
	
三、模糊查询
	like:一般搭配通配符使用，可以判断字符型或数值型
		通配符：%任意多个字符，_任意单个字符
	like、between and、in、is null
```

### 1、按条件表达式筛选

案例1：查询工资 > 12000的员工信息

```mysql
SELECT * FROM employees WHERE salary > 12000;
```

案例2：查询部门编号不等于90号的员工名和部门编号

```mysql
SELECT 
  `employee_id`,
  `department_id` 
FROM
  employees 
WHERE `department_id` <> 90; # 也可以使用!=
```

### 2、按逻辑表达式筛选

案例1：查询工资在10000到20000之间的员工名、工资及奖金

```mysql
SELECT 
  `first_name`,
  `salary`,
  `commission_pct` 
FROM
  employees 
WHERE `salary` > 10000 && `salary` < 20000 ;
```

案例2：查询部门编号不是在90-110之间，或者 工资高于15000的员工信息

```mysql
SELECT 
  * 
FROM
  employees 
WHERE `department_id` < 90 
  OR `department_id` > 110 
  OR `salary` > 15000 ;

# 另一种写法
FROM
  employees 
WHERE NOT (`department_id` >= 90 AND `department_id` <= 110) 
  OR `salary` > 15000 ;
```

### 3、模糊查询

#### 3.1 like

- `like`：一般搭配通配符使用，可以判断字符型或数值型
- 通配符：
  - `%`：==任意多个字符==
  - `_`：==任意单个字符==
  - `\`：转义字符

案例1：查询员工名中**包含**字符 a 的员工信息

```mysql
SELECT 
  * 
FROM
  employees 
WHERE `last_name` LIKE '%a%' ;
```

案例2：查询员工名中第三个字符为 b，第五个字符为 a 的员工名和工资

```mysql
SELECT 
  `last_name`,
  `salary` 
FROM
  employees 
WHERE `last_name` LIKE '__b_a%' ;
# Tobias 2800.00
```

案例3：查询员工名种第二个字符为_ 的员工名

- 需要使用转义字符： `\`

```mysql
SELECT 
  `last_name` 
FROM
  employees 
  # '-'需要转义为 \_
WHERE `last_name` LIKE '_\_%' ;
```

#### 3.2 between and

注意事项

1. 提高语句简洁度
2. 包含临界值
3. 两个临界值**不能调换顺序**

案例1：查询员工编号在100到120之间的员工信息

```mysql
SELECT 
  * 
FROM
  employees 
WHERE employee_id >= 100 
  AND employee_id <= 120 ;

# 使用 between and 语法
SELECT 
  * 
FROM
  employees 
WHERE `employee_id` BETWEEN 100 AND 120 ;
```

#### 3.3 in

判断某字段的值**是否属于**列表中的某一项

特点:

1. 使用 in 提高语句简洁度
2. in 列表的值类型必须 一致 或 兼容

案例1：查询员工的工种编号是IT_PROG、AD_VP、AD_PRES中的一个员工名和工种编号

```mysql
SELECT 
  `last_name`,
  `job_id` 
FROM
  employees 
WHERE `job_id` = 'IT_PROG' 
  OR `job_id` = 'AD_VP' 
  OR `job_id` = 'AD_PRES' ;
  
# 使用in 
SELECT 
  `last_name`,
  `job_id` 
FROM
  employees 
WHERE `job_id` IN ('IT_PROG', 'AD_VP', 'AD_PRES') ;
```



#### 3.4 is null

> = 或 <> 不能用于判断null值；
>
> is null 或 is not null 可以**判断null值**

案例1：查询没有奖金的员工名和奖金率

```mysql
SELECT 
  `last_name`,
  `commission_pct` 
FROM
  employees 
WHERE `commission_pct` IS NULL ; # 查询commission_pct为null的值
```

#### 3.5  安全等于<=>

案例1：查询没有奖金的员工名和奖金率

```mysql
SELECT 
  `last_name`,
  `commission_pct` 
FROM
  employees 
WHERE `commission_pct` <=> NULL ;
```

案例2：查询工资为12000的员工信息

```mysql
SELECT 
  * 
FROM
  employees 
WHERE `salary` = 12000 ;

# 安全等于
SELECT 
  * 
FROM
  employees 
WHERE `salary` <=> 12000 ;
```

#### 3.6 is null 与 <=>

|         | 普通类型的数值 | null值 | 可读性 |
| :-----: | :------------: | :----: | :----: |
| IS NULL |       ×        |   √    |   √    |
|   <=>   |       √        |   √    |   ×    |

## 排序查询

引入：SELECT * FROM employees;

语法

- `SELECT 查询列表 FROM 表 [WHERE 筛选条件] ORDER BY [ASC/DESC]`

特点：

1. `ASC`代表的是升序（Ascend），`DESC`代表降序（Descend）。不写**默认为升序**
2. order by 子句中可以支持单个字段、多个字段、表达式、函数、别名
3. ==order by 子句一般是放在查询语句的最后面==，`limit子句`除外

### 按单字段排序

案例1：查询员工信息,要求工资从高到低排序

```mysql
SELECT 
  * 
FROM
  employees 
ORDER BY salary DESC ; # 降序
```

案例2：查询部门编号是 >=90，按入职时间的先后进行排序

```mysql
SELECT 
  * 
FROM
  employees 
WHERE `department_id` >= 90  # 筛选条件
ORDER BY `hiredate` ASC ;    # 升序
```

### 按表达式排序

案例3：按年薪的高低显示员工的信息和年薪

- 从高到低也即**降序**

```mysql
SELECT 
  *,
  `salary` * 12 * (1 + IFNULL(`commission_pct`, 0))  AS '年薪'
FROM
  employees 
ORDER BY `salary` * 12 * (1 + IFNULL(`commission_pct`, 0)) DESC ;
```

### 按别名排序

案例4：按年薪的高低显示员工的信息和年薪

```mysql
SELECT 
  *,
  # 修改别名,也可以使用 空格
  `salary` * 12 * (1 + IFNULL(`commission_pct`, 0)) AS 年薪  
FROM
  employees 
ORDER BY `salary` * 12 * (1 + IFNULL(`commission_pct`, 0)) DESC ;
```

### 按函数排序

- `LENGTH(str)`：长度

案例5：按姓名的**长度**显示员工的姓名和工资

```mysql
SELECT 
  LENGTH(`last_name`) AS 字节长度,
  `last_name`,
  `salary` 
FROM
  employees 
ORDER BY LENGTH(`last_name`) DESC ;
```

### 按多个字段排序

- ==只需一个== `ORDER BY` 就可以了

案例6：查询员工信息，要求按工资升序，再按员工编号降序

```mysql
SELECT 
  * 
FROM
  employees 
# 只需要一个ORDER BY
ORDER BY `salary` ASC,  
  `employee_id` DESC ;
```



### 测试

1、查询员工的姓名和部门号和年薪，按年薪降序，按姓名升序

```mysql
SELECT 
  `last_name`,
  `department_id`,
  `salary` * (1 + IFNULL(`commission_pct`, 0)) AS 年薪 
FROM
  employees 
ORDER BY `salary` * (1 + `commission_pct`) DESC,
  `last_name` ASC ;
```



2、选择工资不在8000到17000的员工的姓名和工资，按工资降序

```mysql
SELECT 
  `last_name`,
  `salary` 
FROM
  employees 
WHERE `salary` < 8000  OR `salary` > 17000 
ORDER BY `salary` DESC ;

# between and 
SELECT 
  `last_name`,
  `salary` 
FROM
  employees 
WHERE `salary` NOT BETWEEN 8000 AND 17000 
ORDER BY `salary` DESC ;
```

3、查询邮箱中包含e的员工信息，并先按邮箱的字节数降序，再按部门号升序

```mysql
SELECT 
  * 
FROM
  employees 
WHERE `email` LIKE '%e%' 
ORDER BY LENGTH(`email`) DESC, `department_id` ASC ;
```

## 常见函数

概念：类似于Java的方法，将一组逻辑语句封装在方法体中，对外暴露方法名

优点：

- 隐藏了实现细节 
- 提高了代码的重用性

语法：`select 函数名(实参列表) [from 表]`;

特点：

- 1.叫什么(函数名)

- 2.干什么(函数功能)

分类:
1. 单行函数，如concat、length、ifnull等

2. 分组函数，做统计使用

### 单行函数

单行函数分类：

- 字符函数
- 数学函数
- 日期函数
- 其他函数
- 流程控制函数

> 字符函数具体案例：

#### 1、字符函数

1. `LENGTH(str)`：返回字符串str的长度。
2. `CONCAT(str1,str2,...)`：返回来自于参数连结的字符串。
   - 如果**任一参数是NULL，返回NULL**。
   - 可以有超过2个的参数。一个数字参数被变换为等价的字符串形式。
3. upper(str)：变大写
4. lower(str)：变小写
5. substr 或 `SUBSTRING`(str, pos, len)：从字符串str的起始位置pos返回一个长度为len的子串。
6. INSTR(str, substr)：获取子串第一次出现的索引，找不到返回0。
7. LOCATE(substr, str)：返回子串substr在字符串str第一个出现的位置，注意**子串在前**。
   - 如果substr不是在str里面，**返回0**.
8. TRIM(str)：去前后空格
9. LTRIM(str)：返回删除了其前置空格字符的字符串str。
10. RTRIM(str)：返回删除了其拖后空格字符的字符串str。
11. LEFT(str,len)：返回字符串str的最左面len个字符。
12. RIGHT(str,len)：返回字符串str的最右面len个字符。
13. lpad：用指定的字符实现左填充指定长度
14. rpad：用指定的字符实现右填充指定长度
15. REPLACE(str, from_str, to_str)：返回字符串str其子字符串from_str的所有出现由字符串to_str代替。
16. `INSERT(str,pos,len,newstr)`：返回字符串str在位置pos起始的子串且len个字符长的子串由字符串newstr代替。

##### length

- 获取参数值的**字节值**

```mysql
SELECT LENGTH('subei'); # 5
SELECT LENGTH("鬼谷子"); # 9

# 查看字符集
SHOW VARIABLES LIKE '%char%';
```

##### concat

- 拼接字符串

```mysql
SELECT 
  CONCAT(`last_name`, '_', `first_name`) 
FROM
  employees ;
```

##### upper

- 变大写

```mysql
SELECT UPPER('Tom'); # TOM
```

##### lower

- 变小写

案例：将姓变大写，名变小写，然后拼接

```mysql
SELECT LOWER('Tom'); # tom
```

示例：将姓变大写，名变小写，然后拼接

```mysql
SELECT 
  CONCAT(UPPER(last_name), LOWER(first_name)) 姓名 
FROM
  employees ;
```

##### substr、substring

- 注意：==索引从1开始==

截取从指定所有处后面的**所有**字符

```mysql
SELECT 
  SUBSTR("你好呀，李银河", 2) ; # 好呀，李银河
  
SELECT SUBSTR('吴刚伐桂在天上',4) out_put;   # 桂在天上
```

截取从指定索引处**指定字符长度**的字符

```mysql
SELECT 
  SUBSTR("你好呀，李银河", 1, 4) ; # 你好呀，
  
SELECT SUBSTR('吴刚伐桂在天上',1, 2) out_put; # 吴刚

```

案例：姓名中首字符大写，其他字符小写，然后用_拼接，显示出来

```mysql
SELECT 
  CONCAT(
    # 选取首字符
    UPPER(SUBSTR(`last_name`, 1, 1)),
    # 选取剩余字符
    LOWER(SUBSTR(`last_name`, 2))
  ) AS "name"
FROM
  employees ;
```

##### instr

- 索引从 1 开始
- 获取子串第一次出现的索引，找不到返回0

```mysql
SELECT INSTR("MySQL技术进阶","技术"); # 6

SELECT INSTR("MySQL技术进阶","手段"); # 0
```

##### trim

- 去前后空格
- tab 不是空格。

```mysql
SELECT LENGTH('    霍山    '); # 14

SELECT LENGTH(TRIM('    霍山    ')); # 6

SELECT TRIM('    霍山    '); # 霍山

SELECT TRIM('+' FROM '++++李刚+++刘邦+++') AS out_put; # 李刚+++刘邦
```

##### lpad

- 用指定的字符实现左填充**指定长度**

```mysql
SELECT 
  LPAD('梅林', 8, '+') AS out_put ;   # ++++++梅林
```

##### rpad

- 用指定的字符实现右填充**指定长度**

```mysql
SELECT RPAD('梅林',5,'&') AS out_put; # 梅林&&& 
```

##### replace

- `REPLACE(str,from_str,to_str)`：返回字符串str其子字符串from_str的所有出现由字符串to_str代替。

```mysql
SELECT 
  REPLACE(
    '莉莉伊万斯的青梅竹马是詹姆',
    '詹姆',
    '斯内普'
  ) ; # 莉莉伊万斯的青梅竹马是斯内普
```

#### 2、数学函数

数学函数是MySQL中常用的一类函数。主要用于处理数字，包括整型、浮点数等。数学函数包括绝对值函数、正弦函数、余弦函数、获取随机数的函数等。

- ABS(X)：返回X的绝对值
- `MOD(N,M)`  或 %：返回N被M除的余数。
- `FLOOR(X)`：返回**不大于**X的最大整数值。
- `CEILING(X)`：返回**不小于**X的最小整数值。
- ROUND(X)：返回参数X的**四舍五入**的一个整数。
- `RAND()`：获取随机数，返回 **0-1之间的小数**
- TRUNCATE()：截断

```mysql
# 绝对值
SELECT ABS(-32);  # 32

# 取余
SELECT MOD(15,7); # 1
SELECT 15 % 7;    # 1

# 不大于X的整数
SELECT FLOOR(1.23);    #  1
SELECT FLOOR(-1.23);   # -2

# 不小于X的整数
SELECT CEILING(1.23);  #  2
SELECT CEILING(-1.23); # -1

#1.round:四舍五入
SELECT ROUND(1.23);    #  1
SELECT ROUND(-1.23);   # -1 

SELECT ROUND(1.45);    # 1
SELECT ROUND(1.567,2); # 1.57

#2.ceil:向上取整,返回 >= 该参数的最小整数
SELECT CEIL(1.005);   # 2
SELECT CEIL(-1.002);  # -1

#3.floor:向下取整,返回<=该参数的最大整数
SELECT FLOOR(-9.99);      # -10

#4.truncate:截断
SELECT TRUNCATE(1.65,1);  # 1.6

#5.mod:取余
SELECT MOD(10,3);  # 1

#6.rand:获取随机数，返回0-1之间的小数
SELECT RAND();     # 0.9112224848114163
```



#### 3、日期函数

- `NOW()`：回当前的日期和时间
  - 以‘YYYY-MM-DD HH:MM:SS’ 或YYYYMMDDHHMMSS格式
- `YEAR(date)`：返回date的年份，范围在1000到9999。
- `MONTH(date)`：返回date的月份，范围1到12。
- day(date)：返回日
- ==DATEDIFF==：返回两个日期相差的天数
- `STR_TO_DATE`：将字符通过指定格式转换成日期
- `DATE_FORMAT`：将日期转换成字符
- CURRENT_DATE：以‘YYYY-MM-DD’或YYYYMMDD格式返回今天日期值，取决于函数在一个字符串还是数字上下文被使用。

```mysql
# 1.now:返回当前系统时间+日期
SELECT NOW();  # 2020-12-29 10:57:24

# 2.year:返回年
SELECT YEAR(NOW()); # 2020
SELECT YEAR(hiredate) AS 年 FROM employees;

# 3.month:返回月
# MONTHNAME:以英文形式返回月
SELECT MONTH(NOW());      # 12
SELECT MONTHNAME(NOW());  # December 

# 4.day:返回日
# DATEDIFF:返回两个日期相差的天数
SELECT DAY(NOW());        # 29
SELECT DATEDIFF('2020/06/30','2020/06/21'); # 9

# 5.str_to_date: 将字符通过指定格式转换成日期
# 2020-05-13
SELECT STR_TO_DATE('2020-5-13','%Y-%c-%d') AS out_put; 

# 6.date_format:将日期转换成字符
# 2020年06月06日
SELECT DATE_FORMAT('2020/6/6','%Y年%m月%d日') AS out_put;
# 2020年12月29日
SELECT DATE_FORMAT(NOW(),'%Y年%m月%d日') AS out_put;

#7.curdate:返回当前日期
SELECT CURDATE(); # 2020-12-29

#8.curtime:返回当前时间
SELECT CURTIME(); # 11:00:26
```



#### 4、其他函数

- `VERSION()`：返回数据库的版本号
- USER()：返回当前用户
- `DATABASE()`：返回当前数据库名
- MD5('字符')：返回该字符的md5加密形式
- PASSWORD('字符')：返回该字符的密码形式

```mysql
#version 当前数据库服务器的版本
SELECT VERSION();  # 5.7.19

#database 当前打开的数据库
SELECT DATABASE(); # myemployees

#user当前用户
SELECT USER();     # root@localhost

#password('字符')：返回该字符的密码形式
SELECT PASSWORD('a'); # *667F407DE7C6AD07358FA38DAED7828A72014B4E

#md5('字符'):返回该字符的md5加密形式
SELECT MD5('a');  # 0cc175b9c0f1b6a831c399e269772661
```



#### 5、流程控制函数

##### IF(expr1,expr2,expr3)

- 如果 expr1 是TRUE (expr1 <> 0 and expr1 <> NULL)，则 IF()的返回值为expr2；否则返回值则为 expr3。
  - 只要 expr1 不为0或NULL 都是 TRUE。

IF() 的返回值为数字值或字符串值，具体情况视其所在语境而定。

```mysql
SELECT IF(1>2,2,3);         # 3
SELECT IF(1<2,'yes','no'); # yes 

#1.if函数: if else效果
SELECT IF(10<5,'大','小');  # 小

SELECT 
  `last_name`,
  `commission_pct`,
  IF(
    `commission_pct` IS NULL,
    '没奖金',
    '有奖金'
  ) AS '备注' 
FROM
  employees ;
```

##### STRCMP(expr1,expr2) 

- 如果 str1>str2 返回1，str1=str2 反回0，str1<str2 返回 -1

```mysql
SELECT STRCMP('test','test1'); # -1

# IF只要expr 不为0即为true
SELECT IF(STRCMP('test','test1'),'no','yes'); # no
```

##### CASE 

语法：

```mysql
case 要判断的变量或表达式
when 常量1 then 要显示的值1或语句1
when 常量2 then 要显示的值2或语句2
...
else 要显示的值n或语句n
end
```

- 当“要判断的变量或表达式”省略时，可以当作IF -ELSE语句

```mysql
# 案例:查询员工的工资,要求:
/*
部门号=30,显示的工资为1.1倍
部门号=40,显示的工资为1.2倍
部门号=50,显示的工资为1.3倍
其他部门,显示的工资为原工资
*/
SELECT 
  `salary` AS '原始工资',
  `department_id`,
  CASE
    `department_id` 
    WHEN 30 
    THEN `salary` * 1.1 
    WHEN 40 
    THEN `salary` * 1.2 
    WHEN 50 
    THEN `salary` * 1.3 
    ELSE `salary` 
  END AS '新工资' 
FROM
  employees 
ORDER BY `department_id` ASC ;


# 案例:查询员工的工资的情况
/*
如果工资>20000，显示A级别
如果工资>15000，显示B级别
如果工资>10000，显示C级别
否则，显示D级别
*/
SELECT 
  `salary`,
  CASE
    WHEN `salary` > 20000 
    THEN 'A' 
    WHEN `salary` > 15000 
    THEN 'B' 
    WHEN salary > 10000 
    THEN 'C' 
    ELSE 'D' 
  END AS 工资等级 
FROM
  employees ;
```



#### 测试

１、显示系统时间(注：日期+时间)

```mysql
SELECT NOW(); # 2020-12-29 11:34:57
```

２、查询员工号，姓名，工资，以及工资提高百分之20%后的结果（new salary）

```mysql
SELECT 
  `employee_id`,
  `last_name`,
  `salary`,
  `salary` * 1.2 AS 'new salary' 
FROM
  employees ;
```

３、将员工的姓名按首字母排序，并写出姓名的长度（length）

```mysql
SELECT 
  `last_name`,
  LENGTH(`last_name`) 
FROM
  `employees` 
ORDER BY SUBSTR(`last_name`, 1, 1) ASC ;
```

４、做一个查询，产生下面的结果
　<last_name> earns <salary> monthly but wants <salary*3>

![image-20201229113434089](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20201229113434.png)

```mysql
SELECT 
  CONCAT(
    `last_name`,
    ' earns ',
    `salary`,
    ' monthly but wants ',
    `salary` * 3
  ) AS 'Dream Salary' 
FROM
  employees 
WHERE `salary` = 24000 ;
```



５、使用case-when，按照下面的条件：

```
job 　　　　　　grade
AD_PRES　　　　　 A
ST_MAN　　　　　　B
IT_PROG　　　　　 C
SA_REP           D
ST_CLERK　　　　　E

产生下面的结果
Last_name　　　　Job_id　　　　　Grade
king　　　　　　　AD_PRES　　　　　　A
```



```mysql
SELECT 
  `last_name`,
  `job_id`,
  CASE
    `job_id` 
    WHEN 'AD_PRES' 
    THEN 'A' 
    WHEN 'ST_MAN' 
    THEN 'B' 
    WHEN 'IT_PROG' 
    THEN 'C' 
    WHEN 'SA_REP' 
    THEN 'D' 
    WHEN 'ST_CLERK' 
    THEN 'E' 
    ELSE 'G' 
  END AS 'Grade' 
FROM
  employees 
WHERE `last_name` = 'K_ing' 
  AND `job_id` = 'AD_PRES' 
ORDER BY Grade ;
```



### 分组函数

用作统计使用，又称为**聚合函数**或统计函数或**组函数**

分类：sum 求和、avg 平均值、max 最大值、min最小值、count 计算个数

- `SUM(expression)`：返回指定字段的总和

- `AVG`(expression)：返回一个表达式的平均值，expression 是一个字段
- `MAX`(expression)：返回字段 expression 中的最大值
- `MIN`(expression)：返回字段 expression 中的最小值
- `COUNT(expression)`：返回查询的记录总数，expression 参数是一个**字段**或者 * 号
  - `count(字段)`：统计该字段**非空值**的个数
  - `count(*)`：统计结果集的行数

特点:

sum和avg一般用于处理**数值型**，max、min、count可以处理**任何数据类型**

- 以上分组函数==都忽略NULL== 

- 都可以搭配distinct使用，实现去重的统计
  select sum [distinct 字段] from 表;

- 和分组函数一同查询的字段，要求是 `group by` 后出现的字段  

```mysql
# 1.简单使用
SELECT SUM(salary) FROM employees;  # 691400.00
SELECT AVG(salary) FROM employees;  # 6461.682243
SELECT MAX(salary) FROM employees;  # 24000.00
SELECT MIN(salary) FROM employees;  # 2100
SELECT COUNT(salary) FROM employees;# 107


SELECT 
  SUM(salary) AS 和,
  ROUND(AVG(salary), 2) AS 平均,
  MAX(salary) AS 最高,
  MIN(salary) AS 最低,
  COUNT(salary) AS 个数 
FROM
  employees ;

# 2.参数支持哪些数据类型
# 0,0
SELECT SUM(last_name),AVG(last_name) FROM employees;

# 2148552443000000 20079929373831.7757
SELECT SUM(hiredate),AVG(hiredate) FROM employees;   

# Zlotkey Abel
SELECT MAX(last_name),MIN(last_name) FROM employees;

# 2016-03-03 00:00:00 1992-04-03 00:00:00
SELECT MAX(hiredate),MIN(hiredate) FROM employees;

SELECT COUNT(commission_pct) FROM employees; # 35

SELECT COUNT(last_name) FROM employees;      # 107

# 3.是否忽略null
SELECT 
  SUM(commission_pct),  # 7.80
  AVG(commission_pct)   # 0.222857
FROM
  employees ;

SELECT commission_pct FROM employees;

SELECT 
  SUM(commission_pct),       # 7.80
  AVG(commission_pct),       # 0.222857
  SUM(commission_pct) / 35,  # 0.222857
  AVG(commission_pct) / 107  # 0.0020827770
FROM
  employees ;

SELECT 
  MAX(commission_pct),  # 0.40
  MIN(commission_pct)   # 0.10
FROM 
  employees ;

SELECT 
  COUNT(commission_pct) # 35
FROM
  employees ;

# 4.和distinct搭配
SELECT SUM(DISTINCT salary),SUM(salary) FROM employees;

SELECT COUNT(DISTINCT salary),COUNT(salary) FROM employees;

# 5.count函数详解
SELECT COUNT(salary) FROM employees;  # 107
SELECT COUNT(*) FROM employees;       # 107
SELECT COUNT(1) FROM employees;       # 107

/*
效率上：
MyISAM存储引擎，count(*)最高
InnoDB存储引擎，count(*)和count(1)效率>count(字段)
*/

# 6.和分组函数一同查询的字段有限制
SELECT AVG(salary),employee_id FROM employees; # 1 errors
```

#### 测试

1、查询公司员工工资的最大值，最小值，平均值，总和

```mysql
SELECT 
  MAX(`salary`) AS 'max',
  MIN(`salary`) AS 'min',
  ROUND(AVG(`salary`), 2) AS 'avg',
  SUM(`salary`) AS 'sum' 
FROM
  employees ;
```

2、查询员工表中的最大入职时间和最小入职时间的相差天数 （DIFFRENCE）

```mysql
SELECT 
  # 8735
  DATEDIFF(MAX(`hiredate`), MIN(`hiredate`)) AS 'DIFFRENCE'  
FROM
  employees ;
```

3、查询部门编号为90的员工个数

```mysql
SELECT 
  COUNT(*)  # 3
FROM
  employees 
WHERE `department_id` = 90 ;
```

## 分组查询

### GROUP BY 语句

- 根据一个或多个列对结果集进行分组。
- 在分组的列上我们可以使用 COUNT, SUM, AVG,等函数。

```mysql
# 分组后的字段
SELECT expression1, expression2, ... expression_n,
	   # 聚合函数，例如SUM，COUNT，MIN，MAX或AVG函数
       aggregate_function(aggregate_expression)
FROM tables        # 表
[WHERE conditions] # 筛选条件
# 分组的字段，用逗号隔开
GROUP BY expression1, expression2, ... expression_n
# 分组后的筛选
[having 分组后的筛选]
# 排序列表
[ORDER BY expression [ ASC | DESC ]];
```

注意：查询列表必须特殊，要求是分组函数和group by后出现的字段

特点

1. group by子句支持单个字段分组，多个字段分组，==多个字段之间用逗号隔开没有顺序要求==，表达式或函数(使用较少)	
2. 也可以添加排序，排序放在整个分组查询的最后。

### HAVING语句

- 通常与GROUP BY语句联合使用，用来过滤由GROUP BY语句返回的记录集。
- HAVING语句的存在弥补了WHERE关键字不能与聚合函数联合使用的不足。 
- 通俗的说：==WHERE过滤行，HAVING过滤组==

分组查询中的筛选条件分为两类：

```mysql
			使用关键字	筛选的表	      位置
分组前筛选	where		原始表		   group by的前面
分组后筛选	having		分组后的结果	 group by的后面

1、分组函数做条件肯定是放在having子句中
2、能用分组前筛选的，就优先考虑使用分组前筛选
```



> 案例

引入：查询每个部门的平均工资

```mysql
SELECT AVG(salary) FROM employees;
```

案例1：查询每个工种的最高工资

```mysql
SELECT 
  MAX(`salary`),
  `job_id` 
FROM
  employees 
GROUP BY `job_id` ;
```

案例2：查询每个位置上的部门个数

```mysql
SELECT 
  `location_id`,
  COUNT(*) 
FROM
  departments 
GROUP BY `location_id` ;
```



#### 添加筛选条件

案例1：查询邮箱中包含a字符的，每个部门的平均工资

```mysql
SELECT 
  `department_id`,
  AVG(`salary`) 
FROM
  employees 
WHERE `email` LIKE '%e%'     # 邮箱中包含a字符的
GROUP BY `department_id` ;   # 按部门进行分组
```

案例2：查询有奖金的每个领导手下员工的最高工资

```mysql
SELECT 
  `manager_id`,
  MAX(`salary`) 
FROM
  employees 
WHERE `commission_pct` IS NOT NULL  # 有奖金
GROUP BY `manager_id` ;             # 按领导分组         
```

#### 添加复杂的筛选条件

案例1：查询哪个部门的员工个数 > 2

1.查询每个部门的员工个数

```mysql
SELECT 
  `department_id`,
  COUNT(*) 
FROM
  employees 
GROUP BY `department_id` ;  # 按部门进行分组
```

2.根据1的结果进行筛选，查询哪个部门的员工个数大于2

```mysql
SELECT 
  `department_id`,
  COUNT(*) 
FROM
  employees 
GROUP BY `department_id` 
HAVING COUNT(*) > 2 ;        # 分组后筛选
```

案例2：查询每个工种有奖金的员工的最高工资 > 12000的工种编号和最高工资 

1.查询每个工种有奖金的员工的最高工资 

```mysql
SELECT 
  `job_id`,
  MAX(`salary`) 
FROM
  `employees` 
WHERE `commission_pct` IS NOT NULL 
GROUP BY `job_id` ;      # 按工种进行分组
```

2.根据结果继续筛选，最高工资 > 12000

```mysql
SELECT 
  `job_id`,
  MAX(`salary`) 
FROM
  `employees` 
WHERE `commission_pct` IS NOT NULL 
GROUP BY `job_id` 
HAVING MAX(`salary`) > 12000 ;
```



#### 按表达式或函数分组

案例：按员工姓名的长度分组，查询每一组的员工个数，筛选员工个数 > 5

1.查询每个长度的员工个数 

```mysql
SELECT 
  LENGTH(`last_name`) AS 'Length_name',
  COUNT(*) 
FROM
  employees 
GROUP BY LENGTH(`last_name`) ; 
```

2.添加筛选条件

```mysql
SELECT 
  LENGTH(`last_name`) AS 'Length_name',
  COUNT(*) AS c
FROM
  employees 
GROUP BY LENGTH(`last_name`) 
HAVING c > 5 ;
```



#### 按多个字段查询

案例：查询每个部门每个工种的员工的平均工资

```mysql
SELECT 
  `department_id`,
  `job_id`,
  AVG(`salary`) 
FROM
  employees 
GROUP BY `department_id`, `job_id` ;
```

#### 添加排序

案例：查询每个部门每个工种的员工的平均工资，按平均工资的高低查询

```mysql
SELECT 
  `department_id`,
  `job_id`,
  AVG(`salary`) 
FROM
  employees 
GROUP BY `department_id`,
  `job_id` 
ORDER BY AVG(`salary`) DESC ;
```



#### 测试

1、查询各 job_id 的员工工资的最大值、最小值、平均值、总和，并按job_id升序

```mysql
SELECT 
  `job_id`,
  MAX(`salary`),
  MIN(`salary`),
  AVG(`salary`),
  SUM(`salary`) 
FROM
  employees 
GROUP BY `job_id` 
ORDER BY `job_id` ASC ;
```

2、查询员工最高工资和最低工资的差距（DIFFERENCE）

```mysql
SELECT 
  MAX(`salary`),
  MIN(`salary`),
  MAX(`salary`) - MIN(`salary`) AS 'DIFFERENCE' 
FROM
  employees ;
```

3、查询各个管理者手下员工的最低工资，其中最低工资不能低于6000，没有管理者的员工不计算在内

```mysql
SELECT 
  `manager_id`,
  MIN(`salary`) 
FROM
  employees 
WHERE `manager_id` IS NOT NULL  # 分组前筛选
GROUP BY `manager_id`           # 按照领导分组
HAVING MIN(`salary`) >= 6000    # 分组后筛选
ORDER BY MIN(`salary`) ASC ;
```

4、查询所有部门的编号，员工数量和工资平均值，并按平均工资降序

```mysql
SELECT 
  `department_id`,
  COUNT(*),
  AVG(`salary`) 
FROM
  employees 
GROUP BY `department_id`   # 按部门进行分组
ORDER BY AVG(`salary`) ASC ;
```

5、选择具有各个job_id的员工人数

```mysql
SELECT 
  `job_id`,
  COUNT(*) 
FROM
  employees 
GROUP BY `job_id` ;      # 按工种进行分组
```



## 连接查询

含义：又称多表查询，当查询的数据来自多个表时，就会用到连接查询

- ==笛卡尔乘积==现象：表1 有m行，表2 有n行，结果 = m * n行

- 发生原因：没有有效的连接条件
- 如何避免：**添加有效的连接条件**


> 分类

按年代分类：

- sql92标准
  - 仅仅支持内连接
- sql99标准【推荐】
  - 支持 内连接 + 外连接（左外和右外）+ 交叉连接	

按功能分类：

- 内连接
- 等值连接
- 非等值连接
- 自连接

外连接：

- 左外连接
- 右外连接
- 全外连接

交叉连接

### sql92标准案例

> sql92 标准案例，仅仅 内连接

#### 等值连接

1. 多表等值连接的结果为多表的**交集部分**
2. ==n表连接，至少需要n-1个连接条件==
3. 多表的顺序没有要求
4. 一般需要为表起别名
5. 可以搭配前面介绍的所有子句使用，比如排序、分组、筛选

案例1：查询女神名和对应的男神名

```mysql
SELECT 
  `name`,
  `boyName` 
FROM
  `boys`,
  `beauty` 
WHERE `beauty`.`id` = `boys`.`id` ; # 指定两个表的连接条件
```

案例2：查询员工名和对应的部门名

```mysql
SELECT 
  `last_name`,
  `department_name` 
FROM
  `employees`,
  `departments` 
WHERE `departments`.`department_id` = `employees`.`department_id` ;
```

##### 为表起别名

1. 提高语句的简洁度
2. 区分多个重名的字段

> 注意：如果为表起了别名，则查询的字段就不能使用原来的表名去限定

案例1：查询员工名、工种号、工种名

```mysql
SELECT 
  `last_name`,
  `employees`.`job_id`,
  `job_title` 
FROM
  `employees`,
  `jobs` 
WHERE `employees`.`job_id` = `jobs`.`job_id` ;

# 添加别名
SELECT 
  `last_name`,
  e.`job_id`,
  `job_title` 
FROM
  `employees` AS e, # 给表添加别名
  `jobs` AS j 
WHERE e.`job_id` = e.`job_id` ;
```

##### 两个表的顺序可以调换

案例1：查询员工名、工种号、工种名

```mysql
SELECT 
  e.`last_name`,
  e.`job_id`,
  j.`job_title` 
FROM
  `jobs` AS j,
  `employees` AS e # 给表添加别名
WHERE e.`job_id` = e.`job_id` ;
```

##### 可以加筛选

案例1：查询有奖金的员工名、部门名

```mysql
SELECT 
  e.`last_name`,
  d.`department_name` 
FROM
  `employees` AS e,
  `departments` AS d 
WHERE e.`commission_pct` IS NOT NULL 
  AND e.`department_id` = d.`department_id` ; # 需要使用AND连接，不是使用逗号
```

案例2：查询城市名中第二个字符为o的部门名和城市名

```mysql
SELECT 
  d.`department_name`,
  l.`city` 
FROM
  `departments` AS d,
  `locations` AS l 
WHERE d.`location_id` = l.`location_id` 
  AND l.`city` LIKE '_o%' ; # 城市名中第二个字符为o
```

##### 可以加分组

案例1：查询每个城市的部门个数

```mysql
SELECT 
  l.`city`,
  COUNT(*) 
FROM
  `locations` AS l,
  `departments` AS d 
WHERE d.`location_id` = l.`location_id` 
GROUP BY l.`city` ;
```

案例2：查询有奖金的每个部门的部门名、部门的领导编号和该部门的最低工资

```mysql
SELECT 
  d.`department_name`, # 部门名
  d.`manager_id`,      # 领导编号
  MIN(e.`salary`) AS '最低工资'  # 最低工资
FROM
  `departments` AS d,
  `employees` AS e 
WHERE d.`department_id` = e.`department_id` 
  AND e.`commission_pct` IS NOT NULL 
GROUP BY d.`department_id`;
```

##### 可以加排序

案例1：查询每个工种的工种名和员工的个数，并且按员工个数降序

```mysql
SELECT 
  `job_title`,
  COUNT(*) AS c 
FROM
  `jobs` AS j,
  `employees` AS e 
WHERE j.`job_id` = e.`job_id` 
GROUP BY j.`job_title` 
ORDER BY c DESC ;
```

##### 可以实现三表连接

案例1：查询员工名、部门名和所在的城市

```mysql
SELECT 
  e.`last_name`,
  d.`department_name`,
  l.`city` 
FROM
  `employees` AS e,
  `departments` AS d,
  `locations` AS l 
WHERE e.`department_id` = d.`department_id` 
  AND d.`location_id` = l.`location_id` ;
```



#### 非等值连接

案例1：查询员工的工资和工资级别

![image-20201230141307343](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20201230141307.png)

```mysql
SELECT 
  e.`salary`,
  g.`grade_level` 
FROM
  `employees` AS e,
  `job_grades` AS g 
WHERE e.`salary` BETWEEN g.`lowest_sal`  # 工资在两者之间
  AND g.`highest_sal` ;
```

#### 自连接

- 还是看成两个表

案例1：查询员工名和上级的名称

```mysql
SELECT 
  # e.`employee_id`,
  e.`last_name`,
  #e.`manager_id`,
  m.`last_name` 
FROM
  employees AS e,
  employees AS m 
WHERE e.`manager_id` = m.`employee_id` ;
```

#### 测试

1、显示所有员工的姓名，部门号和部门名称。

```mysql
SELECT 
  e.`last_name`,
  d.`department_id`,
  d.`department_name` 
FROM
  employees AS e,
  departments AS d 
WHERE e.`department_id` = d.`department_id` ;
```

2、查询90号部门员工的job_id和90号部门的 location_id

```mysql
SELECT 
  d.`department_id`,
  e.`job_id`,
  d.`location_id` 
FROM
  `employees` AS e,
  `departments` AS d 
WHERE e.`department_id` = d.`department_id` 
  AND e.`department_id` = 90 ; # 90号部门
```

3、选择所有有奖金的员工的last_name , department_name , location_id , city

```mysql
SELECT 
  e.`last_name`,
  d.`department_name`,
  l.`location_id`,
  l.`city` 
FROM
  `employees` AS e,
  `departments` AS d,
  `locations` AS l 
WHERE e.`department_id` = d.`department_id` 
  AND d.`location_id` = l.`location_id`  
  AND e.`commission_pct` IS NOT NULL ; # 有奖金
```

4、选择city在Toronto工作的员工的last_name , job_id , department_id ,  department_name

```mysql
SELECT 
  e.`last_name`,
  e.`job_id`,
  d.`department_id`,
  d.`department_name`,
  l.`location_id`,
  l.`city` 
FROM
  `employees` AS e,
  `departments` AS d,
  `locations` AS l 
WHERE e.`department_id` = d.`department_id`  # 连接e 和 d
  AND d.`location_id` = l.`location_id`  # 连接d 和 l
  AND l.`city` = 'Toronto' ;
```

5、查询每个工种、每个部门的部门名、工种名和最低工资

```mysql
SELECT 
  j.`job_title`,
  d.`department_name`,
  MIN(e.`salary`) AS '最低工资' 
FROM
  `employees` AS e,
  `departments` AS d,
  `jobs` AS j 
WHERE j.`job_id` = e.`job_id` 
  AND e.`department_id` = d.`department_id` 
GROUP BY d.`department_name`,  # 两个分组
  j.`job_title` ;
```

6、查询每个国家下的部门个数大于2的国家编号

```mysql
SELECT 
  l.`country_id`,
  COUNT(*) AS c 
FROM
  `departments` AS d,
  `locations` AS l 
WHERE d.`location_id` = l.`location_id` 
GROUP BY l.`country_id` 
HAVING c > 2 ; # 在分组之后筛选使用having
```

7、选择指定员工的姓名，员工号，以及他的管理者的姓名和员工号，结果类似于下面的格式

![image-20201230142359917](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20201230142400.png)

```mysql
SELECT 
  e.`last_name`,
  e.`employee_id`,
  m.`last_name`,
  m.`employee_id` 
FROM
  `employees` AS e,
  `employees` AS m 
WHERE e.`manager_id` = m.`employee_id` # 自连接 
  AND e.`last_name` = 'Kochhar' ;
```



### * sql99标准案例

![img](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20201230181015.png)

语法

```sql
SELECT 查询列表
FROM 表1 AS 别名 
[连接类型] JOIN 表2 AS 别名 
ON 连接条件
[WHERE 筛选条件]
[GROUP BY 分组]
[HAVING 筛选条件]
[ORDER BY 排序列表]
```



连接类型分类

内连接（★）: `INNER JOIN`

外连接
- 左外(★): `LEFT [OUTER] JOIN`
- 右外(★): `RIGHT [OUTER] JOIN`
- 全外: `FULL [OUTER] JOIN`

交叉连接: `CROSS JOIN`

> 通过 JOIN 关键字实现连接 

#### 内连接

```mysql
SELECT <select_list>       # 查询列表
FROM  table1 AS A 
INNER JOIN table1 AS  B    # 内连接
ON A.key=B.key;            # 连接条件
```

![image-20201230180845472](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20201230180845.png)

分类

- 等值
- 非等值
- 自连接

特点：

1. 添加排序、分组、筛选
2. INNER可以省略
3. 筛选条件放在where后面，连接条件放在on后面，提高分离性，便于阅读
4. INNER join连接和 sql92 语法中的等值连接效果是一样的，都是==查询多表的交集==

##### 等值连接

案例1：查询员工名、部门名

```mysql
SELECT 
  e.`last_name`,
  d.`department_name` 
FROM
  `employees` AS e 
  INNER JOIN `departments` AS d  # 内连接
  ON e.`department_id` = d.`department_id` ;# 连接条件
   
# 之前写法
SELECT 
  e.`last_name`,
  d.`department_name` 
FROM
  `employees` AS e,
  `departments` AS d 
WHERE e.`department_id` = d.`department_id` ;
```

案例2：查询名字中包含 e 的员工名和工种名（添加筛选）

```mysql
SELECT 
  e.`last_name`,
  j.`job_title` 
FROM
  `employees` AS e 
  INNER JOIN `jobs` AS j 
    ON e.`job_id` = j.`job_id` 
WHERE e.`last_name` LIKE '%e%' ;
```

案例3：查询部门个数 >3 的城市名和部门个数（添加分组+筛选）

- 查询每个城市的部门个数

```mysql
SELECT 
  l.`country_id`,
  COUNT(*) AS '部门个数' 
FROM
  `departments` AS d 
  INNER JOIN `locations` AS l 
    ON d.`location_id` = l.`location_id` 
GROUP BY l.`country_id` ;
```

- 在1结果上筛选满足条件的

```mysql
SELECT 
  l.`country_id`,
  COUNT(*) AS '部门个数' 
FROM
  `departments` AS d 
  INNER JOIN `locations` AS l 
    ON d.`location_id` = l.`location_id` 
GROUP BY l.`country_id` 
HAVING 部门个数 > 3 ; # 再添加筛选条件
```

案例4：查询哪个部门的员工个数 >3 的部门名和员工个数，并按个数降序（添加排序）

- 查询每个部门的员工个数

```mysql
SELECT 
  d.`department_name`,
  COUNT(*) AS '员工个数' 
FROM
  `departments` AS d 
  INNER JOIN `employees` AS e 
    ON e.`department_id` = d.`department_id` 
GROUP BY d.`department_name`; # 按照部门名分组
```

- 在1结果上筛选员工个数>3的记录，并排序

```mysql
SELECT 
  d.`department_name`,
  COUNT(*) AS '员工个数' 
FROM
  `departments` AS d 
  INNER JOIN `employees` AS e 
    ON e.`department_id` = d.`department_id` 
GROUP BY d.`department_name` 
ORDER BY 员工个数 DESC ; # 在结果上筛选员工个数>3的记录，并排序
```

案例5：查询员工名、部门名、工种名，并按部门名降序（添加三表连接）

```mysql
SELECT 
  e.`last_name`,
  d.`department_name`,
  j.`job_title` 
FROM
  `employees` AS e 
  INNER JOIN `departments` AS d 
    ON e.`department_id` = d.`department_id` 
  # 写两次INNER JOIN
  INNER JOIN `jobs` AS j 
    ON e.`job_id` = j.`job_id` 
ORDER BY e.`salary` DESC ;
```

##### 非等值连接

案例1：查询员工的工资级别

```mysql
SELECT 
  e.`last_name`,
  g.`grade_level` 
FROM
  `employees` AS e 
  INNER JOIN `job_grades` AS g 
    ON e.`salary` BETWEEN g.`lowest_sal` 
    AND g.`highest_sal` ;
```

案例2：查询工资级别的个数>20的级别，并且按工资级别降序

```mysql
SELECT 
  g.`grade_level`,
  COUNT(*) 
FROM
  `employees` AS e 
  INNER JOIN `job_grades` AS g 
    ON e.`salary` BETWEEN g.`lowest_sal` 
    AND g.`highest_sal` 
GROUP BY g.`grade_level`   # 按照等级分组
HAVING COUNT(*) > 20 
ORDER BY g.`grade_level` DESC ; # 工资级别降序
```

##### 自连接

案例1：查询员工的名字、上级的名字

```mysql
SELECT 
  e.`last_name`,
  m.`last_name` 
FROM
  `employees` AS e 
  INNER JOIN `employees` AS m 
    ON e.`manager_id` = m.`employee_id` ;
```

案例2：查询姓名中包含字符k的员工的名字、及其上级的名字

```mysql
SELECT 
  e.`last_name`,
  m.`last_name` 
FROM
  `employees` AS e 
  INNER JOIN `employees` AS m 
    ON e.`manager_id` = m.`employee_id` 
WHERE e.`last_name` LIKE '%k%' ;
```



#### 外连接

![image-20201230181041493](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20201230181041.png)

```mysql
SELECT <select_list>
FROM A
LEFT JOIN B
ON A.key=B.key
```

应用场景：==用于查询一个表中有，另一个表没有的记录==

特点：

1. 外连接的查询结果为主表中的所有记录
   - 如果从表中有和它匹配的，则显示匹配的值
   - 如果从表中==没有和它匹配的，则显示null==
   - 外连接查询结果 = 内连接结果 + 主表中有而从表没有的记录
2. 左外连接，`left join` 左边的是主表，left join 是 left outer join 的简写
3. 右外连接，`right join` 右边的是主表，right join 是 right outer join的简写
   - 左外和右外交换两个表的顺序，可以实现同样的效果
4. 全外连接 = 内连接的结果 + 表1中有但表2没有的 + 表2中有但表1没有的



引入：查询男朋友 不在 男神表的的女神名

```mysql
SELECT 
  b.`name`,
  bo.* 
FROM
  beauty AS b  # 女神表是主表
  LEFT OUTER JOIN `boys` AS bo 
    ON b.`boyfriend_id` = bo.`id` 
WHERE bo.`id` IS NULL ;
```

![image-20201230173624298](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20201230173624.png)



案例1：查询哪个部门没有员工

##### 左外连接

![image-20201230181041493](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20201230181041.png)

```mysql
SELECT <select_list>
FROM A
LEFT JOIN B
ON A.key=B.key
```



![image-20201230181307342](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20201230181307.png)

```mysql
SELECT <select_list>
FROM A
LEFT JOIN B
ON A.key=B.key
WHERE B.key IS NULL;
```



案例1：查询哪个部门没有员工

```mysql
SELECT 
  d.*,
  e.`employee_id` 
FROM
  `departments` AS d 
  LEFT OUTER JOIN `employees` AS e 
    ON e.`department_id` = d.`department_id` 
WHERE e.`employee_id` IS NULL ;
```

##### 右外连接

![image-20201230181127309](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20201230181127.png)



```mysql
SELECT <select_list>
FROM A
RIGHT JOIN B
ON A.key=B.key
```



![image-20201230181324290](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20201230181324.png)

```mysql
SELECT <select_list>
FROM A
RIGHT JOIN B
ON A.key=B.key
WHERE A.key IS NULL;
```

案例1：查询哪个部门没有员工

```mysql
SELECT 
  d.*,
  e.`employee_id` 
FROM
  `employees` AS e 
  RIGHT JOIN `departments` AS d 
    ON e.`department_id` = d.`department_id`
    WHERE e.`employee_id` IS NULL;
```

##### 全外连接

![image-20201230181509750](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20201230181509.png)

```mysql
SELECT<select_list>
FROM A
FULL JOIN B 
ON A.key=B.key
```



![image-20201230181519149](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20201230181519.png)



```mysql
SELECT <select_list>
FROM A
FULL JOIN B 
ON A.key=B.key
WHERE A.key IS NULL OR B.key IS NULL; 
```

#### 交叉连接

- 相当于笛卡尔乘积

```mysql
SELECT 
  b.*,
  bo.* 
FROM
  beauty AS b 
  CROSS JOIN boys AS bo ;
```

> sql92和sql99对比

- 功能：sql99 支持 的较多
- 可读性：sql99实现连接条件和筛选条件的分离，可读性较高



#### 测试

1、查询编号 >3 的女神的男朋友信息，如果有则列出详细，如果没有，用nul填充

- LEFT JOIN 如果从表中没有和它匹配的，则**显示null**

![image-20201230181041493](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20201230190130.png)

```mysql
SELECT 
  g.`id`,
  g.`name`,
  b.`boyName` 
FROM
  `beauty` AS g 
  LEFT JOIN `boys` AS b 
    ON g.`boyfriend_id` = b.`id` 
WHERE g.`id` > 3 ;
```

2、查询哪个城市没有部门

- 左外连接

![image-20201230181307342](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20201230190107.png)

```mysql
SELECT 
  `city`,
  `department_name` 
FROM
  `locations` AS l 
  LEFT JOIN `departments` AS d 
    ON l.`location_id` = d.`location_id` 
WHERE d.`department_name` IS NULL ;
```

3、查询部门名为SAL或IT的员工信息

```mysql
SELECT 
  `department_name`,
  `last_name` 
FROM
  `departments` AS d 
  LEFT JOIN `employees` AS e 
    ON d.`department_id` = e.`department_id` 
WHERE d.`department_name` = 'SAL' 
  OR d.`department_name` = 'IT' ;
```

## 子查询

含义：一条查询语句中又嵌套了另一条完整的select语句，其中被嵌套的select语句，称为**子查询**或内查询；在外面的查询语句，称为**主查询**或外查询

特点：

- 子查询都放在小括号内
- 子查询可以放在from后面、select后面、where后面、having后面，但一般==放在条件的右侧==
- **子查询优先于主查询执行**，主查询使用了子查询的执行结果

子查询根据查询结果的行数不同分为以下两类：

**单行子查询**

- 结果集只有一行
- 一般搭配单行操作符使用：> < = <> >= <= 
- 非法使用子查询的情况：
  - 子查询的结果为一组值
  - 子查询的结果为空

**多行子查询**

- 结果集有多行
- 一般搭配多行操作符使用：any、all、in、not in
- in： 属于子查询结果中的任意一个就行
- any和all往往可以用其他查询代替

分类

![15](https://aliyun-typora-img.oss-cn-beijing.aliyuncs.com/imgs/20201230125752.png)

案例

### where 或 having后面

1、**标量 子查询**（单行子查询）
2、**列 子查询**（多行子查询）
3、行 子查询（多列多行）

特点：

1. 子查询放在**小括号内**
2. 子查询一般放在条件的右侧
3. ==标量子查询==，一般搭配着单行操作符（ > < >= <= = <> ）使用
4. ==列子查询==，一般搭配着多行操作符（IN、ANY/SOME、ALL）使用
5. 子查询的执行优先于主查询执行，主查询的条件用到了子查询的结果

#### 标量子查询

- 结果集只有一行一列

案例1：谁的工资比 Abel 高?

1、查询Abel的工资

```mysql
SELECT 
  `salary` 
FROM
  `employees` 
WHERE `last_name` = 'Abel' ;
```



2、查询员工的信息，满足 salary>①结果

```mysql
SELECT 
  * 
FROM
  employees AS e 
WHERE e.`salary` > 
  (SELECT 
    `salary` 
  FROM
    `employees` 
  WHERE `last_name` = 'Abel') ;
```

案例2：返回job_id与141号员工相同，salary比143号员工多的员工姓名、job_id 和工资

1、查询141号员工的job_id

```mysql
SELECT 
  `job_id` 
FROM
  employees 
WHERE `employee_id` = 141 ;
```

2、查询143号员工的salary

```mysql
SELECT 
  `salary` 
FROM
  employees 
WHERE `employee_id` = 143 ;
```

3、查询员工的姓名，job_id 和工资，要求job_id=①并且salary>②

```mysql
SELECT 
  `last_name`,
  `job_id`,
  `salary` 
FROM
  employees 
WHERE `job_id` = 
  (SELECT 
    `job_id` 
  FROM
    employees 
  WHERE `employee_id` = 141) 
  AND `salary` > 
  (SELECT 
    `salary` 
  FROM
    employees 
  WHERE `employee_id` = 143) ;
```

案例3：返回公司工资最少的员工的last_name、job_id和salary

1、查询公司的最低工资

```mysql
SELECT 
  MIN(`salary`) 
FROM
  `employees` ;
```

2、查询last_name,job_id和salary，要求salary=①

```mysql
SELECT 
  `last_name`,
  `job_id`,
  `salary` 
FROM
  employees 
WHERE `salary` = 
  (SELECT 
    MIN(`salary`) 
  FROM
    `employees`) ;
```



案例4：查询最低工资大于50号部门最低工资的部门id和其最低工资

1、查询50号部门的最低工资

```mysql
SELECT 
  MIN(`salary`) 
FROM
  employees 
WHERE `department_id` = 50 ;
```

2、查询每个部门的最低工资

```mysql
SELECT 
  `department_id`,
  MIN(`salary`) 
FROM
  employees 
GROUP BY `department_id` ;
```

3、在②基础上筛选，满足min(salary)>①

```mysql
SELECT 
  `department_id`,
  MIN(`salary`) AS '最低工资' 
FROM
  employees 
GROUP BY `department_id` 
HAVING 最低工资 > 
  (SELECT 
    MIN(`salary`) 
  FROM
    employees 
  WHERE `department_id` = 50) ;
```



> 非法使用标量子查询

1、子查询结果不是一行一列



#### 列子查询

- 结果集只有一列多行

多行操作符

- `IN`/`NOT IN`：等于列表中的**任意一个**
- ANY | SOME：和子查询返回的**某一个**值比较
- ALL：和子查询返回的**所有**值比较



案例1：返回location_id是1400或1700的部门中的所有员工姓名

1、查询location_id是1400或1700的部门编号

结果是一列多行

```mysql
SELECT DISTINCT
  `department_id` 
FROM
  `departments` 
WHERE `location_id` IN (1400, 1700) ;
```

2、查询员工姓名，要求部门号是①列表中的某一个

```mysql
SELECT 
  `last_name` 
FROM
  `employees` 
WHERE `department_id` IN 
  (SELECT DISTINCT 
    `department_id` 
  FROM
    `departments` 
  WHERE `location_id` IN (1400, 1700)) ;
```



解法二：使用左外连接查询

```mysql
SELECT 
  e.`last_name` 
FROM
  `employees` AS e 
  LEFT JOIN `departments` AS d 
    ON e.`department_id` = d.`department_id` 
WHERE d.`location_id` IN (1400, 1700) ;
```

案例2：返回其它工种中比job_id为IT_PROG工种==任一==工资低的员工的员工号、姓名、job_id 以及salary

①、查询job_id为‘IT_PROG’部门任一工资

```mysql
SELECT 
  `salary` 
FROM
  `employees` 
WHERE `job_id` = 'IT_PROG' ;
```

②、查询员工号、姓名、job_id 以及salary，salary<(①)的任意一个

```mysql
SELECT 
  `employee_id`,
  `last_name`,
  `job_id`,
  `salary` 
FROM
  `employees` 
WHERE `salary` < ANY 
  (SELECT 
    `salary` 
  FROM
    `employees` 
  WHERE `job_id` = 'IT_PROG')  
  AND `job_id` <> 'IT_PROG' ; # !=
  
  #  可以使用max 来改写
  SELECT 
  `employee_id`,
  `last_name`,
  `job_id`,
  `salary` 
FROM
  `employees` 
WHERE `salary` < 
  (SELECT 
    MAX(`salary`) 
  FROM
    `employees` 
  WHERE `job_id` = 'IT_PROG') 
  AND `job_id` <> 'IT_PROG' ;
```



案例3：返回其它部门中比job_id为‘IT_PROG’部门==所有==工资都低的员工的员工号、姓名、job_id 以及salary

```mysql
SELECT 
  `employee_id`,
  `last_name`,
  `job_id`,
  `salary` 
FROM
  `employees` 
WHERE `salary` < ALL 
  (SELECT 
    `salary` 
  FROM
    `employees` 
  WHERE `job_id` = 'IT_PROG')  
  AND `job_id` <> 'IT_PROG' ;

# 使用min改写
SELECT 
  `employee_id`,
  `last_name`,
  `job_id`,
  `salary` 
FROM
  `employees` 
WHERE `salary` < 
  (SELECT 
    MIN(`salary`) 
  FROM
    `employees` 
  WHERE `job_id` = 'IT_PROG') 
  AND `job_id` <> 'IT_PROG' ; # 不等于<>, !=
```



#### 行子查询

- ==结果集只有一行多列==
- 使用较少

案例：查询员工编号最小并且工资最高的员工信息

①查询最小的员工编号

```mysql
SELECT 
  MIN(`employee_id`) 
FROM
  `employees` ;
```

②查询最高工资

```mysql
SELECT 
  MAX(`salary`) 
FROM
  `employees` ;
```

③查询员工信息

```mysql
SELECT 
  * 
FROM
  `employees` 
WHERE `employee_id` = 
  (SELECT 
    MIN(`employee_id`) 
  FROM
    `employees`) 
  AND `salary` = 
  (SELECT 
    MAX(`salary`) 
  FROM
    `employees`) ;
```

修改为==行子查询==

```mysql
SELECT 
  * 
FROM
  `employees` 
WHERE (`employee_id`, `salary`) = 
  (SELECT 
    MIN(`employee_id`),
    MAX(`salary`) 
  FROM
    `employees`);
```



### select后面

- 仅仅支持**标量子查询**（一行一列）

案例：查询每个部门的员工个数

```mysql
SELECT 
  d.*,
  (SELECT 
    COUNT(*) 
  FROM
    `employees` AS e 
  WHERE e.`department_id` = d.`department_id`) AS '个数' 
FROM
  `departments` AS d ;
  
# 左连接查询
SELECT 
  d.*,
  COUNT(*) 
FROM
  `departments` AS d 
  LEFT JOIN `employees` AS e 
    ON d.`department_id` = e.`department_id` 
GROUP BY d.`department_id` ;
```

案例2：查询员工号=102的部门名

```mysql

SELECT 
  d.`department_name`
FROM
  `departments` AS d 
  LEFT JOIN `employees` AS e 
    ON e.`department_id` = d.`department_id` 
WHERE e.`employee_id` = 102 ;
```

### from后面

- 将子查询结果充当一张表，==要求必须起别名==

案例：查询每个部门的平均工资的工资等级

①查询每个部门的平均工资

```mysql
SELECT 
  d.`department_id`,
  AVG(`salary`) AS '平均工资'
FROM
  `departments` AS d 
  LEFT JOIN `employees` AS e 
    ON d.`department_id` = e.`department_id` 
GROUP BY d.`department_id` ;
```



②连接①的结果集和job_grades表，筛选条件平均工资 between lowest_sal and highest_sal

```mysql
SELECT 
  t.`department_id`,
  t.asgs,
  g.`grade_level` 
FROM
  (SELECT 
    d.`department_id`,
    AVG(`salary`) AS asgs 
  FROM
    `departments` AS d 
    LEFT JOIN `employees` AS e 
      ON d.`department_id` = e.`department_id` 
  GROUP BY d.`department_id`) AS t  # 起别名
  INNER JOIN `job_grades` AS g 
    ON t.asgs BETWEEN g.`lowest_sal` 
    AND g.`highest_sal` ;
```



### exists后面

- 相关子查询

语法：

- `exists`(完整的查询语句)：结果为0或1
- 该语法可以理解为，将主查询的数据，放到子查询中做条件验证，根据验证结果(TRUE或FALSE）来决定主查询的数据结果是否得以保留。

案例1：查询有员工的部门名

```mysql
SELECT 
  `department_name` 
FROM
  `departments` AS d 
WHERE EXISTS 
  (SELECT 
    * 
  FROM
    `employees` AS e 
  WHERE d.`department_id` = e.`department_id`);
  
# 使用IN关键字改写
SELECT 
  `department_name` 
FROM
  `departments` AS d 
WHERE d.`department_id` IN 
  (SELECT 
    `department_id` 
  FROM
    `employees` 
  WHERE `department_id` IS NOT NULL) ;
```

案例2：查询没有女朋友的男神信息

```mysql
#exists
SELECT 
  b.* 
FROM
  `boys` AS b 
WHERE NOT EXISTS 
  (SELECT 
    `boyfriend_id` 
  FROM
    `beauty` AS g 
  WHERE g.`boyfriend_id` = b.`id`) ;
  
# IN
SELECT 
  b.* 
FROM
  `boys` AS b 
WHERE b.`id` NOT IN 
  (SELECT 
    `boyfriend_id` 
  FROM
    `beauty` AS g 
  WHERE g.`boyfriend_id` = b.`id`);
```



### 测试一

1、查询和Zlotkey相同部门的员工姓名和工资

```mysql
SELECT 
  `last_name`,
  `salary` 
FROM
  `employees` 
WHERE `department_id` = 
  (SELECT 
    # 查询Zlotkey的部门
    `department_id` 
  FROM
    `employees` 
  WHERE `last_name` = 'Zlotkey') ;
```



2、查询工资比公司平均工资高的员工的员工号，姓名和工资。

```mysql
SELECT 
  `employee_id`,
  `last_name`,
  `salary` 
FROM
  `employees` 
WHERE `salary` > 
  (SELECT 
    AVG(`salary`)  # 平均工资6461.682243
  FROM
    `employees`) ;
```



3、查询各部门中工资比本部门平均工资高的员工的员工号，姓名和工资

```mysql
SELECT 
  e.`employee_id`,
  e.`department_id`,
  e.`last_name`,  
  e.`salary`,	     # 员工工资
  v.avgs             # 本部门平均工资
FROM
  `employees` AS e 
  INNER JOIN 
    (SELECT 
      `department_id`,
      AVG(`salary`) AS 'avgs' 
    FROM
      `employees` 
    GROUP BY `department_id`) AS v 
    ON v.`department_id` = e.`department_id` 
WHERE e.`salary` > v.avgs ;
```



4、查询和姓名中包含字母u的员工在相同部门的员工的员工号和姓名

```mysql
SELECT 
  `employee_id`,
  `last_name` 
FROM
  `employees` AS e 
WHERE e.`department_id` IN 
  (SELECT DISTINCT 
    `department_id` # 姓名中包含字母u的员工的部门
  FROM
    `employees` 
  WHERE `last_name` LIKE '%u%') ;
```



5、查询在部门的 location_id为1700的部门工作的员工的员工号

```mysql
SELECT 
  `employee_id`,
  `last_name` 
FROM
  `employees`  AS e
WHERE e.`department_id` IN 
  (SELECT 
    d.`department_id`  # 一组部门
  FROM
    `departments` AS d 
  WHERE d.`location_id` = 1700) ;
```



6、查询管理者是K_ing 的员工姓名和工资

```mysql
SELECT 
  e.`last_name`,
  e.`salary` 
FROM
  `employees` AS e 
WHERE e.`manager_id` IN   # 两个叫做K_ing的管理者
  (SELECT 
    m.`employee_id` 
  FROM
    `employees` AS m 
  WHERE m.`last_name` = 'K_ing');
```



7、查询工资最高的员工的姓名，要求first_name和last_name显示为一列，列名为姓.名

```mysql
SELECT 
  CONCAT(`last_name`, '.', `first_name`) AS 姓名 
FROM
  `employees` 
WHERE `salary` = 
  (SELECT 
    MAX(`salary`) 
  FROM
    `employees`) ;
```



### *测试二

作业待完成

> [尚硅谷MySQL数据库全套完整版  98-99）](https://www.bilibili.com/video/BV1xW411u7ax?p=98)

1、查询工资最低的员工信息：last_name, salary

```mysql

```

2、查询平均工资最低的部门信息

```mysql

```

3、查询平均工资最低的部门信息和该部门的平均工资

```mysql

```

4、查询平均工资最高的 job 信息

```mysql

```

5、查询平均工资高于公司平均工资的部门有哪些?

```mysql

```

6、查询出公司中所有manager的详细信息

```mysql

```

7、各个部门中最高工资中最低的那个部门的最低工资是多少

```mysql

```

8、查询平均工资最高的部门的 manager的详细信息：last_name，department_id，email，salary

```mysql

```

## 分页查询

应用场景：当要显示的数据，一页显示不全，需要**分页**提交sql请求。

语法：

```mysql
SELECT 查询列表
FROM 表
[JOIN TYPE JOIN 表2
ON 连接条件
WHERE 筛选条件
GROUP BY 分组字段
HAVING 分组后的筛选
ORDER BY 排序的字段]
LIMIT [OFFSET,] size;   # 显示的页数
```

注意

- offset：要显示条目的起始索引，默认起始索引从0开始
- size：要显示的条目个数

特点：

1. limit语句==放在查询语句的最后==

2. 公式

   ```mysql
   SELECT 查询列表 FROM 表
   LIMIT (page-1) * size,size;
   ```

   - page：要显示的页数 ，size：每页的条目数

```sql
size=10
page      offset
1	         0
2  	        10
3	        20
```

案例1：查询前五条员工信息

```mysql
SELECT 
  * 
FROM
  `employees` 
LIMIT 0, 5 ;

# 省略起始索引0
SELECT 
  * 
FROM
  `employees` 
LIMIT 5 ; 
```

案例2：查询第11条——第25条

- offet = 11 - 1 =10
- size = 25 - 11 + 1 = 15

```mysql
SELECT 
  * 
FROM
  `employees` 
LIMIT 10, 15;  # offset= begin - 1, size = end - begin + 1 
```



案例3：有奖金的员工信息，并且工资较高的前10名显示出来

```mysql
SELECT 
  * 
FROM
  `employees` 
WHERE `commission_pct` IS NOT NULL 
ORDER BY `salary` DESC 
LIMIT 0, 10 ; # LIMIT 10
```



### 测试

```
已知表student
id  学号
name 姓名 
email 邮箱   john@126.com
gradeId   年级编号
sex 性别  男女 
age

表grade
id  年级编号
gradeName 年级名称
```

1、查询所有学员的邮箱的用户名(注：邮箱中@前面的字符)

```mysql
SELECT 
  # LOCATE(substr,str)
  LOCATE('@', `email`),
  # INSTR(str,substr)
  INSTR(email, '@'),
  SUBSTRING(`email`, 1, INSTR(email, '@') - 1) AS 用户名 
FROM
  `student` ;
```



2、查询男生和女生的个数

```mysql
SELECT 
  `sex`,
  COUNT(*) AS '个数' 
FROM
  `student` 
GROUP BY `sex` ;
```

3、查询年龄>18岁的所有学生的姓名和年级名称

```mysql
SELECT 
  s.`studentname`,
  g.`gradename` 
FROM
  `student` AS s 
  INNER JOIN `grade` AS g 
    ON s.`gradeid` = g.`gradeid` 
WHERE `age` > 18 ;
```

4、查询哪个年级的学生最小年龄 > 17岁

```mysql
SELECT 
  `gradeid`,
  MIN(`age`) AS min_age 
FROM
  `student` 
GROUP BY `gradeid` 
HAVING min_age > 17 ;
```

5、试说出查询语句中涉及到的所有的关键字，以及执行先后顺序

```mysql
SELECT 查询列表                 ⑦          
FROM 表1    		              ①
[LEFT INNER RIGHT] JOIN 表2    ②
ON 连接条件                     ③
WHERE 筛选条件                  ④
GROUP BY 分组字段               ⑤
HAVING 分组后的筛选              ⑥
ORDER BY 排序字段               ⑧
LIMIT 偏移,条目数                ⑨
```

## 联合查询

含义：union (联合、合并)

- 将多条查询语句的结果合并成一个结果。

语法

```sql
查询语句1
UNION [ALL]
查询语句2
UNION [ALL]
...
```

意义

- 将一条比较复杂的查询语句拆分成多条语句
- 适用于查询多个表的时候，查询的列基本是一致。

特点

- 要求**多条查询语句的查询列数是一致的**！
- 要求多条查询语句的查询的**每一列的类型和顺序最好一致**
- ==union关键字默认去重==，如果使用`UNION ALL` 可以包含重复项



引入案例：查询部门编号>90 或 邮箱包含a的员工信息

```mysql
SELECT 
  * 
FROM
  `employees` 
WHERE `email` LIKE "%a%" 
  OR `department_id` > 90 ;
  
# 联合查询
SELECT 
  * 
FROM
  `employees` 
WHERE `email` LIKE "%a%" 
UNION  # 添加一个union
SELECT 
  * 
FROM
  `employees` 
WHERE `department_id` > 90 ;
```



案例1：查询中国用户中男性的信息以及外国用户中年男性的用户信息

```mysql
SELECT id,cname,csex FROM t_ca WHERE csex='男'
UNION
SELECT t_id,tName,tGender FROM t_ua WHERE tGender='male';
```



---

此处需要的数据库源文件

```sql
/*
 Navicat MySQL Data Transfer

 Source Server         : localhost
 Source Server Type    : MySQL
 Source Server Version : 50145
 Source Host           : localhost:3306
 Source Schema         : test

 Target Server Type    : MySQL
 Target Server Version : 50145
 File Encoding         : 65001

 Date: 03/07/2020 11:08:32
*/

SET NAMES utf8mb4;
SET FOREIGN_KEY_CHECKS = 0;

-- ----------------------------
-- Table structure for t_ca
-- ----------------------------
DROP TABLE IF EXISTS `t_ca`;
CREATE TABLE `t_ca`  (
  `id` int(20) NOT NULL,
  `cname` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `csex` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Compact;

-- ----------------------------
-- Records of t_ca
-- ----------------------------
INSERT INTO `t_ca` VALUES (1, '韩梅梅', '女');
INSERT INTO `t_ca` VALUES (2, '李雷', '男');
INSERT INTO `t_ca` VALUES (3, '李明', '男');

SET FOREIGN_KEY_CHECKS = 1;


/*
 Navicat MySQL Data Transfer

 Source Server         : localhost
 Source Server Type    : MySQL
 Source Server Version : 50145
 Source Host           : localhost:3306
 Source Schema         : test

 Target Server Type    : MySQL
 Target Server Version : 50145
 File Encoding         : 65001

 Date: 03/07/2020 11:09:05
*/

SET NAMES utf8mb4;
SET FOREIGN_KEY_CHECKS = 0;

-- ----------------------------
-- Table structure for t_ua
-- ----------------------------
DROP TABLE IF EXISTS `t_ua`;
CREATE TABLE `t_ua`  (
  `t_id` int(11) NOT NULL,
  `tName` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `tGender` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  PRIMARY KEY (`t_id`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Compact;

-- ----------------------------
-- Records of t_ua
-- ----------------------------
INSERT INTO `t_ua` VALUES (1, 'john', 'male');
INSERT INTO `t_ua` VALUES (2, 'lucy', 'female');
INSERT INTO `t_ua` VALUES (3, 'lily', 'female');
INSERT INTO `t_ua` VALUES (4, 'jack', 'male');
INSERT INTO `t_ua` VALUES (5, 'rose', 'female');

SET FOREIGN_KEY_CHECKS = 1;
```

