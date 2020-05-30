.. _header-n2:

Spark SQL
=========

.. _header-n3:

package
-------

-  org.apache.spark.sql

   -  org.apache.spark.sql.SparkSession

      -  使用Dataset和DataFrame API进行Spark编程的主要入口

   -  org.apache.spark.sql.Dataset

   -  org.apache.spark.sql.DataFrame

.. _header-n20:

DataFrame 操作函数 API
----------------------

.. _header-n21:

1.1内置函数
~~~~~~~~~~~

.. code:: scala

   // API
   function.expr()

.. _header-n23:

1.2 DataFrame 操作函数
~~~~~~~~~~~~~~~~~~~~~~

   -  Annotations: @Stable()

   -  Source: functions.scala

   -  Since: 1.3.0

.. _header-n32:

基本操作函数
^^^^^^^^^^^^

-  .show()

.. code:: scala

   import org.apache.spark.sql.SparkSession

   object SparkSQLAggregateFunc {
   	def main(args: Array[String]) {
   		val spark = SparkSession
   			.builder
   			.appName("Spark SQL Aggregate Functions")
   			.config()
   			.getOrCreate()

   		val df = spark.read.json("")

   		// -------------------------------------------------
   		// functions
   		// -------------------------------------------------
   		df.show()
   	}
   }

.. _header-n39:

聚合函数(Aggregate functions)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: scala

   import org.apache.spark.sql.SparkSession

   object SparkSQLAggregateFunc {
   	def main(args: Array[String]) {
   		val spark = SparkSession
   			.builder
   			.appName("Spark SQL Aggregate Functions")
   			.config()
   			.getOrCreate()

   		val df = spark.read.json("")

   		// -------------------------------------------------
   		// functions
   		// -------------------------------------------------
   		// count()
   		// countDistinct()
   		// approx_count_distinct()
   		df..select("").filter("").groupBy("").count().show()
   		// sum()
   		// sumDistinct()
   		// avg()
   		// collect_list()
   		// collect_set()
   		// first()
   		// last()
   		// max()
   		// min()
   		// mean()
   		// variance()
   		// var_pop()
   		// var_samp()
   		// skewness()
   		// kurotsis()
   		// stddev()
   		// stddev_pop()
   		// stddev_samp()
   		// corr()
   		// covar_pop()
   		// covar_samp()
   		// grouping()
   		// grouping_id()
   	}
   }

.. _header-n41:

集合函数(Collection functions)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: scala

   import org.apache.spark.sql.SparkSession

   object SparkSQLAggregateFunc {
   	def main(args: Array[String]) {
   		val spark = SparkSession
   			.builder
   			.appName("Spark SQL Aggregate Functions")
   			.config()
   			.getOrCreate()

   		val df = spark.read.json("")

   		// -------------------------------------------------
   		// functions
   		// -------------------------------------------------
   		// array_contains()
   		
   	}
   }

.. _header-n44:

日期时间函数(Date Time functions)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. _header-n46:

数学函数(Math functions)
^^^^^^^^^^^^^^^^^^^^^^^^

-  abs()

.. code:: scala

   import org.apache.spark.sql.SparkSession

   object SparkSQLMathFunc {
   	def main(args: Array[String]) {
   		val spark = SparkSession
   			.builder
   			.appName("Spark SQL Math Functions")
   			.config()
   			.getOrCreate()

   		val df = spark.read.json("")

   		// abs()
   		df..select("").abs()
   	}
   }

.. _header-n53:

非聚合函数(Non-aggregate functions)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. _header-n55:

排序函数(Sorting functions)
^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. _header-n57:

字符函数(String functions)
^^^^^^^^^^^^^^^^^^^^^^^^^^

.. _header-n59:

UDF函数(UDF functions)
^^^^^^^^^^^^^^^^^^^^^^

.. _header-n61:

窗口函数(Window functions)
^^^^^^^^^^^^^^^^^^^^^^^^^^

-  rank()

-  dense_rank()

-  percent_rank()

-  row_number()

.. _header-n72:

Dataset API
-----------

   A Dataset is a strongly typed collection of domain-specific objects
   that can be transformed in parallel using functional or relational
   operations. Each Dataset also has an untyped view called a DataFrame,
   which is a Dataset of Row.

Dataset操作：

-  transformations

   -  map

   -  filter

   -  select

   -  aggregate

      -  groupBy

-  actions

   -  count

   -  show

   -  writting data out to file systems

-  Dataset是惰性的(lazy)

   -
