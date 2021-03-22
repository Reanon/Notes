<style>
    h2
    {
      margin-bottom:50px;
      font-size: 1em;
        }
h2 span{
  display:inline-block;
  background: rgb(102, 126, 233);
  color:#ffffff !important;
  padding:  10px  16px;
  border-radius:5px;
  box-shadow: 2px 2px 5px rgb(216, 216, 216);
}
/* .content{
  width:1000px;
  margin: 0 auto;
  padding-top: 30px;
} */
.markdown-section{
  padding: 30px 30px 40px 30px !important;
}
    </style>

💡 **「关于」**

- 📚 学习是一个不断模仿、练习、创新、超越的过程，一起努力吧，呱呱~ 🐸
- ⚡ [Gitee 在线阅读](https://reanon.gitee.io/notes/) | [Gitee 仓库地址](https://gitee.com/Reanon/notes)
- 🔮 Github 在线阅读 | Github 仓库地址

## 📑 计算机基础

---

### 数据结构 DS

> [!WARNING]
> 该部分内容目前正在重构中，会陆续更新至 『 图解数据结构 』系列

- [图解数据结构 · 第 1 篇《线性表》](https://mp.weixin.qq.com/s/AxVkAeqN-a34O4rOFNV0IA)

### 算法 Algorithm

> [!TIP]
> 本节的每个知识点都配备对应的 LeetCode 题目，知其然而知其所以然，**拒绝无脑刷题**。目前尚在更新中，对应的 LeetCode 会逐渐添加进去。不需要看知识点的小伙伴可直接进入 <u>[LeetCode 分模块刷题指南](计算机基础/算法/LeetCode/刷题指南.md)</u>，所有题目会在此处分类汇总，现已包含 `83` 道题目。

- 1 - 算法基础计算机基础/算法/1-算法基础.md
- [2 - NP 完全性理论与近似算法](计算机基础/算法/2-NP完全性理论与近似算法.md)
- 【算法思想系列】
  - 3 - 递归
  - 4 - 分治计算机基础/算法/4-分治.md
  - 5 - 二分查找计算机基础/算法/5-二分查找.md
  - 6 - 动态规划计算机基础/算法/6-动态规划.md
  - 7 - 贪心算法计算机基础/算法/7-贪心算法.md
  - [8 - 回溯法](计算机基础/算法/8-回溯法.md)
  - [9 - 分支限界法](计算机基础/算法/9-分支限界法.md)
  - [10 - 双指针](计算机基础/算法/10-双指针.md)
  - [11 - 随机化算法](计算机基础/算法/11-随机化算法.md)
- 【数据结构系列】
  - 12 - 链表
  - 13 - 栈和队列
  - 14 - 哈希表
  - [15 - 树](计算机基础/算法/15-树.md)
  - 16 - 图
- <u>[🔥 LeetCode 分模块刷题指南](计算机基础/算法/LeetCode/刷题指南.md)</u>

### 计算机网络 Net

> [!WARNING]

### 操作系统 OS

<span data-v-73ca276e="" class="badge warning" style="vertical-align: base-line;">Vital</span>

### Linux

- 1 - Linux 简介
- 2 - Linux 系统启动过程 + 系统目录结构
- 3 - 如何远程登录 Linux 服务器
- 4 - Linux 磁盘管理
- 5 - Linux 文件与目录管理
- 6 - Linux 用户和用户组管理
- 7 - Linux 管道指令 + 正则表达式
- 8 - Linux 进程管理
- 9 - Vim 常用操作

### 数据库 DB

<span data-v-73ca276e="" class="badge tip" style="vertical-align: base-line;">Important</span>




### 软件工程 SE



## 🍵 Java

---

### Java 基础

> [!WARNING]
> 所有笔记和代码均基于 **Java 8** 版本，基本包含 Java 语言的全部核心知识点。目前正在重构中，将陆续更新至『 Java 小白成长记 』系列



### Java 虚拟机

### 设计模式

> [!NOTE]
> 设计模式是**解决问题的方案**，学习现有的设计模式可以做到经验复用。拥有设计模式词汇，在沟通时就能用更少的词汇来讨论，并且不需要了解底层细节。

- [设计模式七大原则]()
- [二十三种设计模式]() <span data-v-73ca276e="" class="badge warning" style="vertical-align: base-line;">Vital</span>

## 🔥 必备框架

---

> [!WARNING|label:注意区分下数据库和 ORM 框架！]
> 
>**数据库**：存储数据。常见的数据库如：
> 
>- MySQL
> - Oracle
> 
>**ORM 框架**：`Object Relational Mapping` 对象关系映射，将实体类对象持久化到数据库中。可以简单的认为 ORM 框架就是用来操作数据库的。常见的 ORM 框架如：
> 
>- JDBC
> - MyBatis （常见组合为 <u>Spring + SpringMVC</u> / Spring Boot + MyBatis + 数据库）
> - Hibernate（常见组合为 Spring Boot + Hibernate + Spring Data JPA + 数据库）

### SSM

- 1 - **Spring (Framework)**
  - [1.1 - Spring 简介 + Spring IoC 详解](必备框架/SSM/Spring/1-Spring简介+IoC详解.md) <span data-v-73ca276e="" class="badge warning" style="vertical-align: base-line;">Vital</span>
  - [1.2 - Spring IoC 容器源码分析](必备框架/SSM/Spring/2-SpringIoC容器源码分析.md)
  - [1.3 - Spring AOP 详解](必备框架/SSM/Spring/3-AOP详解.md) <span data-v-73ca276e="" class="badge warning" style="vertical-align: base-line;">Vital</span>
  - 1.4 - Spring AOP 源码分析
  - 1.4 - Spring 事务控制
  - <u>🎯 Spring 面试指南</u>
- 2 - **SpringMVC**
  - [Spring MVC 知识点全解](必备框架/SSM/SpringMVC/SpringMVC知识点全解.md)
  - <u>🎯 SpringMVC 面试指南</u>
- 3 - **MyBatis**
  - [MyBatis 知识点全解](必备框架/SSM/Mybatis/Mybatis知识点全解.md)
  - [使用 PageHelper 前先学会手写一个分页查询吧（SpringBoot 版）](必备框架/SSM/Mybatis/手写分页查询.md)
  - <u>🎯 MyBatis 面试指南</u>
- [4 - SSM 框架整合实例](必备框架/SSM/SSM框架整合.md)

### Spring Boot 2.x

  - [1 - Spring Boot 入门](必备框架/SpringBoot/1-SpringBoot入门.md)
  - [2 - 配置文件](必备框架/SpringBoot/2-配置文件.md)
  - [3 - 自定义 starter](必备框架/SpringBoot/3-自定义starter.md)
  - [4 - 日志](必备框架/SpringBoot/4-日志.md)
  - [5 - Web 开发](必备框架/SpringBoot/5-Web开发.md)
  - [7 - Web 开发 - 实战案例](必备框架/SpringBoot/7-Web开发-实战案例.md)
  - [6 - SpringBoot 前后端数据交互的几种常用方式 ⭐ ](必备框架/SpringBoot/6-SpringBoot前后端数据交互.md)
  - [8 - SpringBoot 与数据访问](必备框架/SpringBoot/8-SpringBoot与数据访问.md)
  - 9 - **Spring Data JPA**
    - [🛫 Spring Boot + Spring Data JPA 一篇文章快速入门](必备框架/SpringBoot/SpringDataJPA/SpringBoot+SpringDataJPA快速入门.md)
    - [9.1 - 实体类映射到数据库表的基本注解](必备框架/SpringBoot/SpringDataJPA/1-实体类映射到数据库表的基本注解.md)
    - [9.2 - 表之间的映射关系](必备框架/SpringBoot/SpringDataJPA/2-表之间的映射关系.md)
    - [9.3 - Spring Data JPA 的四种查询方式](必备框架/SpringBoot/SpringDataJPA/3-SpringDataJPA的四种查询方式.md)
    - [9.4 - Spring Data JPA 的更新方式详解](必备框架/SpringBoot/SpringDataJPA/4-SpringDataJPA的更新方式详解.md)
  - [10 - 异步、定时、邮件任务](必备框架/SpringBoot/10-异步定时邮件任务.md)
  - [11 - 集成 Redis（Lettuce）](必备框架/SpringBoot/11-集成Redis.md)
  - <u>🎯 SpringBoot 面试指南</u>
  - 💬 Echo（开源社区系统，尚在开发中）：基于 SpringBoot + MySQL + MyBaits + Redis + Elasticsearch + Kafka + Spring Security + ... 实现的开源社区，附详细开发文档 <span data-v-73ca276e="" class="badge recommendation" style="vertical-align: base-line;">项目经验首选</span>

### Netty 4.x

> [!Note]
> Netty 是目前 **Java 网络编程**最热门的框架，很多开源项目涉及到**网络通信**的部分都是基于 Netty 来做的，比如 Dubbo、RocketMQ、ElasticSearch 等。**学习 Netty 前请务必掌握 Java I/O 的相关知识**。并请注意 **Netty 5 已被官方废弃**，本笔记基于 Netty 4.x

- [1 - 从 BIO、NIO 到 Netty](必备框架/Netty/1-从BIONIO到Netty.md)
- [2 - 第一个 Netty 应用：HelloWorld](必备框架/Netty/2-第一个Netty应用.md)
- [3 - Netty 架构设计与特性](必备框架/Netty/3-Netty架构设计与特性.md)
- [4 - Netty 核心组件总览](必备框架/Netty/4-Netty核心组件总览.md)
- [5 - Transport（传输）详解](必备框架/Netty/5-Transport详解.md)
- [6 - Buffer（缓冲/字节容器）详解](必备框架/Netty/6-Buffer详解.md)
- [7 - ChannelHandler（消息处理器）与 ChannelPipeline 详解](必备框架/Netty/7-ChannelHandler与ChannelPipeline详解.md)
- [8 - Netty Reactor 线程模型与 EventLoop（事件循环）详解](必备框架/Netty/8-EventLoop与线程模型详解.md) <span data-v-73ca276e="" class="badge warning" style="vertical-align: base-line;">Vital</span>
- [9 - Bootstrap（引导）详解](必备框架/Netty/9-Bootstrap详解.md)
- [10 - Codec（编码与解码）详解](必备框架/Netty/10-Codec详解.md)
- [11 - 实例及分析：Netty 实现聊天功能](必备框架/Netty/11-实例-Netty实现聊天功能.md)
- [12 - Netty 是如何解决 TCP 粘包/拆包问题的](必备框架/Netty/12-Netty解决TCP粘包拆包问题.md)
- [13 - Netty 是如何实现 TCP 心跳机制与断线重连的](必备框架/Netty/13-Netty实现心跳机制.md)
- [14 - 实例及分析：Netty + Kryo 序列化传输对象](必备框架/Netty/14-实例-Netty+Kryo序列化传输对象.md)
- <u>[🎯 Netty 面试指南](必备框架/Netty/Netty面试指南.md)</u>

## 👷 系统设计

---

### 系统设计基础

- [1 - 大型网站核心架构要素](系统设计/系统设计基础/1-大型网站核心架构要素.md)
- 2 - Web 前端性能优化
- 3 - 应用服务器性能优化

### 编码之道

- 1 - RestFul API 详解
- 2 - Java 命名之道
- 3 - Java 编程规范

### 认证授权

#### 认证授权基础

- 1 - 认证授权之理论基础
- 2 - JWT
- 3 - SSO 单点登录

####  Apache Shiro

- [🛫 SpringBoot + Shiro 一篇文章快速入门](系统设计/认证授权/Shiro/Shiro快速入门.md)
- [Shiro 登录认证过程源码详解](系统设计/认证授权/Shiro/Shiro登录认证过程源码详解.md)

####  Spring Security 5.x

- [🛫 SpringBoot + SpringSecurity 一篇文章快速入门](系统设计/认证授权/SpringSecurity/SpringSecurity快速入门.md)
- [1 - Spring Security 概述](系统设计/认证授权/SpringSecurity/1-概述.md)
- [2 - Spring Boot + Spring Security 初体验](系统设计/认证授权/SpringSecurity/2-初体验.md)
- [3 - 基于内存的认证和授权](系统设计/认证授权/SpringSecurity/3-基于内存的认证和授权.md)
- [4 - 基于内存数据库的认证和授权](系统设计/认证授权/SpringSecurity/4-基于内存数据库的认证和授权.md)
- [5 - 基于 Mysql 数据库的认证和授权](系统设计/认证授权/SpringSecurity/5-基于Mysql数据库的认证和授权.md)
- [6 - 自定义登录页面和构建主页](系统设计/认证授权/SpringSecurity/6-自定义登录页面和构建主页.md)
- [7 - 登出处理和自定义 403 界面](系统设计/认证授权/SpringSecurity/7-登出处理和自定义403界面.md)
- [8 - 从数据库中动态加载角色](系统设计/认证授权/SpringSecurity/8-从数据库中动态加载角色.md)
- [9 - 原理分析](系统设计/认证授权/SpringSecurity/9-原理分析.md)
- [10 - 记住我](系统设计/认证授权/SpringSecurity/10-记住我.md)

### 高并发

👉 如何设计一个高并发系统

####  缓存

> [!WARNING|label:Important]
> 网站性能优化第一定律：优先考虑使用缓存

- 1 - 缓存的相关概念
- 2 - 内存数据库：**Redis** <span data-v-73ca276e="" class="badge warning" style="vertical-align: base-line;">Vital</span>
  - [2.1 - NoSQL 概述](系统设计/高并发/缓存/Redis/1-NoSQL概述.md)
  - [2.2 - Redis 入门](系统设计/高并发/缓存/Redis/2-Redis入门.md)
  - [2.3 - Redis 五大数据类型](系统设计/高并发/缓存/Redis/3-五大数据类型.md)
  - [2.4 - 三种特殊数据类型](系统设计/高并发/缓存/Redis/4-三种特殊数据类型.md)
  - [2.5 - 事务](系统设计/高并发/缓存/Redis/5-事务.md)
  - [2.6 - Java 使用 Redis — Jedis](系统设计/高并发/缓存/Redis/6-Java使用Redis.md)
  - [2.7 - Redis 配置文件详解](系统设计/高并发/缓存/Redis/7-Redis配置文件详解.md)
  - [2.8 - Redis 持久化](系统设计/高并发/缓存/Redis/8-Redis持久化.md)
  - [2.9 - Redis 发布订阅](系统设计/高并发/缓存/Redis/9-Redis发布订阅.md)
  - [2.10 - Redis 主从复制](系统设计/高并发/缓存/Redis/10-Redis主从复制.md)
  - [2.11 - Redis 缓存穿透和雪崩](系统设计/高并发/缓存/Redis/11-Redis缓存穿透和雪崩.md)
- <u>🎯 缓存面试指南</u>

#### 消息队列 MQ

> [!NOTE]
> 消息队列在分布式系统中主要是为了解耦和削峰

- [1 - 消息队列相关概念](系统设计/高并发/消息队列/1-消息队列相关概念.md)
- 2 - 消息队列中间件：**RabbitMQ**
  - [2.1 - RabbitMQ 入门](系统设计/高并发/消息队列/2-RabbitMQ入门.md)
  - [2.2 - SpringBoot + RabbitMQ 简单实例](系统设计/高并发/消息队列/3-SpringBoot+RabbitMQ简单实例.md)
- 3 - 消息队列中间件：**Kafka**
- 4 - 消息队列的缺点以及解决方案
- 5 - 如何设计一个消息队列

#### 读写分离

> [!NOTE]
> 读写分离主要是为了将数据库的读和写操作分布到不同的数据库节点上。**主服务器负责写，从服务器负责读**。一主一从或者一主多从都可以。
>
> 读写分离可以大幅提高读性能，小幅提高写的性能。因此，**读写分离更适合单机并发读请求比较多的场景**。

- [如何实现 MySQL 的读写分离（主从复制）](系统设计/高并发/读写分离/如何实现MySQL的读写分离.md)

#### 分库分表

> [!NOTE]
> **分库分表是为了解决由于库、表数据量过大，而导致数据库性能持续下降的问题。**
>
> 常见的分库分表工具/中间件有：`sharding-jdbc`（当当）、`TSharding`（蘑菇街）、`MyCAT`（基于 Cobar）、`Cobar`（阿里巴巴）...... 
>
> **推荐使用 `sharding-jdbc`** 。 因为 `sharding-jdbc` 是一款轻量级 `Java` 框架，以 `jar` 包形式提供服务，不要我们做额外的运维工作，并且兼容性也很好。

- [1 - 分库分表相关概念](系统设计/高并发/分库分表/1-分库分表相关概念.md)
- 2 - 分库分表中间件
- [3 - 如何将单库单表系统迁移到分库分表](系统设计/高并发/分库分表/3-如何将单库单表系统迁移到分库分表.md)

####  负载均衡

> [!NOTE]
> 所谓负载均衡即将任务比如用户请求处理分配到多个服务器进行处理，以提高网站、应用或者数据库的性能和可靠性。

- [1 - 负载均衡算法与实现负载均衡的技术](系统设计/高并发/负载均衡/1-负载均衡算法与实现负载均衡的技术.md)
- [2 - 集群、分布式、负载均衡的区别](系统设计/高并发/负载均衡/2-集群+分布式+负载均衡的区别.md)
- 3 - **Nginx**

### 高可用

👉 如何设计一个高可用系统

####  降级

> [!NOTE]
> 降级是从系统功能优先级的角度考虑如何应对系统故障。
>
> 服务降级是指系统为了应对大量的请求，**主动关闭部分功能，以此释放服务器资源从而保证核心功能可用**。

- 如何实现降级

####  限流

> [!NOTE]
> 限流可以认为是服务降级的一种，限流就是**限制系统的输入和输出流量**以达到保护系统的目的。一般来说系统的吞吐量是可以被测算的，为了保证系统的稳定运行，一旦达到需要限制的阈值，就采取一些措施以完成限制流量的目的。比如：延迟处理/排队，拒绝处理，或者部分拒绝处理等等。

- [如何实现限流（常见限流算法）](系统设计/高可用/限流/常见限流算法.md)

####  熔断

> [!NOTE]
> 熔断和降级是两个比较容易混淆的概念，两者的含义并不相同。
>
> 降级的目的在于应对系统自身的故障，而**熔断的目的在于应对当前系统依赖的外部系统或者第三方系统的故障**。

- 1 - 如何实现熔断
- 2 - 熔断中间件 **Hystrix**（已停更）
- 3 - 熔断中间件 **Sentienl**（推荐使用）
- 4 - 如何设计一个消息队列

### 分布式

####  分布式理论基础

- [1 - 分布式相关概念](系统设计/分布式/分布式理论基础/1-分布式相关概念.md) <span data-v-73ca276e="" class="badge warning" style="vertical-align: base-line;">Vital</span>
- [2 - 分布式事务](系统设计/分布式/分布式理论基础/2-分布式事务.md) <span data-v-73ca276e="" class="badge tip" style="vertical-align: base-line;">Important</span>
- [3 - 分布式 Session](系统设计/分布式/分布式理论基础/3-分布式Session.md)
- [4 - 分布式锁](系统设计/分布式/分布式理论基础/4-分布式锁.md) <span data-v-73ca276e="" class="badge tip" style="vertical-align: base-line;">Important</span>
- [5 - 分布式 ID](系统设计/分布式/分布式理论基础/5-分布式ID.md)

#### RPC 框架 — Dubbo

- [1 - 什么是 RPC 及其原理](系统设计/分布式/RPC/1-什么是RPC及其原理.md) <span data-v-73ca276e="" class="badge tip" style="vertical-align: base-line;">Important</span>

> [!Danger]
> **Dubbo 与 Spring Cloud 并不是竞争关系**，**Dubbo** 作为成熟的 RPC 框架（分布式服务框架），其易用性、扩展性和健壮性已得到业界的认可。而 **Spring Cloud** 是一整个微服务生态，目前 **Dubbo** 已经成为 **Spring Cloud Alibaba** 的 RPC 组件，与 **Spring Cloud** 原生的 **Feign** 以及 **RestTemplate** 进行无缝整合，实现“零”成本迁移。
>
> 💌 <u>[阿里巴巴中间件 独家解读：Dubbo Ecosystem - 从微服务框架到微服务生态](https://mp.weixin.qq.com/s/iNVctXw7tUGHhnF0hV84ww)</u>

- 2 - RPC 框架：**Apache Dubbo**
  - [2.1 - Dubbo 架构及工作原理](系统设计/分布式/RPC/2-Dubbo架构及工作原理.md) <span data-v-73ca276e="" class="badge warning" style="vertical-align: base-line;">Vital</span>
  - [2.2 - SpringBoot 2 + Dubbo + Zookeeper 搭建一个简单的分布式服务](系统设计/分布式/RPC/3-SpringBoot+Dubbo+Zookeeper搭建一个简单的分布式服务.md)
  - [2.3 - Dubbo 三种连接方式](系统设计/分布式/RPC/4-Dubbo三种连接方式.md)
  - [2.4 - Dubbo 通信协议与序列化](系统设计/分布式/RPC/5-Dubbo通信协议与序列化.md)
    - [序列化协议 Protostuff 基本使用](系统设计/分布式/RPC/5.1-序列化协议Protostuff基本使用.md)
    - [序列化协议 Kryo 基本使用](系统设计/分布式/RPC/5.2-序列化协议Kryo基本使用.md)
  - [2.5 - Dubbo 负载均衡策略 + 集群容错策略](系统设计/分布式/RPC/6-Dubbo负载均衡策略+集群容错策略.md)
  - [2.6 - Dubbo 动态代理 + SPI 机制](系统设计/分布式/RPC/7-Dubbo动态代理+SPI机制.md)
  - 2.7 - 深入 Dubbo 源码
    - Dubbo 的服务暴露过程
    - Dubbo 的服务引用过程
    - Dubbo 的服务调用过程
  - <u>🎯 Dubbo 面试指南</u>
- [🏆 从零开始设计一个轻量级分布式 RPC 框架](https://github.com/Veal98/RPC-FromScratch) <span data-v-73ca276e="" class="badge recommendation" style="vertical-align: base-line;">项目经验首选</span>

####  API 网关

- 1 - API 网关相关概念
- 2 - 网关中间件：**Netflix Zuul**（已停更）
- 3 - 网关中间件：**Gateway**（推荐使用）
- 4 - 如何设计一个网关

#### 分布式搜索引擎 — Elasticsearch

#### 分布式协调服务 — Zookeeper

> [!NOTE]
> 所谓**分布式协调服务**就是**将各个分布式组件协调起来**，减少各个系统之间的耦合度、处理分布式事务、配置整个分布式系统等等

- [1 - Zookeeper 重要概念详解](系统设计/分布式/Zookeeper/1-Zookeeper重要概念详解.md) <span data-v-73ca276e="" class="badge warning" style="vertical-align: base-line;">Vital</span>
- [2 - Zookeeper 安装与基本使用](系统设计/分布式/Zookeeper/2-Zookeeper安装与基本使用.md)

### 微服务架构 — Spring Cloud

####  微服务相关概念

- [1 - 什么是微服务与 Spring Cloud](系统设计/微服务/微服务相关概念/1-什么是微服务与SpringCloud.md)
- 2 - 微服务之间是如何进行独立通讯的


####  Spring Cloud Netflix

> [!Warning]
> **Spring Cloud Netflix**  2018 年12 月 12 日进入维护模式（Maintenance Mode），且其内置组件/中间件大部分都已停更或出现更好的替代组件，所以不太适合再长期使用

- [1 - Spring Cloud Netflix 技术栈及组件停更说明](系统设计/微服务/Spring-Cloud-Netflix/1-技术栈及组件停更说明.md)

#### Spring Cloud Alibaba

## 🔨 工具

---

### Git

### Docker

## 🎨 前端

---

### 必备基础知识

- 1 - HTML / CSS / JS 速览
- [2 - JSON 详解](前端/必备基础/JSON详解.md)
- [3 - AJAX 详解](前端/必备基础/AJAX详解.md)

### Vue 2.x

- [1 - Vue.js 入门](前端/Vue/1-入门.md)
- [2 - 安装](前端/Vue/2-安装.md)
- [3 - Vue-Cli 3+搭建的项目目录结构](前端/Vue/3-目录结构.md)
- [4 - 模板语法](前端/Vue/4-模板语法.md)
- [5 - 条件语句](前端/Vue/5-条件语句.md)
- [6 - 循环语句](前端/Vue/6-循环语句.md)
- [7 - 计算属性](前端/Vue/7-计算属性.md)
- [8 - 监听属性](前端/Vue/8-监听属性.md)
- [9 - 样式绑定](前端/Vue/9-样式绑定.md)
- [10 - 事件处理器](前端/Vue/10-事件处理器.md)
- [11 - 表单](前端/Vue/11-表单.md)
- [12 - 组件](前端/Vue/12-组件.md)
- [13 - 自定义指令](前端/Vue/13-自定义指令.md)
- [14 - 路由详解](前端/Vue/14-路由.md)
- [15 - Axios 详解](前端/Vue/15-axios.md)
- [16 - 生命周期钩子](前端/Vue/16-生命周期钩子.md)

## 🐍 Python

---

### Python 3 基础

  - [1 - Python基础](人工智能/Python/Python入门学习笔记/1-Python基础.md)
  - [2 - 函数](人工智能/Python/Python入门学习笔记/2-函数.md)
  - [3 - 高级特性](人工智能/Python/Python入门学习笔记/3-高级特性.md)
  - [4 - 函数式编程](人工智能/Python/Python入门学习笔记/4-函数式编程.md)
  - [5 - 模块](人工智能/Python/Python入门学习笔记/5-模块.md)
  - [6 - 面向对象编程](人工智能/Python/Python入门学习笔记/6-面向对象编程.md)
  - [7 - 面向对象高级编程](人工智能/Python/Python入门学习笔记/7-面向对象高级编程.md)
  - [8 - 错误、调试和测试](人工智能/Python/Python入门学习笔记/8-错误、调试和测试.md)
  - [9 - IO 编程](人工智能/Python/Python入门学习笔记/9-IO编程.md)
  - [10 - 进程和线程](人工智能/Python/Python入门学习笔记/10-进程和线程.md)
  - [11 - 正则表达式](人工智能/Python/Python入门学习笔记/11-正则表达式.md)

### Python 数据分析

  - [1 - 准备工作](人工智能/Python/Python数据分析/1-准备工作.md)
  - [2 - IPython 和 Jupyter NoteBook](人工智能/Python/Python数据分析/2-IPython和Jupyter-Notebook.md)
  - [3 - Python 语法基础、数据结构、函数、文件](人工智能/Python/Python数据分析/3-Python语法基础+数据结构+函数+文件.md)
  - [4 - Numpy 基础](人工智能/Python/Python数据分析/4-Numpy基础.md)
  - [✍ Numpy：练习题 100 题](人工智能/Python/Python数据分析/4.1-Numpy练习题100题.md)
  - [5 - pandas：入门](人工智能/Python/Python数据分析/5-pandas入门.md)
  - [6 - pandas：数据加载、存储与文件格式](人工智能/Python/Python数据分析/6-pandas数据加载、存储与文件格式.md)
  - [7 - pandas：数据清洗和准备](人工智能/Python/Python数据分析/7-pandas数据清洗和准备.md)
  - [8 - pandas：数据规整 - 聚合、合并和重塑](人工智能/Python/Python数据分析/8-pandas数据规整：聚合、合并和重塑.md)
  - [9 - matplotlib：绘图和可视化](人工智能/Python/Python数据分析/9-matplotlib绘图和可视化.md)
  - [10 - pandas：数据聚合与分组运算](人工智能/Python/Python数据分析/10-pandas数据聚合与分组运算.md)

## 🤖 机器学习

---

### 机器学习入门

> [!TIP]
> 本节基于吴恩达老师的《Machine Learning》课程，并未涉及复杂的数学推导和代码，可用来对机器学习做一个初步的认识和了解。

  - [1 - Introduction to Machine Learning](人工智能/机器学习/吴恩达/1-Introduction.md)
  - [2 - 模型评估与选择](人工智能/机器学习/吴恩达/2-模型评估与选择.md)
  - [3 - 线性回归](人工智能/机器学习/吴恩达/3-线性回归.md)
  - [4 - 逻辑回归 + 正则化](人工智能/机器学习/吴恩达/4-逻辑回归+正则化.md)
  - [5 - 神经网络](人工智能/机器学习/吴恩达/5-神经网络.md)
  - [6 - 支持向量机 SVM](人工智能/机器学习/吴恩达/6-支持向量机SVM.md)
  - [7 - 聚类 (K-means 算法) + 降维 (PCA 算法)](人工智能/机器学习/吴恩达/7-聚类+降维.md)
  - [8 - 异常检测 + 推荐系统](人工智能/机器学习/吴恩达/8-异常检测+推荐系统.md)
  - [9 - 大规模机器学习+图片文字识别](人工智能/机器学习/吴恩达/9-大规模机器学习+图片文字识别.md)
  - [✍ 吴恩达 Coursera 机器学习 — 编程作业 — Python 实现](人工智能/机器学习/吴恩达/吴恩达编程作业.md)

### 常见算法 Python 实现

- 【第一部分：分类 】
  - [1 - k-近邻算法](人工智能/机器学习/机器学习实战/1-k-近邻算法.md)
  - [2 - 决策树](人工智能/机器学习/机器学习实战/2-决策树.md)
  - [3 - 朴素贝叶斯](人工智能/机器学习/机器学习实战/3-朴素贝叶斯.md)
  - [4 - Logistic 回归](人工智能/机器学习/机器学习实战/4-Logistic回归.md)
  - [5 - 支持向量机 SVM](人工智能/机器学习/机器学习实战/5-支持向量机.md)
  - [6 - 集成方法 + 非均衡分类问题](人工智能/机器学习/机器学习实战/6-集成方法.md)
- 【第二部分：回归】
  - [7 - 线性回归](人工智能/机器学习/机器学习实战/7-线性回归.md)
  - [8 - 树回归](人工智能/机器学习/机器学习实战/8-树回归.md)
- 【第三部分：无监督学习】
  - [ 9 - K-均值算法](人工智能/机器学习/机器学习实战/9-K-均值算法.md)
  - [10 - 使用 Apriori 算法进行关联分析](人工智能/机器学习/机器学习实战/10-使用Apriori算法进行关联分析.md)
  - [11 - 使用 FP-growth 算法来高效发现频繁项集](人工智能/机器学习/机器学习实战/11-FP-growth算法.md)
- 【第四部分：其他工具】
  - [12 - 利用 PCA 简化数据](人工智能/机器学习/机器学习实战/12-利用PCA简化数据.md)
  - [13 - 利用 SVD 简化数据](人工智能/机器学习/机器学习实战/13-利用SVD简化数据.md)

## 🌺 深度学习

---

### 深度学习入门

> [!TIP]
> 本节基于吴恩达老师的《deeplearning.ai》课程。

- 【第一部分：神经网络与深度学习】
  - [1 - Introduction to Deep Learning](人工智能/深度学习/入门/1-Introduction.md)
  - [2 - 神经网络基础之逻辑回归](人工智能/深度学习/入门/2-神经网络基础之逻辑回归.md)
  - [3 - 神经网路基础之 Python 与向量化](人工智能/深度学习/入门/3-神经网路基础之Python与向量化.md)
  - [4 - 浅层神经网络](人工智能/深度学习/入门/4-浅层神经网络.md)
  - [5 - 深层神经网络](人工智能/深度学习/入门/5-深层神经网络.md)
- 【第二部分：优化深度神经网络】
  - [6 - 深度学习的实用层面](人工智能/深度学习/入门/6-深度学习的实用层面.md)
  - [7 - 优化算法](人工智能/深度学习/入门/7-优化算法.md)
  - [8 - 超参数调试 + Batch 正则化 + Softmax 回归](人工智能/深度学习/入门/8-超参数调试+Batch正则化+Softmax回归.md)
- 【第三部分：构建机器学习项目】
  - [9 - 机器学习策略 (上)](人工智能/深度学习/入门/9-机器学习策略上.md)
  - [10 - 机器学习策略 (下)](人工智能/深度学习/入门/10-机器学习策略下.md)
- 【第四部分：卷积神经网络 CNN】
  - [11 - 卷积神经网络基础](人工智能/深度学习/入门/11-卷积神经网络基础.md)
  - [12 - 深度卷积网络：实例探究](人工智能/深度学习/入门/12-深度卷积网络-实例探究.md)
  - [13 - 目标检测](人工智能/深度学习/入门/13-目标检测.md)
  - [14 - CNN 的特殊应用：人脸识别和神经风格转换](人工智能/深度学习/入门/14-特殊应用-人脸识别和神经风格转换.md)
- 【第五部分：序列模型】
  - [15 - 循环神经网络 RNN](人工智能/深度学习/入门/15-循环神经网络.md)

### TensorFlow 2

- [🛫 TensorFlow 2 快速入门](人工智能/深度学习/TensorFlow2/TensorFlow2快速入门.md)
- 【TensorFlow 2 基础知识】
  - 1 - 即刻执行 Eager Execution
  - [2 - 张量 tensor](人工智能/深度学习/TensorFlow2/TensorFlow2基础知识/2-张量.md)
  - [3 - 自动微分](人工智能/深度学习/TensorFlow2/TensorFlow2基础知识/3-自动微分.md)
- 【Keras 机器学习基础知识】
  - [1 - Sequential 顺序模型](人工智能/深度学习/TensorFlow2/Keras机器学习基础知识/4-Sequential顺序模型.md)
  - [2 - 使用函数式 API 的 Model 类模型](人工智能/深度学习/TensorFlow2/Keras机器学习基础知识/2-使用函数式API的Model类模型.md)
  - [3 - Keras 网络层](人工智能/深度学习/TensorFlow2/Keras机器学习基础知识/3-Keras网络层.md)
  - [4 - 自定义层和模型](人工智能/深度学习/TensorFlow2/Keras机器学习基础知识/4-自定义层和模型.md)
  - [5 - 防止过拟合](人工智能/深度学习/TensorFlow2/Keras机器学习基础知识/5-防止过拟合.md)
  - [实例：1 - 基本图像分类](人工智能/深度学习/TensorFlow2/Keras机器学习基础知识/实例1-基本图像分类.md)
  - [实例：2 - 基本文本分类](人工智能/深度学习/TensorFlow2/Keras机器学习基础知识/实例2-基本文本分类.md)
  - [实例：3 - 回归](人工智能/深度学习/TensorFlow2/Keras机器学习基础知识/实例3-回归.md)
- 【图像分类】
  - [1 - 使用卷积神经网络 CNN 进行图像分类](人工智能/深度学习/TensorFlow2/图像分类/1-使用CNN进行图像分类.md)
  - 2 - 使用 TF-Hub 进行迁移学习
- 【文本分类】
  - [1 - 使用循环神经网络 RNN 进行文本分类](人工智能/深度学习/TensorFlow2/文本分类/1-使用RNN进行文本分类.md)

## 🎉 其他

---

### 写作相关

- [Snipaste + PicGo+ Gitee 图床一键上传](其他/图床一键上传.md)
- [LaTeX 常用语法](其他/LaTeX常用语法.md)
- 作图软件推荐（draw.io / ProcessOn）
