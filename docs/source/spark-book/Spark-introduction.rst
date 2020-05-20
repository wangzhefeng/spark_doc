.. _header-n0:

(I) Apache Spark
================

.. _header-n3:

1.Spark 的哲学和历史
--------------------

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
----------------

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
------------------------------

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
--------------

-  `Project's
   Github <https://github.com/databricks/Spark-The-Definitive-Guide>`__

-  `Databricks <https://community.cloud.databricks.com/>`__

.. _header-n121:

(II) Spark
==========

.. _header-n122:

1.Spark's Architecture
----------------------

.. _header-n123:

**Cluster**
~~~~~~~~~~~

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
~~~~~~~~~~~~~~~~~~~~~

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
----------------------

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
-------------

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
------------

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
~~~~~~~~~~~~~~~~

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
~~~~~~~~~~~~~~

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
~~~~~~~~~~~~~~

.. _header-n347:

4.4 Transformation
~~~~~~~~~~~~~~~~~~

.. _header-n348:

4.5 Lazy Evaluation
~~~~~~~~~~~~~~~~~~~

.. _header-n349:

4.6 Action
~~~~~~~~~~

.. _header-n350:

4.7 Spark UI
~~~~~~~~~~~~

   -  **Spark job** represents **a set of transformations** triggered by
      **an individual action**, and can monitor the Spark job from the
      Spark UI;

   -  User can monitor the progress of a Spark job through the **Spark
      web UI**:

   -  Spark UI is available on port ``4040`` of the **dirver node**;

      -  Local Mode: ``http://localhost:4040``

   -  Spark UI displays information on the state of:

      -  Spark jobs

      -  Spark environment

      -  cluster state

      -  tunning

      -  debugging
