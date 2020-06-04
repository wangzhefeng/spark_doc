.. _header-n0:


Spark 介绍
=========



Apache Spark
----------------

.. _header-n3:

1.Spark 的设计哲学和历史
~~~~~~~~~~~~~~~~~~~~~~~~~

Apache Spark is **a unified computing engine** and **a set of libraries
for parallel data processing(big data) on computer cluster**, and Spark
**support multiple widely used programming language** (Python, Java,
Scala, and R), and Spark **runs anywhere** from a laptop to a cluster of
thousand of servers. This makes it an easy system to start with and
scale-up to big data processing or incredibly large scale.

-  **A Unified Computing Engine**

   -  [Unified]

      -  Spark's key driving goal is to offer a unified platform for
         writing big data applications. Spark is designed to support a
         wide range of data analytics tasks, range from simple data
         loading and SQL queries to machine learning and streaming
         computation, over the same computing engine and with a
         consistent set of APIs.

   -  [Computing Engine]

      -  Spark handles loading data from storage system and performing
         computation on it, not permanent storage as the end itself, you
         can use Spark with a wide variety of persistent storage
         systems.

         -  cloud storage system

            -  Azure Stroage

            -  Amazon S3

         -  distributed file systems

            -  Apache Hadoop

         -  key-value stroes

            -  Apache Cassandra

         -  message buses

            -  Apache Kafka

-  **A set of libraries for parallel data processing on computer
   cluster**

   -  Standard Libraries

      -  SQL and sturctured data

         -  SparkSQL

      -  machine learning

         -  MLlib

      -  stream processing

         -  Spark Streaming

         -  Structured Streaming

      -  graph analytics

         -  GraphX

   -  `External Libraries <https://spark-packages.org/>`__ published as
      third-party packages by open source communities

.. _header-n73:

2.Spark 开发环境
~~~~~~~~~~~~~~~~~~~~~~~~~

-  Language API

   -  Python

   -  Java

   -  Scala

   -  R

   -  SQL

-  Dev Env

   -  local

      -  `Java(JVM) <https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html>`__

      -  `Scala <https://www.scala-lang.org/download/>`__

      -  `Python interpreter(version 2.7 or
         later) <https://repo.continuum.io/archive/>`__

      -  `R <https://www.r-project.org/>`__

      -  `Spark <https://spark.apache.org/downloads.html>`__

   -  web-based version in `Databricks Community
      Edition <https://community.cloud.databricks.com/>`__

.. _header-n107:

3.Spark's Interactive Consoles
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Python:

.. code:: shell

   ./bin/pyspark

Scala:

.. code:: shell

   ./bin/spark-shell

SQL:

.. code:: shell

   ./bin/spark-sql

.. _header-n114:

4.云平台、数据
~~~~~~~~~~~~~~~~~~~~~~~~~

-  `Project's
   Github <https://github.com/databricks/Spark-The-Definitive-Guide>`__

-  `Databricks <https://community.cloud.databricks.com/>`__

.. _header-n121:






Spark
----------

.. _header-n122:

1.Spark's Architecture
~~~~~~~~~~~~~~~~~~~~~~~~~

.. _header-n123:

**Cluster**

   Challenging: data processing

-  **Cluser(集群)**:

   -  Single machine do not have enough power and resources to perform
      computations on huge amounts of information, or the user probably
      dose not have the time to wait for the computationto finish;

   -  A cluster, or group, of computers, pools the resources of many
      machines together, giving us the ability to use all the cumulative
      resources as if they were a single computer.

   -  A group of machines alone is not powerful, you need a framework to
      coordinate work across them. Spark dose just that, managing and
      coordinating the execution of task on data across a cluster of
      computers.

-  **Cluster manager(集群管理器)**:

   -  Spark's standalone cluster manager

   -  YARN

   -  Mesos

.. _header-n145:

**Spark Application**

-  **Cluster Manager**

   -  A **Driver** process

      -  the heart of a Spark Appliction and maintains all relevant
         information during the lifetime of the application;

      -  runs ``main()`` functions;

      -  sits on a node in the cluster;

      -  responsible for:

         -  maintaining information about the Spark Application

         -  responding to user's program or input

         -  analyzing, distributing and scheduling work across the
            **executors**

   -  A Set of **Executor** process

      -  responsible for actually carrying out the work that the
         **driver** assigns them

      -  repsonsible for :

         -  executing code assigned to it by the driver

         -  reporting the state of the computation on that executor back
            to the dirver node

