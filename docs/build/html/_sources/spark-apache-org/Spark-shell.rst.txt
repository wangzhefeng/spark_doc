.. _header-n0:

Spark Shell
===========

-  `Submitting
   Applications <https://spark.apache.org/docs/latest/submitting-applications.html>`__

.. _header-n7:

1.spark shell
-------------

.. _header-n8:

1.1 进入spark shell
~~~~~~~~~~~~~~~~~~~

.. code:: shell

   $ cd usr/lib/spark/bin # linux
   $ cd D:/spark/bin      # windows

**Python version:**

.. code:: shell

   $ pyspark --help
   $ pyspark --master --py-files --packages -- repositories
   $ pyspark                                               
   $ PYSPARK_PYTHON=python3.6 pyspark
   $ PYSPARK_DRIVER_PYTHON=ipython pyspark
   $ PYSPARK_DRIVER_PYTHON=jupyter PYSPARK_DRIVER_PYTHON_OPTS=notebook pyspark

**Scala version:**

.. code:: shell

   $ spark-shell --help
   $ spark-shell --master --jars --packages --repositories
   $ spark-shell

.. _header-n14:

1.2 Spark Shell默认环境
~~~~~~~~~~~~~~~~~~~~~~~

-  Spark context Web UI: http://192.168.0.111:4040

-  Spark context:
   ``sc (master = local[*], app id = local-1556297140303)``

-  Spark session: ``spark``

.. _header-n23:

2.运行python脚本
----------------

.. code:: shell

   # run Spark application in python without pip install PySpark(pip install pyspark)
   $ /bin/spark-submit my_script.py                          # python
   $ PYSPARK_PYTHOH=python3.6 /bin/spark-submit my_scrity.py # python with specify version

   # run Spark application in python with pip install PySpark(pip install pyspark)
   $ python my_script.py

.. _header-n26:

添加一个对于spark-core工件的Maven依赖 
--------------------------------------

.. code:: 

   // java & scala
   groupId = org.apache.spark
   artifactid = spark-core_2.10
   version = 2.3.0

.. _header-n28:

基本操作
--------

.. _header-n29:

Python Version
~~~~~~~~~~~~~~

.. _header-n30:

python基本操作
^^^^^^^^^^^^^^

.. code:: python

   # 创建一个DataFrame
   textFile = spark.read.text("README.md")

   # action, transformations
   textFile.count()
   textFile.first()

   # 转换为一个新的DataFrame
   lineWithSpark = textFile.filter(textFile.value.contains('Spark'))
   lineWithSpark.count()

   # or 
   textFile.filter(textFile.value.contains('Spark')).count()

   # Dataset Transform
   from pyspark.sql.functions import *
   wordCounts = textFile
   	.select(size(split(textFile.value, "\\s+")).name("numWords")) \
   	.agg(max(col("numWords"))) \
   	.collect()

   # MapReduce
   wordCounts = textFile \
   	.select(explode(split(textFile.value, "\\s+")).alias("word")) \
   	.groupBy("word")
   	.count()
   wordCounts.collect()

   # 缓存
   lineWithSpark.cache()
   lineWithSpark.count()
   lineWithSpark.count()

.. _header-n32:

Python App
^^^^^^^^^^

.. code:: python

   # setup.py
   install_requires=[
           'pyspark=={site.SPARK_VERSION}'
   ]


   # SimpleApp.py
   from pyspark.sql import SparkSession

   logFile = "D:/spark/README.md"  # Should be some file on your system
   spark = SparkSession.builder \
   	.appName("SimpleApp") \
   	.getOrCreate()
   logData = spark.read.text(logFile).cache()

   numAs = logData.filter(logData.value.contains('a')).count()
   numBs = logData.filter(logData.value.contains('b')).count()

   print("Lines with a: %i, lines with b: %i" % (numAs, numBs))

   spark.stop()

.. code:: shell

   # Use spark-submit to run your application
   $ D:/spark/bin/spark-submit --master local[4] SimpleApp.py

   # Use the Python interpreter to run your application(安装了PySpark pip: pip install pyspark)
   $ python SimpleApp.py

.. _header-n35:

Scala Version
~~~~~~~~~~~~~

.. _header-n36:

Scala基本操作
^^^^^^^^^^^^^

.. code:: scala

   // 创建一个Dataset
   val textFile = spark.read.textFile("README.md")

   // action, transformations
   textFile.count()
   textFile.first()


   // 转换为一个新的Dataset
   val linesWithSpark = textFile.filter(line => line.contains("Spark"))

   // or 

   textFile.filter(line => line.contains("Spark")).count()


   // Dataset transform
   textFile.map(line => line.split(" ").size).reduce((a, b) => if (a > b) a else b)

   // or

   import java.lang.Math
   textFile.map(line => line.split(" ").size).reduce((a, b) => Math.max(a, b))



   // MapReduce
   val wordCounts = textFile.flatMap(line => line.split(" ")).groupByKey(identity).count()
   wordCounts.collect()

   lineWithSpark.cache()
   lineWithSpark.count()
   lineWithSpark.count()

.. _header-n39:

Scala App
^^^^^^^^^

.. code:: scala

   /* SimpleApp.scala */
   import org.apache.spark.sql.SparkSession

   object SimpleApp {
     def main(args: Array[String]) {
       val logFile = "D:/spark/README.md" // Should be some file on your system
       val spark = SparkSession.builder.appName("Simple Application").getOrCreate()
       val logData = spark.read.textFile(logFile).cache()
       val numAs = logData.filter(line => line.contains("a")).count()
       val numBs = logData.filter(line => line.contains("b")).count()
       println(s"Lines with a: $numAs, Lines with b: $numBs")
       spark.stop()
     }
   }

sbt configuration file: build.sbt

.. code:: 

   name := "Simple Project"

   version := "1.0"

   scalaVersion := "2.11.12"

   libraryDependencies += "org.apache.spark" %% "spark-sql" % "2.4.0"

.. code:: shell

   # Your directory layout should look like this
   $ find .
   .
   ./build.sbt
   ./src
   ./src/main
   ./src/main/scala
   ./src/main/scala/SimpleApp.scala

   # Package a jar containing your application
   $ sbt package

   # Use spark-submit to run your application
   $ YOUR_SPARK_HOME/bin/spark-submit --class "SimpleApp" --master local[4] target/scala-2.11/simple-project_2.11-1.0.jar

.. _header-n46:

运行示例
--------

.. code:: shell

   # For Scala and Java, use run-example:
   ./bin/run-example SparkPi

   # For Python examples, use spark-submit directly:
   ./bin/spark-submit examples/src/main/python/pi.py

   # For R examples, use spark-submit directly:
   ./bin/spark-submit examples/src/main/r/dataframe.R
