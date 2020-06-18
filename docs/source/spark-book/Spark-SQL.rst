.. _header-n0:

Spark SQL
==============

.. _header-n3:

1.Spark SQL 背景
------------------

使用 Spark SQL，可以对存储到数据库中的视图或表进行 SQL 查询，还可以使用系统函数或用户自定义函数来分析和查询计划以优化器工作负载. 这直接集成到 DataFrame 和 Dataset API 中.

1.1 SQL
~~~~~~~~~~~~~~~~~

结构化查询语言(Structured Query Language, SQL) 是一种表示数据关系操作的特定领域语言。SQL 广泛应用在关系型数据库中，许多“NoSQL”数据库也支持类 SQL 语言以使其更便于使用。

Spark 实现了 ANSI SQL 2003 标准(https://en.wikipedia.org/wiki/SQL:2003)的子集，此 SQL 标准是在大多数 SQL 数据库中都支持的，这种支持意味着 Spark 能够运行各种流行的 TPC-DS 基准测试(http://www.tpc.org/default.asp).

1.2 Apache Hive
~~~~~~~~~~~~~~~~~~

在 Spark 流行之前，Hive 是支持 SQL 的主流大数据处理工具。Hive 最初是由 Facebook 开发，曾经是支持大数据 SQL 操作的一个非常流行的工具。它在许多方面将 Hadoop 推广到不同的行业，因为分析师可以运行 SQL 查询命令来实现他们的操作。尽管 Spark 最初是作为一个基于弹性分布式数据集(RDD)的通用处理引擎开发的，但现在大量用户都在使用 Spark SQL.

1.3 Spark SQL
~~~~~~~~~~~~~~

Spark 2.0 发布了一个支持 Hive 操作的超集，并提供了一个能够同时支持 ANSI-SQL 和 HiveQL 的原生 SQL 解析器。Spark SQL 和 DataFrame 的互操作性，使得 Spark SQL 成为各大公司强有力的工具。2016年末，发布 Hive 的 Facebook 公司宣布已经开始运行 Spark 工作负载，并取得很好的效果.

Spark SQL 在以下关键方面具有强大的能力：

   - SQL 分析人员可通过 Thrift Server 或者 Spark 的 SQL 接口利用 Spark 的计算能力

   - 数据工程师或者科学家可以在任何数据流中使用 Spark SQL

   - Spark SQL 这个统一的 API 功能强大，允许使用 SQL 提取数据，并将数据转化成 DataFrame 进行处理

   - 可以把数据交由 Spark MLlib 的大型机器学习算法处理，还可以将数据写到另一个数据源中

Spark SQL 的目的是作为一个在线分析处理(OLAP)数据库而存在，而不是在线事务处理(OLTP)数据库，这意味着 Spark SQL 现在还不适合执行对低延迟要求极高的查询，但是未来，Spark SQL 将会支持这一点.

1.4 Spark 与 Hive 的关系
~~~~~~~~~~~~~~~~~~~~~~~~~~



.. _header-n4:

2.Spark SQL 查询
-----------------

- Spark SQL CLI
- Spark 的可编程 SQL 接口
- Spark SQL Thrift JDBC/ODBC 服务器

2.1 Spark SQL CLI
^^^^^^^^^^^^^^^^^^^^^^^


2.2 Spark 的可编程 SQL 接口
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


2.3 Spark SQL Thrift JDBC/ODBC 服务器
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^





3. Catalog
---------------

.. _header-n1009:

3.1 数据表(tables)
~~~~~~~~~~~~~~~~~~~~~



.. _header-n1010:

3.1.1 Spark SQL 创建表
^^^^^^^^^^^^^^^^^^^^^^

读取 flight data 并创建为一张表:

.. code:: sql

   CREATE TABLE flights (
       DEST_COUNTRY_NAME STRING, 
       ORIGIN_COUNTRY_NAME STRING, 
       COUNTS LONG
   )
   USING JSON OPTIONS (path "/data/flight-data/json/2015-summary.json")

.. code:: sql

   CREATE TABLE flights (
       DEST_COUNTRY_NAME STRING, 
       ORIGIN_COUNTRY_NAME STRING "remember, the US will be most prevalent", 
       COUNTS LONG
   )
   USING JSON OPTIONS (path, "/data/flight-dat/json/2015-summary.json")

.. code:: sql

   CREATE TABLE flights_from_select USING parquet AS 
   SELECT * 
   FROM flights

.. code:: sql

   CREATE TALBE IF NOT EXISTS flights_from_select AS 
   SELECT *
   FROM flights

.. code:: sql

   CREATE TABLE partitioned_flights USING parquet PARTITION BY (DEST_COUNTRY_NAME) AS 
   SELECT 
       DEST_COUNTRY_NAME, 
       ORIGIN_COUNTRY_NAME, 
       COUNTS 
   FROM flights
   LIMIT 5

.. _header-n1018:

3.1.2 Spark SQL 创建外部表
^^^^^^^^^^^^^^^^^^^^^^^^^^

.. _header-n1019:

3.1.3 Spark SQL 插入表
^^^^^^^^^^^^^^^^^^^^^^

.. code:: sql

   INSERT INTO flights_from_select
   SELECT 
       DEST_COUNTRY_NAME,
       ORIGIN_COUNTRY_NAME,
       COUNTS
   FROM flights
   LIMIT 20

.. code:: sql

   INSERT INTO partitioned_flights
   PARTITION (DEST_COUNTRY_NAME="UNITED STATES")
   SELECT 
       COUNTS,
       ORIGIN_COUNTRY_NAME
   FROM flights
   WHERE DEST_COUNTRY_NAME="UNITED STATES"
   LIMIT 12

.. _header-n1024:

3.1.4 Spark SQL Describing 表 Matadata
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: sql

   DESCRIBE TABLE flights_csv

.. _header-n1026:

3.1.5 Spark SQL Refreshing 表 Matadata
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: sql

   REFRESH TABLE partitioned_flights

.. code:: sql

   MSCK REPAIR TABLE partitioned_flights

.. _header-n1030:

3.1.6 Spark SQL 删除表
^^^^^^^^^^^^^^^^^^^^^^

   当删除管理表(managed table)时，表中的数据和表的定义都会被删除；

.. code:: sql

   DROP TABLE flights_csv;
   DROP TABLE IF EXISTS flights_csv;

..

   当删除非管理表时，表中的数据不会被删除，但是不能够再引用原来表的名字对表进行操作；

.. _header-n1038:

3.1.7 Caching 表
^^^^^^^^^^^^^^^^

.. code:: sql

   CACHE TABLE flights
   UNCACHE TABLE flights

.. _header-n1042:

4. 视图 (views)
----------------

   -  A view specifies a set of transformations on top of an existing
      table-basically just saved query plans, which cna be convenient
      for organizing or resuing query logic.

   -  A view is effectively a transformation and Spark will perform it
      only at query time, views are equivalent to create a new DataFrame
      from an existing DataFrame.

.. _header-n1049:

3.2.1 创建视图
^^^^^^^^^^^^^^

创建 View:

.. code:: sql

   CREATE VIEW just_usa_view AS
   SELECT *
   FROM flights 
   WHERE DEST_COUNTRY_NAME = 'UNITED STATES'

.. code:: sql

   CREATE OR REPLACE TEMP VIEW just_usa_view_temp AS 
   SELECT *
   FROM flights
   WHERE DEST_COUNTRY_NAME = "UNITED STATES"

创建临时 View:

.. code:: sql

   CREATE TEMP VIEW just_usa_view_temp AS 
   SELECT *
   FROM flights 
   WHERE DEST_COUNTRY_NAME = "UNITED STATES"

创建全局临时 View:

.. code:: sql

   CREATE GLOBAL TEMP VIEW just_usa_global_view_temp AS 
   SELECT *
   FROM flights
   WHERE DEST_COUNTRY_NAME = "UNITED STATES"

   SHOW TABLES

.. _header-n1057:

3.2.2 删除视图
^^^^^^^^^^^^^^

.. code:: sql

   DROP VIEW IF EXISTS just_usa_view;

.. _header-n1059:

3.2.3 DataFrame 和 View
^^^^^^^^^^^^^^^^^^^^^^^

**DataFrame:**

.. code:: scala

   val flights = spark.read.format("json")
       .load("/data/flight-data/json/2015-summary.json")

   val just_usa_df = flights.where("dest_country_name = 'United States'")

   just_usa_df.selectExpr("*").explain

**View:**

.. code:: sql

   EXPLAIN SELECT * FROM just_usa_view
   EXPLAIN SELECT * FROM flights WHERE dest_country_name = "United States"

.. _header-n1065:

5. 数据库 (databases)
-------------------------

.. _header-n1066:

3.3.1 创建数据库
^^^^^^^^^^^^^^^^

.. _header-n1067:

3.3.2 配置数据库
^^^^^^^^^^^^^^^^

.. _header-n1068:

3.3.3 删除数据库
^^^^^^^^^^^^^^^^

.. _header-n1070:

6. 数据查询语句
-------------------------

   ANSI SQL

**(1) 查询语句**

.. code:: sql

   SELECT [ALL|DESTINCT] 
       named_expression[, named_expression, ...]
   FROM relation[, relation, ...] 
        [lateral_view[, lateral_view, ...]]
   [WHERE boolean_expression]
   [aggregation [HAVING boolean_expression]]
   [ORDER BY sort_expression]
   [CLUSTER BY expression]
   [DISTRIBUTE BY expression]
   [SORT BY sort_expression]
   [WINDOW named_window[, WINDOW named_window, ...]]
   [LIMIT num_rows]

其中:

-  named_expression:

   -  ``expression [AS alias]``

-  relation:

   -  ``join_relation``

   -  ``(table_name|query|relation) [sample] [AS alias]``

   -  ``VALUES (expression)[, (expressions), ...] [AS (column_name[, column_name, ...])]``

-  expression:

   -  ``expression[, expression]``

-  sort_expression:

   -  ``expression [ASC|DESC][, expression [ASC|DESC], ...]``

**(2) CASE...WHEN...THEN...ELSE...END 语句**

.. code:: sql

   SELECT 
       CASE WHEN DEST_COUNTRY_NAME = 'UNITED STATES' THEN 1
            WHEN DEST_COUNTRY_NAME = 'Egypt' THEN 0
            ELSE -1 
       END
   FROM partitioned_flights

.. _header-n1104:

7. 其他
------------