-  **Spark Application**

   -  Spark employs a **cluster manager** that keeps track of the
      **resources** available;

   -  The **dirver** process is responsible for executing the **dirver
      program's commands** across the **executors** to complete a given
      task;

      -  The executors will be running Spark code

.. _header-n193:

2.Spark's Language API
~~~~~~~~~~~~~~~~~~~~~~~~~

-  Scala

   -  Spark's "default" language.

-  Java

-  Python

   -  ``pyspark``

-  SQL

   -  Spark support a subset of the ANSI SQL 2003 standard.

-  R

   -  Spark core

      -  ``SparkR``

   -  R community-driven package

      -  ``sparklyr``

.. _header-n225:

3.Spark's API
~~~~~~~~~~~~~~~~~~~~~~~~~

**Spark has two fundamental sets of APIS:**

-  Low-level "unstructured" APIs

   -  RDD

   -  Streaming

-  Higher-level structured APIs

   -  Dataset

   -  DataFrame

      -  ``org.apache.spark.sql.functions``

      -  Partitions

      -  DataFrame(Dataset) Methods

         -  DataFrameStatFunctions

         -  DataFrameNaFunctions

      -  Column Methods

         -  alias

         -  contains

   -  Spark SQL

   -  Structured Streaming

.. _header-n265:

4.开始 Spark
~~~~~~~~~~~~~~~~~~~~~~~~~

-  启动 Spark's local mode、

   -  交互模式

      -  ``./bin/spark-shell``

      -  ``./bin/pyspark``

   -  提交预编译的 Spark Application

      -  ``./bin/spark-submit``

-  创建 ``SparkSession``

   -  交互模式，已创建

      -  ``spark``

   -  独立的 APP

      -  Scala:

         -  ``val spark = SparkSession.builder().master().appName().config().getOrCreate()``

      -  Python:

         -  ``spark = SparkSession.builder().master().appName().config().getOrCreate()``

.. _header-n304:

4.1 SparkSession
^^^^^^^^^^^^^^^^^^^^^^^^

   -  **Spark Application** controled by a **Driver** process called the
      **SparkSession**\ ；

   -  **SparkSession** instance is the way Spark executes user-defined
      manipulations across the cluster, and there is a one-to-one
      correspondence between a **SparkSession** and a **Spark
      Application**;

示例：

Scala 交互模式：

.. code:: shell

   # in shell
   $ spark-shell

.. code:: scala

   // in Scala
   val myRange = spark.range(1000).toDF("number")

Scala APP 模式：

.. code:: scala

   // in Scala
   import org.apache.spark.SparkSession
   val spark = SparkSession 
   	.builder()
   	.master()
   	.appName()
   	.config()
   	.getOrCreate()

Python 交互模式：

.. code:: shell

   # in shell
   $ pyspark

.. code:: python

   # in Pyton
   myRange = spark.range(1000).toDF("number")

Python APP 模式：

.. code:: python

   # in Python
   from pyspark import SparkSession
   spark = SparkSession \
   	.builder() \
   	.master() \
   	.appName() \
   	.config() \
   	.getOrCreate()

.. _header-n325:

4.2 DataFrames
^^^^^^^^^^^^^^^^^^^^^^^^

   -  A DataFrame is the most common Structured API;

   -  A DataFrame represents a table of data with rows and columns;

   -  The list of DataFrame defines the columns, the types within those
      columns is called the schema;

   -  Spark DataFrame can span thousands of computers:

   -  the data is too large to fit on one machine

   -  the data would simply take too long to perform that computation on
      one machine

.. _header-n344:

4.3 Partitions
^^^^^^^^^^^^^^^^^^^^^^^^

.. _header-n347:

4.4 Transformation
^^^^^^^^^^^^^^^^^^^^^^^^

.. _header-n348:

4.4.1 Lazy Evaluation
````````````````````````

.. _header-n349:

4.5 Action
^^^^^^^^^^^^^^^^^^^^^^^^

转换操作能够建立逻辑转换计划，为了触发计算，需要运行一个动作操作(action)。一个动作指示 Spark 在一系列转换操作后计算一个结果。





.. _header-n350:

4.6 Spark UI
^^^^^^^^^^^^^^^^^^^^^^^^


