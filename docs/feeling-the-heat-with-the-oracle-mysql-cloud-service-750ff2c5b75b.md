# 感受 Oracle MySQL 云服务的热度

> 原文：<https://medium.com/oracledevs/feeling-the-heat-with-the-oracle-mysql-cloud-service-750ff2c5b75b?source=collection_archive---------0----------------------->

![](img/4a3dd082c8bf2a9a105f5dd9c390a0d7.png)

对于开源关系数据库，Oracle MySQL 仍然是当今最受欢迎的数据库之一。

很少有人会意识到 Oracle MySQL 的与众不同，这是其他开源技术所没有的。例如 Postgres。

此外，Oracle MySQL 数据库服务可作为 OLTP 和 OLAP 查询的内存查询加速器与 HeatWave 一起使用。

但是，如果您已经熟悉了另一种开源关系数据库，为什么要首先考虑 Oracle MySQL 呢？

研究 Oracle MySQL 的一些标准特性是很有帮助的，这些特性使它在开源人群中脱颖而出。

## 存储引擎

对于那些熟悉其他数据库技术的人来说，Oracle MySQL 存储引擎可能并不熟悉。

本质上，对于您创建的每个表，您通过指定存储引擎来决定它的操作配置文件。有多种存储引擎可供选择，但 Oracle MySQL 中的两种主要存储引擎是:

*   InnoDB(事务存储引擎)。
*   MyISAM(非事务存储引擎)。

> InnoDB 是默认的、最通用的存储引擎，Oracle 建议将其用于表，除非是特殊的用例。

存储引擎之间的差异如下。

**1。InnoDB 存储引擎**

InnoDB 是 MySQL 客户通常用于可靠性和并发性非常重要的事务的存储引擎。

此存储引擎具有以下属性:

— InnoDB 是一个多版本存储引擎。它保留了有关已更改行的旧版本的信息，以支持事务性特性，如并发和回滚。

—每个表都经过物理组织，以基于主键优化查询。

—使用行级锁定来支持多个会话的同时写入访问，并在默认情况下以非锁定一致读取的方式运行查询，这是 Oracle 的风格。

如果服务器由于硬件或软件问题意外退出，不管当时数据库中发生了什么，在重新启动数据库后，您不需要做任何特殊的事情。

**2。MyISAM 存储引擎**

由于 MyISAM 存储引擎是非事务性的，因此由于所使用的锁定模型，它最适合只读、主要读取或单用户应用程序。

MyISAM 只有表级锁定(多个读取器使用一个写入器)。

*注意——与 Oracle 数据库不同，默认情况下，MySQL(和其他开源关系数据库)将在启用自动提交的情况下为每个新连接启动会话。*

## 指数

至于 Oracle 数据库，MySQL 支持使用范围、列表和散列分区。

对于 InnoDB 存储引擎:

—每个 InnoDB 表都有一个称为聚集索引的特殊索引，用于存储行数据。通常，聚集索引与主键同义。

—除聚集索引之外的 InnoDB 索引称为辅助索引。在 InnoDB 中，二级索引中的每条记录都包含该行的主键列，以及为二级索引指定的列。

— InnoDB 我们可以在同一个表上拥有 b 树索引和自适应散列索引特性吗？

— InnoDB 可以有散列(或键控)分区表，索引将遵循相同的分区。

也支持降序索引和不可见索引。

## 数据访问路径

对于查询的执行，优化是先进的。Oracle MySQL 能够对查询执行以下高级优化:

*   索引跳过扫描。
*   分区修剪。

在 Oracle MySQL 中，DML 的优化相当复杂，与 Oracle 数据库没有太大的不同。

## 在线 DDL

就像 Oracle 数据库一样，MySQL 确实为在线 DDL 大放异彩。

表和索引上的许多 DDL 操作(CREATE、ALTER 和 DROP 语句)可以在线执行。

*   索引操作
*   主键操作
*   列操作
*   生成的列操作
*   外键操作
*   表格操作
*   表空间操作
*   分区操作

在在线 DDL 操作的广度上，其他开源关系数据库无法与 Oracle MySQL 相提并论。

## 物理备份

Oracle MySQL 能够进行物理备份。备份由 MySQL 企业备份实用程序管理。

**完整备份**

MySQL Enterprise Backup 对所有使用 InnoDB 存储引擎的表进行热备份。

—可以在 MySQL 服务器未运行时执行备份。

—如果服务器正在运行，则有必要执行适当的锁定，以便服务器在备份过程中不会更改数据库内容。MySQL Enterprise Backup 会为需要锁定的表自动进行锁定。

对于使用 MyISAM 或其他非 InnoDB 存储引擎的表，它会进行“热”备份，此时数据库会继续运行，但这些表在备份时不能被修改。

**增量备份**

增量备份实际上只是事务创建的日志的备份。

通过启用服务器的二进制日志，增量备份成为可能，服务器使用二进制日志来记录数据更改。

## 维护

不像在 Oracle 数据库中，实际上没有维护任务，MySQL 需要一些任务。

**MyISM 存储引擎的容量规划**

—若要合并碎片行并消除因删除或更新行而造成的空间浪费，请在恢复模式下运行 myisamchk。

—定期执行表检查是一个好主意，而不是等待问题出现。

此外，截断每个表的文件表空间非常快，并且可以释放磁盘空间供操作系统重用。

**统计数据收集**

在 Oracle MySQL 中，表统计是手动完成的，但是可能会出现自动收集状态的情况。

Paul Guerin 是一名专注于 Oracle 数据库的国际顾问。此外，他还出席了一些世界领先的甲骨文会议，包括甲骨文 2013 年世界开放大会。自 2015 年以来，他的工作一直是 IOUG 最佳实践技巧小册子以及 AUSOUG、Oracle Technology Network、Quest 和 Oracle Developers (Medium)出版物的主题。2019 年，他被授予 My Oracle 支持社区最有价值贡献者。他是一名 DBA OCP，并将继续参与 Oracle ACE 计划。