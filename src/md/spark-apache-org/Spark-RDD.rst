.. _header-n2:

Spark RDD
=========

.. _header-n3:

Spark 应用依赖
--------------

Spark 的 Maven 依赖：

.. code:: 

   groupId = org.apache.spark
   artifactId = spark-core_2.12
   version = 2.4.4

HDFS 集群的依赖:

.. code:: 

   groupId = org.apache.hadoop
   artifactId = hadoop-client
   version = <your-hdfs-version>

Spark 基本类：

.. code:: scala

   import org.apache.spark.SparkContext
   import org.apache.spark.SparkConf

.. _header-n11:

Spark 初始化
------------

-  创建 ``SparkContext`` 对象，用来连接到集群(cluster)

.. code:: scala

   val conf = new SparkConf().setAppName("appName").setMaster("master") // "local"
   val sc = new SparkContext(conf)

-  Shell

.. code:: shell

   $ ./bin/spark-shell --master local[4]
   $ ./bin/spark-shell --master local[4] --jars code.jar
   $ ./bin/spark-shell --master local[4] --packages "org.example:example:0.1"

.. _header-n20:

RDDs (Resilent Distributed Datasets)
------------------------------------

.. _header-n21:

创建 RDD
~~~~~~~~

创建 RDD 的方法：

-  并行化驱动程序中的已有数据集合

-  引用外部存储系统中的数据集

(1) 并行化驱动程序中的已有数据集合

.. code:: scala

   val conf = new SparkConf().setAppName("appName").setMaster("master") // "local"
   val sc = new SparkContext(conf)

   val data = Array(1, 2, 3, 4, 5)
   val distData = sc.parallelize(data, 10)

(2) 引用外部存储系统中的数据集

外部存储系统：

-  local file system

-  HDFS

-  Cassandra

-  HBase

-  Amazon S3

-  ...

数据类型：

-  text files

   -  csv

   -  tsv

   -  Plain Text

   -  ...

-  SequenceFiles

-  Hadoop InputFormat

.. code:: scala

   // text files
   val distFile = sc.textFile("data.txt")
   val data = sc.wholeTextFiles()

   // SequneceFiles
   val data = sc.sequenceFile[K, V]

   // Hadoop Input
   val data = sc.hadoopRDD()
   val data = sc.newAPIHadoopRDD()

.. code:: scala

   RDD.saveAsObjectFile()
   sc.objectFile()

.. _header-n64:

RDD 操作
~~~~~~~~