-  **Spark job** represents **a set of transformations** triggered by **an individual action**, and can monitor the Spark job from the Spark UI;
-  User can monitor the progress of a Spark job through the **Spark web UI**:
-  Spark UI is available on port ``4040`` of the **dirver node**;

   -  Local Mode: ``http://localhost:4040``

-  Spark UI displays information on the state of:

   -  Spark jobs

   -  Spark environment

   -  cluster state

   -  tunning

   -  debugging



4.7 一个 🌰
^^^^^^^^^^^^^^^^^^^^^^^^

(1) 查看数据集

.. code-block:: shell

   $ head /data/flight-data/csv/2015-summary.csv

(2) 读取数据集

.. code-block:: scala

   // in Scala
   val flightData2015 = spark
      .read
      .option("inferSchema", "true")
      .option("header", "true")
      .csv("/data/flight-data/csv/2015-summary.csv")

.. code-block:: python

   # in Python
   flightData2015 = spark \
      .read \
      .option("inferSchema", "true") \
      .option("header", "true") \
      .csv("/data/flight-data/csv/2015-summary.csv")

(3) 在数据上执行转换操作并查看 Spark 执行计划

.. code-block:: scala
   
   // in Scala
   // 转换操作 .sort()
   flightData2015.sort("count").explain()
   flightData2015.sort("count")


(4) 在数据上指定动作操作执行技术

.. code-block:: scala

   // in Scala
   // 配置 Spark shuffle
   spark.conf.set("spark.sql.shuffle.partitions", "5")
   // 动作操作 .take(n)
   flightData2015.sort("count").take(2)


(5) DataFrame 和 SQL

.. code-block:: scala

   // in Scala
   flightData2015.createOrReplaceTempView("flight_data_2015")


.. code-block:: scala
   
   // in Scala
   val sqlWay = spark.sql("""
      SELECT DEST_COUNTRY_NAME, count(1)
      FROM flight_data_2015
      GROUP BY DEST_COUNTRY_NAME
      """)

   val dataFrameWay = flightData2015
      .groupBy("DEST_COUNTRY_NAME")
      .count()
   
   sqlWay.explain()
   dataFrameWay.explain()


.. code-block:: python

   # in Python
   sqlWay = spark.sql("""
      SELECT DEST_COUNTRY_NAME, count(1)
      FROM flight_data_2015
      GROUP BY DEST_COUNTRY_NAME
      """)
   
   dataFrameWay = flightData2015 \
      .groupBy("DEST_COUNTRY_NAME") \
      .count()

   sqlWay.explain()
   dataFrameWay.explain()



.. code-block:: scala

   // in Scala
   spark.sql("""
      SELECT max(count) 
      FROM flight_data_2015
      """)
      .take(1)
   
   import org.apache.spark.sql.functions.max
   flightData2015
      .select(max("count"))
      .take(1)


.. code-block:: python

   // in Python
   spark.sql("""
      SELECT max(count)
      FROM flight_data_2015
      """) \
      .take(1)

   from pyspark.sql.functions import max
   flightData2015.select(max("count")).take(1)




Spark 工具
----------------


1.Spark 应用程序
~~~~~~~~~~~~~~~~~~~~~~

Spark 可以通过内置的命令行工具 ``spark-submit`` 轻松地将测试级别的交互程序转化为生产级别的应用程序.

通过修改 ``spark-submit`` 的 ``master`` 参数，可以将将应用程序代码发送到一个集群并在那里执行，应用程序将一直运行，直到正确退出或遇到错误。应用程序需要在集群管理器的支持下进行，常见的集群管理器有 Standalone，Mesos 和 YARN 等.

示例 1：
^^^^^^^

.. code-block:: shell

   ./bin/spark-submit \
      --class org.apache.spark.examples.SparkPi \      # 运行的类 
      --master local \                                 # 在本地机器上运行程序
      ./examples/jars/spark-examples_2.11-2.2.0.jar 10 # 运行的 JAR 包

示例 2：
^^^^^^^^^

.. code-block:: shell
   
   ./bin/spark-submit \
      -- master local \
      ./examples/src/main/python/pi.py 10


2.Dataset: 类型安全的结果化 API
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~



示例:
^^^^^^^^^

