# TCL语言

事务控制语言（Transaction Control Language)，用于维护数据的一致性，包括：

- `COMMIT`：
- `ROLLBACK`：
- `SAVEPOINT`：

## 事务

==事务由单独单元的一个或多个SQL语句组成，在这个单元中，每个MySQL语句是相互依赖的==。

- 而整个单独单元作为一个不可分割的整体，如果单元中某条SQL语句一旦执行失败或产生错误，整个单元将会**回滚**：所有受到影响的数据将返回到事物开始以前的状态；
- 如果单元中的所有SQL语句均执行成功，则事务被顺利执行。

>事务是一个或一组sql语句组成一个**执行单元**，这个执行单元要么全部执行，要么全部不执行。



案例1：余额都为1000的张三丰向郭襄转账500

```mysql
/*
初始状态：
    张三丰   1000
    郭襄     1000
转账成功：
    张三丰   500
    郭襄     1500
意外状态：
    张三丰   500
    郭襄     1000
*/
UPDATE 表 SET 张三丰的余额=500 WHERE NAME='张三丰';
# 意外发生
UPDATE 表 SET 郭襄的余额=1500 WHERE NAME='郭襄';
```

### 存储引擎

概念：在mysql中的数据用各种不同的技术存储在文件（或内存）中。

- 查看mysql支持的存储引擎：`SHOW ENGINES;` 

#### 存储引擎分类

在mysql中用的最多的存储引擎有：INNODB、MYISAM、MEMORY等。

- 其中==INNOD支持事务==，默认。
- 而MYISAM、MEMORY等不支持事务



### 事务的特性(ACID)

#### 原子性 Atomicity

原子性是指==事务是一个不可分割的工作单位==，事务中的操作要么都发生，要么都不发生。

#### 一致性 Consistency

一个事务执行会使数据从一个==一致状态切换到另外一个一致状态==。

#### 隔离性 Isolation

事务的隔离性是指一个事务的执行不能被其他事务干扰，即一个事务内部的操作及使用的数据对并发的其他事务是隔离的；==并发执行的各个事务之间不能互相干扰==。

#### 持久性 Durability

持久性是指一个事务一旦被提交，它对==数据库中数据的改变就是永久性==的，接下来的其他操作和数据库故障不应该对其有任何影响。

### 事务的使用步骤 ★

隐式（自动）事务

- 没有明显的开启和结束，本身就是一条事务可以自动提交，比如INSERT、UPDATE、DELETE

显式事务

- 事务具有明显的开启和结束的标记；
- 前提：必须先设置自动提交功能为禁用。

```sql
开启事务的语句;
update 表 set 张三丰的余额=500 where name='张三丰'

update 表 set 郭襄的余额=1500 where name='郭襄' 
结束事务的语句;
```

> 具体步骤

步骤1：开启事务

```mysql
# 查看是否设置了自动提交
SHOW VARIABLES LIKE 'autocommit' ;

#　设置自动提交功能为禁用,默认开启事务
SET autocommit=0;

# 开启事务，可以不加, 可选的
START TRANSACTION; 
```

步骤2：编写事务中的sql语句(SELECT、INSERT、UPDATE、DELETE)

```mysql
语句1;
语句2;
...
```

步骤3：结束事务

```mysql
# 提交事务
COMMIT; 

#　回滚事务
ROLLBACK; 
```

> 案例：演示事务的使用步骤

1、创建表

```mysql
SHOW VARIABLES LIKE 'autocommit';
SHOW ENGINES;

#　创建表
DROP TABLE IF EXISTS account ;

CREATE TABLE account (
  id INT PRIMARY KEY AUTO_INCREMENT,
  username VARCHAR (20),
  balance DOUBLE
) ;
# 插入数据
INSERT INTO account (`username`, balance) 
VALUES
  ('john', 1000),
  ('Tom', 1000) ;
```

2、开启事务

```mysql
SET autocommit=0;
START TRANSACTION;
```

3、编写一组事务的语句

```mysql
UPDATE account SET balance = 1000 WHERE username='John';
UPDATE account SET balance = 1000 WHERE username='Tom';
```



4、结束事务

```mysql
# 提交事务
COMMIT;
# 回滚事务
ROLLBACK;

SELECT * FROM account;
```

## 并发事务

### 并发问题

多个事务同时操作==同一个数据库的相同数据==时，如果没有采取必要的隔离机制，就会导致各种并发问题。

> 并发问题分类

#### 脏读

