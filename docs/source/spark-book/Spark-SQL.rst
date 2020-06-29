.. _header-n0:

Spark SQL
==============

.. _header-n3:

1.Spark SQL 背景
------------------

使用 Spark SQL,可以对存储到数据库中的视图或表进行 SQL 查询,
还可以使用系统函数或用户自定义函数来分析和查询计划以优化其工作负载. 
这直接集成到 DataFrame 和 Dataset API 中.

1.1 SQL
~~~~~~~~~~~~~~~~~

结构化查询语言(Structured Query Language, SQL) 是一种表示数据关系操作的特定领域语言.
SQL 广泛应用在关系型数据库中,许多“NoSQL”数据库也支持类 SQL 语言以使其更便于使用.

.. note:: 

   Spark 实现了 ANSI SQL 2003 标准(https://en.wikipedia.org/wiki/SQL:2003)的子集,
   此 SQL 标准是在大多数 SQL 数据库中都支持的,这种支持意味着 Spark 能够运行各种流行的 
   TPC-DS 基准测试(http://www.tpc.org/default.asp).

1.2 Apache Hive
~~~~~~~~~~~~~~~~~~

在 Spark 流行之前,Hive 是支持 SQL 的主流大数据处理工具.
Hive 最初是由 Facebook 开发,曾经是支持大数据 SQL 操作的一个非常流行的工具.
它在许多方面将 Hadoop 推广到不同的行业,因为分析师可以运行 SQL 查询命令来实现他们的操作.
尽管 Spark 最初是作为一个基于弹性分布式数据集(RDD)的通用处理引擎开发的,
但现在大量用户都在使用 Spark SQL.

1.3 Spark SQL
~~~~~~~~~~~~~~

Spark 2.0 发布了一个支持 Hive 操作的超集,并提供了一个能够同时支持 ANSI-SQL 和 HiveQL 的原生 SQL 解析器.
Spark SQL 和 DataFrame 的互操作性,使得 Spark SQL 成为各大公司强有力的工具.2016年末,
发布 Hive 的 Facebook 公司宣布已经开始运行 Spark 工作负载,并取得很好的效果.

Spark SQL 在以下关键方面具有强大的能力:

   - SQL 分析人员可通过 Thrift Server 或者 Spark 的 SQL 接口利用 Spark 的计算能力

   - 数据工程师或者科学家可以在任何数据流中使用 Spark SQL

   - Spark SQL 这个统一的 API 功能强大,允许使用 SQL 提取数据,并将数据转化成 DataFrame 进行处理

   - 可以把数据交由 Spark MLlib 的大型机器学习算法处理,还可以将数据写到另一个数据源中

.. note:: 

   Spark SQL 的目的是作为一个在线分析处理(OLAP)数据库而存在, 而不是在线事务处理(OLTP)数据库, 
   这意味着 Spark SQL 现在还不适合执行对低延迟要求极高的查询, 但是未来,Spark SQL 将会支持这一点.

1.4 Spark 与 Hive 的关系
~~~~~~~~~~~~~~~~~~~~~~~~~~

Spark SQL 与 Hive 的联系很紧密, 因为 Spark SQL 可以与 Hive metastore 连接.

Hive metastore 维护了 Hive 跨会话数据表的信息, 使用 Spark SQL 可以连接到 Hive metastore 访问表的元数据.
这可以在访问信息的时候减少文件列表操作带来的开销.对传统 Hadoop 环境转而使用 Spark 环境运行工作负载的用户来说, 这很受欢迎.

要连接到 Hive metastore, 需要设置几个属性:

   - ``spark.SQL.hive.metastore.version``

      - 设置 Metastore 版本, 对应于要访问的 Hive metastore, 默认情况为 ``1.2.1``
   
   - ``spark.SQL.hive.metastore.jars``
      
      - 如果要更改 Hive MetastoreClient 的初始化方式, 还需要设置 Hive metastore JAR 包. Spark 使用默认版本, 但也可以通过设置 Java 虚拟机(JVM)来指定 Maven repositories 或 classpath

   - ``spark.SQL.hive.metastore.sharedPrefixes``
      
      - 可能还需要提供适当的类前缀, 以便与存储 Hive metastore 的不同数据库进行通信.要将这些设置为 "Spark" 和 "Hive" 共享的前缀

.. note:: 

   如果要连接到自己的 metastore, 则要查询该文档以了解相关的更新信息.


.. _header-n4:

2.Spark SQL 运行
------------------

   - Spark SQL CLI
   - Spark 的可编程 SQL 接口
   - Spark SQL Thrift JDBC/ODBC 服务器

2.1 Spark SQL CLI
~~~~~~~~~~~~~~~~~~~~~~

使用 Spark SQL CLI, 可以在本地模式命令行中实现基本的 Spark SQL 查询. Spark SQL CLI 无法与 Thrift JDBC 服务端通信.

要启动 Spark SQL CLI, 需要在 Spark 目录中运行以下命令:

   .. code-block:: shell

      ./bin/spark-sql

.. note:: 

   - 可以通过修改 ``conf\`` 文件夹下的 ``hive-site.xml``, ``core-site.xml``, ``hdfs-site.xml`` 等文件来配置 Spark SQL CLI.

   - 可以运行 ``./bin/spark-sql -help`` 查看所有的可选选项的完整列表.


2.2 Spark 的可编程 SQL 接口
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

可以通过任何 Spark 支持语言的 API 执行 SQL.可以通过 ``SparkSession`` 对象上的 ``sql`` 方法来实现, 这将返回一个 ``DataFrame``.

示例 1:
   - 命令 ``spark.sql("SELECT 1 + 1")`` 返回一个 DataFrame, 可以被后续处理, 这是一个强大的接口,  
     因为有一些转换操作通过 SQL 代码表达要比通过 DataFrame 表达要简单得多.

   .. code-block:: python

      # in Python

      spark.sql("SELECT 1 + 1").show()

示例 2: 
   - 通过将多行字符串传入 ``sql`` 函数中, 可以很简单地表示多行查询.

   .. code-block:: scala

      // in Scala

      spark.sql("""
            SELECT user_id, department, first_name 
            FROM professors
            WHERE department IN (SELECT name FROM department WHERE created_date >= '2016-01-01')
      """)
   
   .. code-block:: python

      # in Python

      spark.sql("""
            SELECT user_id, department, first_name 
            FROM professors
            WHERE department IN (SELECT name FROM department WHERE created_date >= '2016-01-01')
      """)


示例 3:
   - 可以根据需要在 SQL 和 DataFrame 之间实现完全的互操作.

   .. code-block:: scala

      // in Scala

      // DataFrame => SQL
      spark.read.json("/data/flight-data/json/2015-summary.json")
         .createOrReplaceTempView("some_sql_view") 
      
      // SQL => DataFrame
      spark.sql("""
         SELECT DEST_COUNTRY_NAME, sum(count)
         FROM some_sql_view 
         GROUP BY DEST_COUNTRY_NAME
      """)
         .where("DEST_COUNTRY_NAME like 'S%'")
         .where("`sum(count)` > 10")
         .count()
   
   .. code-block:: python

      # in Python

      // DataFrame => SQL
      spark.read.json("/data/flight-data/json/2015-summary.json") \
         .createOrReplaceTempView("some_sql_view") 
      
      // SQL => DataFrame
      spark.sql("""
         SELECT DEST_COUNTRY_NAME, sum(count)
         FROM some_sql_view 
         GROUP BY DEST_COUNTRY_NAME
      """) \
         .where("DEST_COUNTRY_NAME like 'S%'") \
         .where("`sum(count)` > 10") \
         .count()


2.3 Spark SQL Thrift JDBC/ODBC 服务器
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Spark 提供了一个 Java 数据库连接 (JDBC) 接口, 通过它远程程序可以连接到 Spark 驱动器, 以便执行 Spark SQL 查询. 
此处实现的 Thrift JDBC/ODBC 服务器对应 Hive 1.2.1 中的 HiveServer2, 可以使用带有 Spark 或 Hive 1.2.1 的 beeline 脚本来测试 JDBC 服务器.

要启动 JDBC/ODBC 服务器, 需要在 Spark 目录下运行以下命令:

   .. code-block:: shell

      ./sbin/start-thriftserver.sh


.. note:: 

   - 上面的脚本支持全部的 ``bin/spark-submit`` 命令行选项.

   - 要查看配置此 Thrift 服务器的所有可用选项, 需要运行 ``./sbin/start-thriftserver.sh --help``.

   - 默认情况下, 服务器监听 ``localhost:10000``, 可以通过更改环境变量或系统属性来更新该监听地址和端口.
      
      - 对于环境变量配置:

      .. code-block:: shell

         export HIVE_SERVER2_THRIFT_PORT=<listening-port>
         export HIVE_SERVER2_THRIFT_BIND_HOST=<listening-host>
         ./sbin/start-thriftserver.sh \
            --master <master-uri> \
            ...
      
      - 对于系统属性:

      .. code-block:: shell

         ./sbin/start-thriftserver.sh \
            --hiveconf hive.server2.thrift.port=<listening-port> \
            --hiveconf hive.server2.thrift.bind.host=<listening-host> \
            --master <master-uri> \
            ...
      
      - 通过运行一下命令来测试侧连接

      .. code-block:: shell
         
         # beeline 将询问你的用户名和密码, 在非安全模式下, 只需要在计算机上输入用户名和一个空白密码即可,对于安全模式, 请按照 beeline 文档中给出的说明进行操作
         ./bin/beeline
      

3. Catalog
------------------

Spark SQL 中最高级别的抽象是 Catalog. 

Catalog 是一个抽象, 用于存储用户数据中的元数据以及其他有用的东西, 如:数据库、数据表、函数、视图. 
它在 ``org.apache.spark.sql.catalog.Catalog`` 包中, 它包含许多有用的函数, 用于执行诸如列举表、数据库和函数之类的操作.

对于用户来说, Catalog 具有自解释性, 它实际上只是 Spark SQL 的另一个编程接口. 
因此如果使用该编程接口, 需要将所有内容放在 ``spark.sql()`` 函数中以执行相关代码.

.. _header-n1009:

3.1 数据表
~~~~~~~~~~~~~~~~~~~~~

要使用 Spark SQL 来执行任何操作之前, 首先需要定义数据表, 数据表在逻辑上等同于 DataFrame, 因为他们都是承载数据的数据结构.

数据表和 DataFrame 的核心区别在于: 
   
   - DataFrame 是在编程语言范围内定义的
   
   - 数据表是在数据库中定义的

.. note:: 

   在 Spark 2.X 中, 数据表始终是实际包含数据的, 没有类似视图表的概念, 只有视图不包含数据, 这一点很重要, 因为如果要删除一个表, 那么可能会导致丢失数据.


3.2 Spark 托管表
~~~~~~~~~~~~~~~~~~~

表存储两类重要的信息, 表中的数据以及关于表的数据即元数据, Spark 既可以管理一组文件的元数据, 也可以管理实际数据.

   - 非托管表:

      - 当定义磁盘上的若干文件为一个数据表时, 这个就是非托管表.

   - 托管表:

      - 在 DataFrame 上使用 ``saveAsTable`` 函数来创建一个数据表时, 就是创建了一个托管表, Spark 将跟踪托管表的所有相关信息.

.. note:: 

   - 在 DataFrame 上使用 ``saveAsTable`` 函数将读取表并将其写入到一个新的位置(以 Spark 格式), 可以看到这也体现在新的解释计划中.在解释计划中, 你还会注意到这将写入到默认的 Hive 仓库位置. 可以通过 ``spark.SQL.warehouse.dir`` 为创建 ``SparkSession`` 时所选择的目录.默认情况下, Spark 将此设置为 ``/user/hive/warehouse``.

   - Spark 也有数据库, 需要提前说明的是, 可以在某个其他数据库系统中执行查询命令 ``show tables IN databaseName`` 来查看该数据库中的表, 其中 ``databaseName`` 表示要查询的数据库名称.

   - 如果在新的集群或本地模式下运行, 则不会返回结果.


.. _header-n1010:

3.3 Spark SQL 创建表
~~~~~~~~~~~~~~~~~~~~~~~~~~

可以从多种数据源创建表.Spark 支持在 SQL 中重用整个 Data Source API, 
这意味着不需要首先定义一个表再加载数据.Spark 允许从某数据源直接创建表, 
从文件中读取数据时, 甚至可以指定各种复杂的选项.

示例 1:
   - 读取文件数据并创建为一张表:

.. code:: sql

   CREATE TABLE flights (
       DEST_COUNTRY_NAME STRING, 
       ORIGIN_COUNTRY_NAME STRING, 
       count LONG
   )
   USING JSON OPTIONS (path "/data/flight-data/json/2015-summary.json")

.. note:: 

   USING 和 STORED AS:
      
      - USING 语法规范都具有重要意义. 如果未指定格式, 则 Spark 将默认为 Hive SerDe 配置, 但是 Hive SerDe 比 Spark 的本级序列化要慢的多.Hive 用户可以使用 STORED AS 语法来指定这是一个 Hive 表.

示例 2:
   - 可以向表中的某些列添加注释:

.. code:: sql

   CREATE TABLE flights_csv (
       DEST_COUNTRY_NAME STRING, 
       ORIGIN_COUNTRY_NAME STRING "remember, the US will be most prevalent", 
       count LONG
   )
   USING JSON OPTIONS (header ture, path "/data/flight-data/csv/2015-summary.csv")


示例 3:
   - 可以从查询结果创建表:


.. code:: sql

   CREATE TABLE flights_from_select USING parquet AS 
   SELECT * 
   FROM flights


- 只有表不存在时才能创建该表:

.. code-block:: sql

   CREATE TALBE IF NOT EXISTS flights_from_select AS 
   SELECT *
   FROM flights


.. note:: 

   在示例 3 的第二个示例中, 正在创建一个与 Hive 兼容的表, 因为我们没有通过 ``USING`` 显示地指定格式.


示例 4:
   - 可以通过写出已分区的数据集来控制数据布局, 这些表可以在整个 Spark 会话中使用, 而临时表不存在 Spark 中, 所以必须创建临时的视图:

.. code:: sql

   CREATE TABLE partitioned_flights USING parquet PARTITION BY (DEST_COUNTRY_NAME) AS 
   SELECT 
       DEST_COUNTRY_NAME, 
       ORIGIN_COUNTRY_NAME, 
       COUNTS 
   FROM flights
   LIMIT 5



.. _header-n1018:

3.4 Spark SQL 创建外部表
~~~~~~~~~~~~~~~~~~~~~~~~~~

Hive 是首批出现的面向大数据的 SQL 系统, 而 Spark SQL 与 Hive SQL(HiveQL) 完全兼容.

可能遇到的一种情况是, 将旧的 Hive 语句端口移植到 Spark SQL 中, 幸运的是, 
可以在大多数情况下直接将 Hive 语句复制并粘贴到 Spark SQL 中.

示例 1:
   - 创建一个非托管表, Spark 将管理表的元数据, 但是数据文件不是由 Spark 管理.可以使用 ``CREATE EXTERNAL TABLE`` 语句来创建此表:

.. code-block:: sql

   CREATE EXTERNAL TABLE hive_flights (
      DEST_COUNTRY_NAME STRING,
      ORIGIN_COUNTRY_NAME STRING,
      count LONG
   )
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LOCATION '/data/flight-data-hive/'

示例 2:
   - 可以从 ``SELECT`` 子句创建外部表:

.. code-block:: sql

   CREATE EXTERNAL TABLE hive_flights_2
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
   LOCATION '/data/flight-data-hive/' AS 
   SELECT *
   FROM flights

.. _header-n1019:

3.5 Spark SQL 插入表
~~~~~~~~~~~~~~~~~~~~~~~~~~

插入表操作遵循标准 SQL 语法:

示例 1:

.. code:: sql

   INSERT INTO flights_from_select
      SELECT 
         DEST_COUNTRY_NAME,
         ORIGIN_COUNTRY_NAME,
         COUNTS
      FROM flights
      LIMIT 20


示例 2:
   - 如果想要只写入某个分区, 可以选择提供分区方案:

.. code:: sql

   INSERT INTO partitioned_flights
      PARTITION (DEST_COUNTRY_NAME="UNITED STATES")
      SELECT 
         COUNTS,
         ORIGIN_COUNTRY_NAME
      FROM flights
      WHERE DEST_COUNTRY_NAME="UNITED STATES"
      LIMIT 12


.. note:: 

   写操作也将遵循分区模式, 可能导致上述查询运行相当缓慢, 它将其他文件只添加到最后的分区中.


.. _header-n1024:

3.6 Spark SQL 描述表的 Matadata
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

示例 1:
   - 可以通过描述数据表的元数据来显示相关注释:

.. code:: sql

   DESCRIBE TABLE flights_csv

示例 2:
   - 可以使用以下方法查看数据的分区方案(仅适用于已分区的表):

.. code-block:: sql

   SHOW PARTITIONS partitioned_flights


.. _header-n1026:

3.7 Spark SQL 刷新表的 Matadata
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

维护表的元数据确保从最新的数据集读取数据, 有两个命令可以刷新元数据：
   
   - ``REFRESH TABLE`` 用来刷新与表关联的所有缓存项(实质上是文件).如果之前缓存了该表, 则在下次扫描时会惰性缓存它.

   - ``REPAIR TABLE`` 用来刷新表在 catalog 中维护的分区. 此命令重点是收集新的分区信息.

示例 1:
   - 如果之前缓存了该表, 则在下次扫描时会惰性缓存它：

.. code:: sql

   REFRESH TABLE partitioned_flights

示例 2:
   - 可以手动写出新分区, 并相应地修复表：

.. code:: sql

   MSCK REPAIR TABLE partitioned_flights


.. _header-n1030:

3.8 Spark SQL 删除表
~~~~~~~~~~~~~~~~~~~~~~~~~~

不能删除表，只能 drop 它们，可以使用 ``DROP`` 关键字.

   - 如果 drop 托管表(managed table), 则表中的数据和表的定义都会被删除.

   - 当删除非托管表时, 表中的数据不会被删除, 但是不能够再引用原来表的名字对表进行操作.


示例 1:
   - 删除托管表 flights_csv

.. code:: sql

   DROP TABLE flights_csv;

   DROP TABLE IF EXISTS flights_csv;


示例 2:
   - 删除非托管表 flights_csv

.. code:: sql

   DROP TABLE flights;
   DROP TABLE IF EXISTS flights;


.. _header-n1038:

3.9 Caching 表
~~~~~~~~~~~~~~~~~~~~~~~~~~

示例 1:
   - 缓存表

.. code:: sql

   CACHE TABLE flights


示例 2:
   - 不缓存表

.. code:: sql

   UNCACHE TABLE flights

.. _header-n1042:

4. 视图 (views)
------------------

   -  A view specifies a set of transformations on top of an existing
      table-basically just saved query plans, which cna be convenient
      for organizing or resuing query logic.

   -  A view is effectively a transformation and Spark will perform it
      only at query time, views are equivalent to create a new DataFrame
      from an existing DataFrame.

.. _header-n1049:

4.1 创建视图
~~~~~~~~~~~~~~~

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

4.2 删除视图
~~~~~~~~~~~~~~~

.. code:: sql

   DROP VIEW IF EXISTS just_usa_view;

.. _header-n1059:

4.3 DataFrame 和 View
~~~~~~~~~~~~~~~~~~~~~~~~~

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

数据库是组织数据表的工具。如果没有一个提前定义好的数据库，Spark 将使用默认的数据库。

在 Spark 中执行的 SQL 语句(包括 DataFrame 命令)都在数据库的上下文中执行。
这意味着，如果更改数据库，那么用户定义的表都将保留在先前的数据库中，
并且需要以不同的方式进行查询.

.. _header-n1066:

5.1 创建数据库
~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: sql

   CREATE DATABASE some_db;

.. _header-n1067:

5.2 配置数据库
~~~~~~~~~~~~~~~~~~~~~~

示例 1:
   - 选择特定的数据库以执行查询

.. code-block:: sql

   USE some_db;

   SHOW tables

   -- fails with table/view not found
   SELECT *
   FROM flights


示例 2:
   - 可以使用前缀来标识数据库进行查询

.. code-block:: sql

   SELECT *
   FROM default.flights

示例 3:
   - 查看当前正在使用的数据库：

.. code-block:: sql

   SELECT current_database()

示例 4:
   - 切换回默认数据库：

.. code-block:: sql

   USE default;


.. _header-n1068:

5.3 删除数据库
~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: sql

   DROP DATABASE IF EXISTS some_db;


.. _header-n1070:

6. 数据查询语句
-------------------------

ANSI SQL

6.1 查询语句
~~~~~~~~~~~~~~~~~~~

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


- 其中:

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

6.2 CASE...WHEN...THEN...ELSE...END 语句
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: sql

   SELECT 
       CASE WHEN DEST_COUNTRY_NAME = 'UNITED STATES' THEN 1
            WHEN DEST_COUNTRY_NAME = 'Egypt' THEN 0
            ELSE -1 
       END
   FROM partitioned_flights



7. 复杂类型
---------------

7.1 结构体
~~~~~~~~~~~~~~~~


7.2 列表
~~~~~~~~~~~~~~~~




8. 函数
----------------


8.1 用户自定义函数
~~~~~~~~~~~~~~~~~~~~



9. 子查询
----------------

9.1 不相关谓词子查询
~~~~~~~~~~~~~~~~~~~~


9.2 相关谓词子查询
~~~~~~~~~~~~~~~~~~~~


9.3 不相关标量查询
~~~~~~~~~~~~~~~~~~~~




.. _header-n1104:

10. 其他
------------

10.1 配置
~~~~~~~~~~~~~~~~~~~~

Spark SQL 应用程序配置如下表，可以在应用程序初始化或应用程序执行过程中设置.


+----------------------------------------------+-----------------------+------------------------------+
| Property Name                                | Default               | Meaning                      |
+==============================================+=======================+==============================+
| spark.sql.inMemoryColumnarStorage.compressed | ``true``              |                              |
+----------------------------------------------+-----------------------+------------------------------+
| spark.sql.inMemoryColumnarStorage.batchSize  | ``10000``             |                              |
+----------------------------------------------+-----------------------+------------------------------+
| spark.sql.files.maxPartitionBytes            | ``134217728(128 MB)`` |                              |
+----------------------------------------------+-----------------------+------------------------------+
| spark.sql.files.openCostInBytes              | ``4194304(4MB)``      |                              |
+----------------------------------------------+-----------------------+------------------------------+
| spark.sql.broadcastTimeout                   | ``300``               |                              |
+----------------------------------------------+-----------------------+------------------------------+
| spark.sql.autoBroadcastJoinThreshold         | ``10485760(10 MB)``   |                              |
+----------------------------------------------+-----------------------+------------------------------+
| spark.sql.shuffle.partitions                 | ``200``               |                              |
+----------------------------------------------+-----------------------+------------------------------+


10.2 在 SQL 中设置配置值
~~~~~~~~~~~~~~~~~~~~~~~~

示例:
   - 从 SQL 中设置 shuffle 分区:

.. code-block:: sql

   SET spark.sql.shuffle.partitions=20