.. code-block:: scala

   case class Flight(DEST_COUNTRY_NAME: String, 
                     ORIGIN_COUNTRY_NAME: String,
                     count: BigInt)
   val flightDF = spark
      .read
      .parquet("/data/flight-data/parquet/2010-summary.parquet/")
   
   val flights = flightDF.as[Flight]

   flights
      .fliter(flight_row => flight_row.ORIGIN_COUNTRY_NAME != "Canada")
      .map(flight_row => flight_row)
      .take(5)
   
   flights
      .take(5)
      .filter(flight_row => flight_row.ORIGIN_COUNTRY_NAME != "Canada")
      .map(fr => Flight(fr.DEST_COUNTRY_NAME, fr.ORIGIN_COUNTRY_NAME, fr.count + 5))



3.Spark Structured Streaming
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Spark Structured Streaming(Spark 结构化流处理) 是用于数据流处理的高阶 API，
在 Spark 2.2 版本之后可用。可以像使用 Spark 结构化 API 在批处理模式下一样，
执行结构化流处理，并以流式方式运行它们，使用结构化流处理可以减少延迟并允许增量处理.
最重要的是，它可以快速地从流式系统中提取有价值的信息，而且几乎不需要更改代码。
可以按照传统批处理作业的模式进行设计，然后将其转换为流式作业，即增量处理数据，
这样就使得流处理变得异常简单.


数据集：https://github.com/databricks/Spark-The-Definitive-Guide/tree/master/data/retail-data

1.创建一个静态数据集 DataFrame 以及 Schema
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: scala

   // in Scala
   val staticDataFrame = spark
      .read
      .format("csv")
      .option("header", "true")
      .option("inferSchema", "true")
      .load("/data/retail-data/by-day/*.csv")
   
   staticDataFrame.createOrReplaceTempView("retail_data")
   cal staticSchema = staticDataFrame.schema

.. code-block:: python

   # in Python
   staticDataFrame = spark \
      .read \
      .format("csv") \
      .option("header", "true") \
      .option("inferSchema", "true") \
      .load("/data/retail-data/by-day/*.csv")
   
   staticDataFrame.createOrReplaceTempView("retail_data")
   staticSchema = staticDataFrame.schema


2.对数据进行分组和聚合操作
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: scala

   // in Scala
   import org.apache.spark.sql.functions.{window, column, desc, col}
   staticDataFrame
      .selectExpr(
         "CustomerId", 
         "(UnitPrice * Quantity) as total_cost", 
         "InvoiceDate"
      )
      .groupBy(
         col("CustomerId"), 
         window(col("InvoiceDate"), "1 day")
      )
      .sum("total_cost")
      .show(5)


.. code-block:: python

   # in Python
   from pyspark.sql.functions import window, column, desc, col
   staticDataFrame \
      .selectExpr(
         "CustomerId", 
         "(UnitPrice * Quantity) as total_cost", 
         "InvoiceDate"
      ) \
      .groupBy(
         col("CustomerId"), 
         window(col("InvoiceDate"), "1 day")
      ) \
      .sum("total_cost") \
      .show(5)


3.设置本地模型运行参数配置
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: scala

   // in Scala
   spark.conf.set("spark.sql.shuffle.partitions", "5")


.. code-block:: python

   # in Python
   spark.conf.set("spark.sql.shuffle.partitions", "5")



4.将批处理代码转换为流处理代码
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

(1)读取流式数据：

.. code-block:: scala

   // in Scala
   val streamingDataFrame = spark
      .readStream
      .schema(staticSchema)
      .option("maxFilesPerTrigger", 1)       // 指定一次应该读入的文件数量，在实际场景中被省略
      .format("csv")
      .option("header", "true")
      .load("/data/retail-data/by-day/*.csv")


.. code-block:: python

   # in Python
   streamingDataFrame = spark \
      .readStream \
      .schema(staticSchema) \
      .option("maxFilesPerTrigger", 1) \
      .format("csv") \
      .option("header", "true") \
      .load("/data/retail-data/by-day/*.csv")

(2)查看 DataFrame 是否代表流数据：

.. code-block:: scala

   // in Scala
   streamingDataFrame.isStreaming // 返回 true


.. code-block:: python

   # in Python
   streamingDataFrame.isStreaming # 返回 true


(3)对流式数据执行分组聚合操作(转换操作)