对于两个事务T1、 T2；T1 读取了已经被T2 ==更新但还没有被提交==的字段。之后，若T2 回滚，T1读取的内容就是临时且无效。

#### 不可重复读

对于两个事务T1、T2；T1 读取了一个字段，然后T2 ==更新==了该字段。 之后，T1再次读取同一个字段，值就不同了。

- 即，一个事务多次读取，结果不一样；

#### 幻读

对于两个事务T1、T2； T1 从一个表中读取了一个字段，然后T2 在该表中==插入==了一些新的行。之后，如果T1 再次读取同一个表，就会多出几行。

- 一个事务读取了其他事务还没有提交的数据，只是读到的是 其他事务「插入」的数据。



### 隔离级别

可以通过设置**隔离级别**来解决并发问题

- ==数据库事务的隔离性==：数据库系统必须具有隔离并发运行各个事务的能力， 使它们不会相互影响， 避免各种并发问题。
- 一个事务与其他事务隔离的程度称为隔离级别。数据库规定了多种事务隔离级别，不同隔离级别对应不同的干扰程度，隔离级别越高，数据一致性就越好，但并发性越弱。

#### 读未提交数据

`READ UNCOMMITTED`：允许事务读取未被其他事物提交的变更。脏读、不可重复读和幻读的问题都会出现

#### 读已提交

`READ COMMITTED`：只允许事务读取已经被其它事务提交的变更。可以避免脏读，但不可重复读和幻读问题仍然可能出现。

#### 可重复读

`REPEATABLE READ`：确保事务可以多次从一个字段中读取相同的值。在这个==事务持续期间，禁止其他事物对这个字段进行更新==。可以避免脏读和不可重复读，但幻读的问题仍然存在。

#### 串行化

`SERIALIZABLE`：确保事务可以从一个表中读取相同的行。在这个事务持续期间，禁止其他事务对该表执行插入。更新和删除操作。==所有并发问题都可以避免，但性能十分低下==。



|                    |          | 脏读 | 不可重复读 | 幻读 |
| :----------------: | :------: | :--: | :--------: | :--: |
| `READ UNCOMMITTED` | 读未提交 |  ×   |     ×      |  ×   |
|  `READ COMMITTED`  | 读已提交 |  √   |     ×      |  ×   |
| `REPEATABLE READ`  | 可重复读 |  √   |     √      |  ×   |
|   `SERIALIZABLE`   |  串行化  |  √   |     √      |  √   |

- Oracle 支持的2 种事务隔离级别：READ COMMITED、 SERIALIZABLE。Oracle 默认的事务隔离级别为: `READ COMMITED` 
- Mysql 支持4 种事务隔离级别；Mysql 默认的事务隔离级别为：`REPEATABLE READ` 

#### 设置隔离级别

每启动一个mysql 程序， 就会获得一个单独的数据库连接。 每个数据库连接都有一个全局变量`@@tx_isolation`， 表示当前的事务隔离级别。

> 案例

1、查看隔离级别

```mysql
SELECT @@tx_isolation; # SELECT @@tx_isolation;
```



2、设置隔离级别

```mysql
# 设置当前mySQL 连接的隔离级别
SET SESSION TRANSACTION ISOLATION LEVEL READ COMMITTED;

#　设置数据库系统的全局的隔离级别
SET GLOBAL TRANSACTION ISOLATION LEVEL READ COMMITTED;
```

3、演示事务对于 DELETE 和 TRUNCATE 的处理的区别

- DELETE 可以成功回滚

```mysql
# 开启事务
SET autocommit=0;
START TRANSACTION;
# 删除表
DELETE FROM `dep1`;
# 回滚
ROLLBACK;

SELECT * FROM `dep1`;
```

- TRUNCATE 无法回滚

```mysql
# 开启事务
SET autocommit=0;
START TRANSACTION;
# 删除表
TRUNCATE TABLE `dep1`;
# 回滚
ROLLBACK;

SELECT * FROM `dep1`;
```



### 回滚点

- 设置保存点：`SAVEPOINT 节点名`;

- 回滚到保存点：`ROLLBACK TO 节点名`;



> 案例：演示savepoint 的使用

```mysql
SET autocommit=0;
START TRANSACTION;


DELETE FROM account WHERE id=25;
# #设置保存点
SAVEPOINT a; 
DELETE FROM account WHERE id=28;

# 回滚到保存点
ROLLBACK TO a;

SELECT * FROM account;
```