.. code-block:: scala

   # in Scala
   val purchaseByCustomerPerHour = streamingDataFrame
      .selectExpr(
         "CustomerId", 
         "(UnitPrice * Quantity) as total_cost", 
         "InvoiceDate"
      )
      .groupBy(
         $"CustomerId", 
         window($"InvoiceDate", "1 day")
      )
      .sum("total_cost")


.. code-block:: python

   # in Python
   purchaseByCustomerPerHour = streamingDataFrame \
      .selectExpr(
         "CustomerId", 
         "(UnitPrice * Quantity) as total_cost", 
         "InvoiceDate"
      ) \
      .groupBy(
         col("CustomerId"), 
         window(col("InvoiceDate"), "1 day")
      ) \
      .sum("total_cost") \
      .show(5)


(4)调用对流数据的动作操作，将数据缓存到内存中的一个表中，在每次被触发后更新这个内存缓存

.. code-block:: scala

   // in Scala
   purchaseByCustomerPerHour.writeStream
      .format("memory")               // memory 代表将表存入内存
      .queryName("customer_purchases") // 存入内存的表的名称
      .outputMode("complete")         // complete 表示保存表中所有记录
      .start()

.. code-block:: python

   # in Python
   purchaseByCustomerPerHour.writeStream \
      .format("memory") \
      .queryName("customer_purchases") \
      .outputMode("complete") \
      .start()

(5)运行查询调试结果

.. code-block:: scala

   // in Scala
   spark.sql("""
      SELECT * 
      FROM customer_purchases
      ORDER BY `sum(total_cost)` DESC
      """)
      .show(5)


.. code-block:: python

   # in Python
   spark.sql("""
      SELECT * 
      FROM customer_purchases
      ORDER BY `sum(total_cost)` DESC
      """) \
      .show(5)

(6)将结果输出到控制台

.. code-block:: scala

   // in Scala
   purchaseByCustomerPerHour.writeStream
      .format("console")
      .queryName("customer_purchases_2")
      .outputMode("complete")
      .start()


.. code-block:: python

   # in Python
   purchaseByCustomerPerHour.writeStream \
      .format("console") \
      .queryName("customer_purchases_2") \
      .outputMode("complete") \
      .start()


4.Spark 机器学习和高级数据分析
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


5.Spark 低阶 API
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Spark 中的所有对象都是建立在 RDD 之上的. Spark 的高阶 API 及所支持的高级操作都会被编译到较低级的 RDD 上执行，以方便和实现其较高效的分布式执行. 使用 RDD 可以并行化已经存储在驱动器机器内存中的原始数据.

大多数情况下用户只需要使用 Spark 的高阶 API 或高级操作就可以实现所需的业务逻辑，有时候可能需要使用 RDD，特别是在读取或操作原始数据(未处理或非结构化的数据)时.


示例 1:
^^^^^^^^^^^

.. code-block:: scala

   // in Scala
   spark.sparkContext.parallelize(Seq(1, 2, 3)).toDF() // 将 RDD 转化为 DataFrame

示例 2:
^^^^^^^^^^^

.. code-block:: python

   # in Python
   from pyspark.sql import Row

   spark.sparkContext.parallelize([Row(1), Row(2), Row(3)]).toDF()



6.SparkR
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

SparkR 是一个在 Spark 上运行的 R 语言工具，它具有与 Spark 其他支持语言相同的设计准则. SparkR 与 Spark 的 Python API 非常相似，在大多数情况下，SparkR 支持 Python 支持的所有功能

示例 1:
^^^^^^^^^
.. code-block:: r

   # in R
   library(SparkR)
   sparkDf <- read.df("/data/flight-data/csv/2015-summary.csv", source = "csv", header = "true", inferSchema = "true")
   take(sparkDF, 5)
   collect(orderBy(sparkDF, "count"), 20)

示例 2:
^^^^^^^^^

.. code-block:: r

   # in R
   library(magrittr)

   sparkDF %>% 
      orderBy(desc(sparkDF$count)) %>%
      groupBy("ORIGIN_COUNTRY_NAME") %>%
      count() %>%
      limit(10) %>%
      collect()




7.Spark 生态系统和工具包
~~~~~~~~~~~~~~~~~~~~~~~~~~

可以在 `Spark Packages 索引
<https://spark-packages.org>`_ 找到所有的开源社区维护的工具包，用户也可以将自己开发的工具包发布到此代码库中，也可以在 GitHub 上找到各种其他项目和工具包.