.. _header-n0:

Spark Structured API 
=====================

.. _header-n3:

1.Spark Structured API
----------------------

-  Spark is a **distributed programming model** in which the user
   specifies ``transformations``.

   -  Multiple ``transformations`` build up a **directed acyclic graph**
      of instructions.

   -  An ``action`` begins the process of executing that graph of
      instructions, as a single **job**, by breaking it down into
      **stages** and **tasks** to execute across the **cluster**.

-  The logical structures that we manipulate with ``transformations``
   and ``actions`` are ``DataFrame`` and ``Datasets``.

   -  To create a new DataFrame and Dataset, you call a
      ``transformation``.

   -  To start computation(cluster computation) or convert to native
      language types(spark types), you call an ``action``.

.. _header-n19:

1.1 Dataset 和 DataFrame
~~~~~~~~~~~~~~~~~~~~~~~~

DataFrames and Datasets are distributed table-like with well-defined
rows and columns.

-  Each column must have the same number of rows as all the other
   columns;

-  Each column has type information that must be consistent for every
   row in the collection;

DataFrame and Datasets represent **immutable**, **lazily evaluated
plans** that specify what operations to apply to data residing at a
location to generate some output. When we perform an action on a
DataFrame, we instruct Spark to perform the actual transformations and
return the result.

.. _header-n28:

1.2 Schema
~~~~~~~~~~

A schema defines the column names and types of a DataFrame, define
schemas:

-  manually

-  read a schema from a data source(schema on read)

.. _header-n35:

1.3 Structured Spark Types
~~~~~~~~~~~~~~~~~~~~~~~~~~

   -  Spark is effectively a **programming language** of its own.

   -  Spark uses an engine called **Catalyst** that maintains its own
      type information through the planning and processing of work. This
      open up a wide variety of execution optimizations that make
      significant differences.

   -  Even if we use Spark's Structured APIs from Python or R, the
      majority of our manipulations will operate strictly on **Spark
      types**, not Python types.

**实例化或声明一个特定类型的列：**

.. code:: scala

   // in Scala
   import org.apache.spark.sql.types._
   val a = ByteType

.. code:: java

   // in Java
   import org.apache.spark.sql.types.DataTypes;
   ByteType a = DataTypes.ByteType;

.. code:: python

   # in Python
   from pyspark.sql.types import *
   a = ByteType()

**Spark Internal Types:**

+-----------------------+-----------------------+-----------------------+
| Spark数据类型         | Scala数据类型         | 创建数据类型实例的API |
+=======================+=======================+=======================+
| ByteType              | Byte                  | ByteType              |
+-----------------------+-----------------------+-----------------------+
| ShortType             | Short                 | ShortType             |
+-----------------------+-----------------------+-----------------------+
| IntegerType           | Int                   | IntegerType           |
+-----------------------+-----------------------+-----------------------+
| LongType              | Long                  | LongType              |
+-----------------------+-----------------------+-----------------------+
| FloatType             | Float                 | FloatType             |
+-----------------------+-----------------------+-----------------------+
| DoubleType            | Double                | DoubleType            |
+-----------------------+-----------------------+-----------------------+
| DecimalType           | java.math.BitgDecimal | DecimalType           |
+-----------------------+-----------------------+-----------------------+
| StringType            | String                | StringType            |
+-----------------------+-----------------------+-----------------------+
| BinaryType            | Array[Byte]           | BinaryType            |
+-----------------------+-----------------------+-----------------------+
| BooleanType           | Boolean               | BooleanType           |
+-----------------------+-----------------------+-----------------------+
| TimestampType         | java.sql.Timestamp    | TimestampType         |
+-----------------------+-----------------------+-----------------------+
| DateType              | java.sql.Date         | DateType              |
+-----------------------+-----------------------+-----------------------+
| ArrayType             | scala.collection.Seq  | ArrayType(elementType |
|                       |                       | ,                     |
|                       |                       | [containsNull =       |
|                       |                       | true])                |
+-----------------------+-----------------------+-----------------------+
| MapType               | scala.collection.Map  | MapType(keyType,      |
|                       |                       | valueType,            |
|                       |                       | [valueContainsNull =  |
|                       |                       | true])                |
+-----------------------+-----------------------+-----------------------+
| StructType            | org.apache.spark.sql. | StructType(field)     |
|                       | Row                   |                       |
+-----------------------+-----------------------+-----------------------+
| StructField           | Int for a StructField | StructField(name,     |
|                       | with the data type    | dataType, [nullable = |
|                       | IntegerType,...       | true])                |
+-----------------------+-----------------------+-----------------------+

.. _header-n118:

1.4 Structured API Execution
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

   -  How code is actually executed across a cluster ?

      -  (1)Write DataFrame/Dataset/SQL Code;

      -  (2)If vaild code, Spark converts Code to a **Logistic Plan**;

      -  (3)Spark transforms this **Logistic Plan** to **Physical
         Plan**, checking for optimizations along the way;

      -  (4)Spark then executes this **Physical Plan**\ (RDD
         manipulations) on the cluster;

To execute code, must write code. This code is then submitted to Spark
either through the console or via a submitted job. This code the passes
through the Catalyst Optimizer, which decides how the code should be
executed and lays out a plan for doing so before, finally, the code is
run and the result is returned to the user.

.. _header-n134:

1.4.1 Logical Planning
^^^^^^^^^^^^^^^^^^^^^^

   Spark uses the **catalog**, a repository of all table and DataFrame
   information, to resolve columns and tables in the **analyzer**.

-  User Code

-  Unresolved logical plan

   -  Catalog

   -  Analyzer

-  Resolved logical plan

   -  Logical Optimization

-  Optimized logical plan

.. _header-n155:

1.4.2 Physical Planning
^^^^^^^^^^^^^^^^^^^^^^^

-  Physical Planning, often called a Spark Plan, specifies how the
   logical planning will execute on the cluster by generating different
   physical execution strategies and comparing them through a cost
   model.

-  Physical planning results in a series of RDDs an transformations.

   -  Spark referred to as a compiler: it takes queries in DataFrames,
      Datasets, SQL an compiles them into RDD transformations.

.. _header-n166:

1.4.3 Execution
^^^^^^^^^^^^^^^

-  selecting a physical plan

-  run code over RDDs

-  perform optimizations

-  generating native Java bytecode

-  return the result to user

.. _header-n179:

2.DataFrame
-----------

-  A DataFrame consists of a series of **records** (like row in a
   table), that are of type ``Row``, and a number of **columns** (like
   columns in a spreadsheet) that represent a computation expression
   that can be preformed on each individual record in the Dataset.

   -  Schema 定义了 DataFrame 中每一列数据的名字和类型；

   -  DataFrame 的 Partitioning 定义了 DataFrame 和 Dataset
      在集群上的物理分布结构；

   -  Partitioning schema defines how Partitioning of the DataFrame is
      allocated;

   -  DataFrame operations:

      -  aggregations

      -  window functions

      -  joins

.. code:: scala

   // in Scala
   val df = spark.read.format("josn")
       .load("/data/flight-data/json/2015-summary.json")
   // 查看DataFrame的schema
   df.printSchema()

.. code:: python

   # in Python
   df = spark.read.format("json") \
       .load("/data/flight-data/json/2015-summary.json")

   // 查看DataFrame的schema
   df.printSchema()

.. _header-n201:

2.1 Schemas
~~~~~~~~~~~

   -  Schema 定义了 DataFrame 中每一列数据的\ *名字*\ 和\ *类型*\ ；

   -  为 DataFrame 设置 Schema 的方式：

      -  使用数据源已有的 Schema (schema-on-read)

      -  使用 ``StructType``, ``StructField`` 自定义 DataFrame 的
         Schema；

   -  A Schema is a ``StructType`` made up of a number of fields,
      ``StructField``, that have a ``name``, ``type``, a
      ``Boolean flag`` which specifies whether that column can contain
      missing of null values, and finally, user can optionally specify
      associated ``Metadata`` with that column. The Metadata is a way of
      storing information about this column.

   -  如果程序在运行时，DataFrame 中 column 的 ``type`` 没有与
      预先设定的 Schema 相匹配，就会抛出错误；

**(1) 使用数据源已有的 Schema (schema-on-read):**

.. code:: scala

   // in Scala
   spark.read.format("json")
       .load("/data/flight-data/json/2015-summary.json")
       .schema

.. code:: python

   # in Python
   spark.read.format("json") \
       .load("/data/flight-data/json/2015-summary.json")
       .schema

**(2)使用 ``StructType``, ``StructField`` 自定义 DataFrame 的 Schema**

.. code:: scala

   // in Scala
   import org.apache.spark.sql.types.{StructField, StructType, StringType, LongType}
   import org.apache.spark.sql.types.Metadata

   val myManualSchema = StructType(Array(
       StructField("DEST_COUNTRY_NAME", StringType, true),
       StructField("ORIGIN_COUNTRY_NAME", StringType, true),
       StructField("COUNT", LongType, false)
   ))

   val df = spark.read.format("json")
       .schema(myManualSchema)
       .load("/data/flight-data/json/2015-summary.json")

.. code:: python

   # in Python
   from pyspark.sql.types import StructType, StructField, StringType, LongType

   myManualSchema = StructType([
       StructField("DEST_COUNTRY_NAME", StringType(), True),
       StructField("ORIGIN_COUNTRY_NAME", StringType(), True),
       StructField("COUNT", LongType(), False)
   ])

   df = spark.read.format("json") \
       .schema(myManualSchema) \
       .load("/data/flight-data/json/2015-summary.json")

.. _header-n223:

2.2 Columns 和 Expressions
~~~~~~~~~~~~~~~~~~~~~~~~~~

   -  Spark 中的 columns 就像 spreadsheet，R dataframe, Pandas DataFrame
      中的列一样；可以对 Spark 中的 columns
      进行\ **选择，操作，删除**\ 等操作，这些操作表现为 expression
      的形式；

   -  在 Spark 中来操作 column 中的内容必须通过 Spark DataFrame 的
      transformation进行；

.. _header-n230:

2.2.1 创建和引用 Columns
^^^^^^^^^^^^^^^^^^^^^^^^

-  有很多种方法来创建和引用 DataFrame 中的 column:

   -  函数(function):

      -  ``col()``

      -  ``column()``

   -  符号(Scala独有的功能，无性能优化):

      -  ``$"myColumn"``

      -  ``'myColumn``

   -  DataFrame 的方法(explicit column references):

      -  ``df.col("myColumn")``

.. code:: scala

   // in Scala
   import org.apache.spark.sql.function.{col, column}

   col("someColumnName")
   column("someColumnName")
   $"someColumnName"
   'someColumnName
   df.col("someColumnName")

.. code:: python

   # in Python
   from pyspark.sql.function import col, column

   col("someColumnName")
   column("someColumnName")
   df.col("someColumnName")

.. _header-n256:

2.2.2 Expressions 
^^^^^^^^^^^^^^^^^^

   -  Columns are expressions；

   -  An expression is a set of transformations on one or more values in
      a records in a DataFrame；

   -  通过函数创建的 expression：\ ``expr()``\ ，仅仅是对 DataFrame 的
      columns 的 reference；

      -  ``expr("someCol")`` 等价于 ``col("someCol")``

      -  ``col()`` 对 columns 进行 transformation 操作时，必须作用在
         columns 的引用上；

      -  ``expr()`` 会将一个 string 解析为 transformations 和 columns
         references，并且能够继续传递给transformations；

   -  Columns are just expressions;

   -  Columns and transformations of those columns compile to the same
      logical plan as parsed expression;

**Column as Expression:**

.. code:: scala

   // 下面的3个表达式是等价的 transformation
   // Spark 会将上面的三个 transformation 解析为相同的逻辑树(logical tree)来表达操作的顺序；

   import org.apache.spark.sql.functions.expr
   expr("someCol - 5")
   col("someCol") - 5
   expr("someCol") - 5

.. code:: scala

   // in Scala
   import org.apache.spark.sql.functions.expr
   expr("(((someCol + 5) * 200) - 6) < otherCol")

.. code:: python

   from pyspark.sql.functions import expr
   expr("(((someCol + 5) * 200) - 6) < otherCol")

**查看 DataFrame 的 Columns:**

.. code:: scala

   spark.read.format("json")
       .load("/data/flight-data/json/2015-summary.json")
       .columns

.. _header-n282:

2.3 Records 和 Rows
~~~~~~~~~~~~~~~~~~~

   -  在 Spark 中，DataFrame 中的每一个 row 都是一个 record； Spark
      使用一个 ``Row`` 类型的对象表示一个 record; Spark 使用 column
      expression 来操作类型为 ``Row`` 的对象；

   -  ``Row`` 类型的对象在 Spark内部表现为\ **字节数组(array of bytes)**

查看 DataFrame 的第一行：

.. code:: scala

   df.first()

.. _header-n291:

2.3.1 创建 Rows
^^^^^^^^^^^^^^^

   -  通过实例化一个 Row 对象创建；

   -  通过实例化手动创建的 Row 必须与 DataFrame 的 Schema
      中定义的列的内容的顺序一致，因为只有 DataFrame 有 Schema，而 Row
      是没有 Schema的；

.. code:: scala

   // in Scala
   import org.apache.spark.sql.Row
   val myRow = Row("Hello", null, 1, false)

   // 在 Scala 中通过索引获得 Row 中的值，但必须通过其他的帮助函数强制转换 Row 中的数据的类型才能得到正确的值的类型
   myRow(0)                      // type Any
   myRow(0).asInstanceOf[String] // String
   myRow.getString(0)            // String
   myRow.getInt(2)               // Int

.. code:: python

   # in Python
   from pyspark.sql import Row
   myRow = Row("Hello", null, 1, False)

   # 在 Python 中通过索引获得 Row 中的值，但不需要强制转换
   myRow[0]
   myRow[1]
   myRow[2]

.. _header-n300:

2.4 DataFrame transformations
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

DataFrame 上可以通过 ``transformation`` 进行的操作：

   -  增：add rows or columns

   -  删：remove rows or columns

   -  行转列：transform rows into column(or vice versa)

   -  排序：change the order of rows based on the values in columns

DataFrame transformation 方法和函数:

   -  ``select`` method

      -  working with "columns or expressions"

   -  ``selectExpr`` method

      -  working with "expressions in string"

   -  Package: ``org.apache.spark.sql.functions``

.. _header-n327:

2.4.1 创建 DataFrame
^^^^^^^^^^^^^^^^^^^^

   1. 从原始数据源创建 DataFrame；

      -  将创建的 DataFrame
         转换为一个临时视图，使得可以在临时视图上进行SQL转换操作；

   2. 手动创建一个行的集合并，将这个集合转换为 DataFrame；

**从原始数据源创建 DataFrame:**

.. code:: scala

   // in Scala
   val df = spark.read.format("json")
       .load("/data/flight-data/json/2015-summary.json")

   df.createOrReplaceTempView("dfTable")

.. code:: python

   # in Python
   df = spark.read.format("json") \
       .load("/data/flight-data/json/2015-summary.json")

   df.createOrReplaceTempView("dfTable")

**通过 Row 的集合创建 DataFrame:**

.. code:: scala

   // in Scala
   import org.apache.spark.sql.Row
   import org.apache.spark.sql.types.{StructType, StructField, StringType, LongType}

   // Schema
   val myManualSchema = new StructType(Array(
       new StructField("DEST_COUNTRY_NAME", StringType, true),
       new StructField("ORIGIN_COUNTRY_NAME", StringType, true),
       new StructField("COUNT", LongType, false)
   ))

   // Row
   val myRows = Seq(Row("Hello", null, 1L))
   // RDD
   val myRDD = spark.sparkContext.parallelize(myRows)
   // DataFrame
   val myDf = spark.createDataFrame(myRDD, myManualSchema)

   myDF.show()

.. code:: python

   # in Python
   from pyspark.sql import Row
   from pyspark.sql.types import StructType, StructField, StringType, LongType

   # Schema
   myManualSchema = StructType([
       StructField("DEST_COUNTRY_NAME", StringType(), True),
       StructField("ORIGIN_COUNTRY_NAME", StringType(), True),
       StructField("COUNT", LongType(), False)
   ])

   # Row
   myRows = Row("Hello", None, 1)
   # DataFrame
   myDf = spark.createDataFrame([myRows], myManualSchema)

   myDf.show()

.. _header-n343:

2.4.2 select 和 selectExpr
^^^^^^^^^^^^^^^^^^^^^^^^^^

   -  ``.select()`` 和 ``.selectExpr()`` 与 SQL
      进行查询的语句做同样的操作；

.. _header-n349:

2.4.2.1 方法：\ ``.select()``
'''''''''''''''''''''''''''''

.. code:: scala

   // in Scala
   import org.apache.spark.sql.functions.{expr, col, column}

   df.select("DEST_COUNTRY_NAME")
     .show(2)

   df.select("DEST_COUNTRY_NAME", "ORIGIN_COUNTRY_NAME")
     .show(2)


   // different ways to refer to columns

   df.select(
       df.col("DEST_COUNTRY_NAME"),
       col("DEST_COUNTRY_NAME"),
       column("DEST_COUNTRY_NAME"),
       'DEST_COUNTRY_NAME,
       $"DEST_COUNTRY_NAME",
       expr("DEST_COUNTRY_NAME"))
     .show(2)

   // Rename column
   df.select(expr("DEST_COUNTRY_NAME AS destination"))
     .show(2)

   df.select(expr("DEST_COUNTRY_NAME AS destination").alias("DEST_COUNTRY_NAME"))
     .show(2)

.. code:: python

   # in Python
   from pyspark.sql.functions import epxr, col, column

   df.select("DEST_COUNTRY_NAME") \
     .show(2)

   df.select("DEST_COUNTRY_NAME", "ORIGIN_COUNTRY_NAME") \
     .show(2)

   # different ways to refer to columns
   df.select(
       expr("DEST_COUNTRY_NAME"),
       col("DEST_COUNTRY_NAME"),
       column("DEST_COUNTRY_NAME")) \
     .show()

   # Rename column
   df.select(expr("DEST_COUNTRY_NAME AS destination")) \
     .show(2)

   df.select(expr("DEST_COUNTRY_NAME AS destination").alias("DEST_COUNTRY_NAME")) \
       .show(2)

.. code:: sql

   -- in SQL
   SELECT 
       DEST_COUNTRY_NAME
   FROM dfTable
   LIMIT 2

   SELECT 
       DEST_COUNTRY_NAME,
       ORIGIN_COUNTRY_NAME
   FROM dfTable
   LIMIT 2


   -- Rename column 
   SELECT 
       DEST_COUNTRY_NAME AS destination
   FROM dfTable
   LIMIT 2

.. _header-n353:

2.4.2.2 方法：\ ``.selectExpr():``
''''''''''''''''''''''''''''''''''

.. code:: scala

   // in Scala
   df.selectExpr("DEST_COUNTRY_NAME AS newColumnName", "DEST_COUNTRY_NAME")
     .show()

   df.selectExpr(
       "*",
       "(DEST_COUNTRY_NAME = ORIGIN_COUNTRY_NAME) AS withinCountry")
     .show(2)

   df.selectExpr(
       "avg(count)", 
       "count(distinct(DEST_COUNTRY_NAME))")
     .show()

.. code:: python

   # in Python
   df.selectExpr("DEST_COUNTRY_NAME AS newColumnName", "DEST_COUNTRY_NAME") \
     .show()

   df.selectExpr(
       "*",
       "(DEST_COUNTRY_NAME = ORIGIN_COUNTRY_NAME) AS withinCountry") \
     .show(2)

   df.selectExpr("avg(count)", "count(distinct(DEST_COUNTRY_NAME))") \
     show()

.. code:: sql

   -- in SQL
   SELECT 
       DEST_COUNTRY_NAME AS newColumnName,
       DEST_COUNTRY_NAME
   FROM dfTable
   LIMIT 2

   SELECT 
       *,
       (DEST_COUNTRY_NAME = ORIGIN_COUNTRY_NAME) AS withinCountry
   FROM dfTable
   LIMIT 2

   SELECT 
       AVG(count),
       COUNT(DISTINCT(DEST_COUNTRY_NAME))
   FROM dfTable

.. _header-n357:

2.4.3 Spark 字面量(Literals)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   -  A translation from a given programming language's literal value to
      one that Spark unstandstand；

   -  Literals 是表达式(expression)；

.. code:: scala

   // in Scala
   import org.apache.spark.sql.functions.{expr, lit}
   df.select(expr("*"), lit(1).as(One)).show(2)

.. code:: python

   # in Python
   from pyspark.sql.functions import expr, lit
   df.select(expr("*"), lit(1).alias("One")).show(2)

.. code:: sql

   -- in SQL
   SELECT 
       *, 
       1 AS One
   FROM dfTable
   LIMIT 2

.. _header-n367:

2.4.4 增加 Columns、 重命名 Columns
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   -  ``.withColumn()``

   -  ``.withColumnRenamed()``

.. code:: scala

   // in Scala
   import org.apache.spark.sql.functions.expr

   df.withColumn("numberOne", lit(1)).show(2)

   df.withColumn("withinCountry", expr("DEST_COUNTRY_NAME = ORIGIN_COUNTRY_NAME"))
     show(2)

   // rename column
   df.withColumn("destination", expr("DEST_COUNTRY_NAME"))
     .show(2)

   df.withColumnRenamed("DEST_COUNTRY_NAME", "dest")
     .show(2)

.. code:: python

   # in Python
   from pyspark.sql.functions import expr

   df.withColumn("numberOne", lit(1)) \
     .show(2)

   df.withColumn("withinCountry", expr("DEST_COUNTRY_NAME = ORIGIN_COUNTRY_NAME")) \
     .show(2)

   # rename column
   df.withColumn("destination", expr("DEST_COUNTRY_NAME")) \
     .show(2)

   df.withColumnRenamed("DEST_COUNTRY_NAME", "dest") \
     .show(2)

.. _header-n376:

2.4.5 转义字符和关键字(reserved characters and keywords)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: scala

   // in Scala
   import org.apache.spark.sql.functions.expr

   // rename column "ORIGIN_COUNTRY_NAME"
   val dfWithLongColName = df.withColumn(
       "This Long Column-Name",
       expr("ORIGIN_COUNTRY_NAME"))


   dfWithLongColName.selectExpr(
       "`This Long Column-Name`",
       "`This Long Column-Name` as `new col`")
       .show()

   dfWithLongColName.select(expr("`This Long Column-Name`"))
       .columns

   dfWithLongColName.select(col("This Long Column-Name"))
       .columns

.. code:: python

   # in Python
   from pyspark.sql.functions import expr

   # rename column "ORIGIN_COUNTRY_NAME"
   dfWithLongColName = df.withColumn(
       "This Long Column-Name",
       expr("ORIGIN_COUNTRY_NAME"))

   dfWithLongColName.selectExpr(
       "`This Long Column-Name`",
       "`This Long Column-Name` as `new col`") \
       .show(2)

   dfWithLongColName.select(expr("`This Long Column-Name`")) \
       .columns

   dfWithLongColName.select(col("This Long Column-Name")) \
       .columns

.. code:: sql

   SELECT 
       `This Long Column-Name`,
       `This Long Column-Name` AS `new col`
   FROM dfTableLong
   LIMIT 2

.. _header-n380:

2.4.6 Case Sensitivity
^^^^^^^^^^^^^^^^^^^^^^

   Spark 默认是大小写不敏感的，即不区分大小写；

.. code:: sql

   -- in SQL
   set spark.sql.caseSensitive true

.. _header-n384:

2.4.7 删除 Columns
^^^^^^^^^^^^^^^^^^

   -  ``.select()``\ ；

   -  ``.drop()``\ ；

.. code:: scala

   // in Scala
   df.drop("ORIGIN_COUNTRY_NAME")
     .columns

   dfWithLongColName.drop("ORIGIN_COUNTRY_NAME", "DEST_COUNTRY_NAME")

.. code:: python

   # in Python
   df.drop("ORIGIN_COUNTRY_NAME") \
     .columns

   dfWithLongColName.drop("ORIGIN_COUNTRY_NAME", "DEST_COUNTRY_NAME")

.. _header-n394:

2.4.8 改变 Columns 的类型(cast)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   -  ``.cast()``

.. code:: scala

   // in Scala
   df.withColumn("count2", col("count").cast("long"))

.. code:: python

   # in Python
   df.withColumn("count2", col("count").cast("long"))

.. code:: sql

   -- in SQL
   SELECT 
       *,
       CAST(count as long) AS count2
   FROM dfTable

.. _header-n403:

2.4.9 筛选行
^^^^^^^^^^^^

.. code:: scala

   // in Scala
   df.filter(col("count") < 2)
     .show(2)

   df.where("count" < 2)
     .show(2)

   df.filter(col("count") < 2)
     .filter(col("ORIGIN_COUNTRY_NAME") =!= "Croatia")
     .show(2)

.. code:: python

   # in Python
   df.filter(col("count") < 2) \
     .show(2)

   df.where("count" < 2) \
     .show(2)

   df.filter(col("count") < 2) \
     .filter(col("ORIGIN_COUNTRY_NAME") =!= "Croatia") \
     .show(2)

.. code:: sql

   -- in SQL
   SELECT 
       *
   FROM dfTable
   WHERE count < 2
   LIMIT 2

   SELECT 
       *
   FROM dfTable
   WHERE count < 2 AND ORIGIN_COUNTRY_NAME != 'Croatia'
   LIMIT 2

.. _header-n408:

2.4.10 获取不重复(Unique/Distinct)的行
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: scala

   df.select("ORIGIN_COUNTRY_NAME", "DEST_COUNTRY_NAME")
     .distinct()
     .count()

.. code:: python

   df.select("ORIGIN_COUNTRY_NAME", "DEST_COUNTRY_NAME") \
     .distinct() \
     .count() \

.. code:: sql

   SELECT
       COUNT(DISTINCT(ORIGIN_COUNTRY_NAME, DEST_COUNTRY_NAME))
   FROM dfTable

.. _header-n412:

2.4.11 随机抽样
^^^^^^^^^^^^^^^

.. code:: scala

   // in Scala
   val seed = 5
   val withReplacement = false
   val fraction = 0.5
   df.sample(withReplacement, fraction, seed)
     .count()

.. code:: python

   # in Python
   seed = 5
   withReplacement = false
   fraction = 0.5
   df.sample(withReplacement, fraction, seed) \
     .count()

.. _header-n415:

2.4.12 随机分割
^^^^^^^^^^^^^^^

.. code:: scala

   val seed = 5
   val dataFrames = df.randomSplit(Array(0.25, 0.75), seed)
   dataFrames(0).count() > dataFrames(1).count()

.. code:: python

   # in Python
   seed = 5
   dataFrames = df.randomSplit([0.25, 0.75], seed)
   dataFrames[0].count() > dataFrames[1].count()

.. _header-n418:

2.4.13 拼接(Concatenating)和追加(Appending)行
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   -  由于 DataFrame 是不可变的(immutable)，因此不能将数据 append 到
      DataFrame 的后面，append 会改变 DataFrame；

   -  对于两个需要 Union 的 DataFrame，需要具有相同的 Schema
      和相同数量的 Column，否则就会失败；

.. code:: scala

   // in Scala
   import org.apache.spark.sql.Row

   val schema = df.schema
   val newRows = Seq(
       Row("New Country", "Other Country", 5L),
       Row("New Country 2", "Other Country 3", 1L)
   )
   val parallelizedRows = spark.sparkContext.parallelize(newRows)
   val newDF = spark.createDataFrame(parallelizedRows, schema)

   df.union(newDF)
     .where("count = 1")
     .where($"ORIGIN_COUNTRY_NAME" =!= "United States")
     .show()

.. code:: python

   # in Python
   from pyspark.sql import Row

   schema = df.schema
   newRow = [
       Row("New Country", "Other Country", 5L),
       Row("New Country 2", "Other Country 3", 1L)
   ]
   parallelizedRow = spark.sparkContext.parallelize(newRows)
   newDF = spark.createDataFrame(parallelizedRows, schema)

   df.union(newDF) \
     .where("count = 1") \
     .where(col("ORIGIN_COUNTRY_NAME") != "United States") \
     .show()

.. _header-n428:

2.4.14 行排序
^^^^^^^^^^^^^

.. code:: scala

   // in Scala
   import org.apache.spark.sql.functions.{col, desc, asc}

   df.sort("count").show(5)
   df.orderBy("count", "DEST_COUNTRY_NAME").show(5)
   df.orderBy(col("count"), col("DEST_COUNTRY_NAME")).show(5)
   df.orderBy(expr("count desc"), expr("DESC_COUNTRY_NAME asc")).show(2)
   df.orderBy(desc("count"), asc("DESC_COUNTRY_NAME")).show(2)
   df.orderBy(
       asc_nulls_first("count"), 
       desc_nulls_first("count"),
       asc_nulls_last("count"),
       desc_nulls_last("count")
   )

.. code:: python

   # in Python
   from pyspark.sql.functions import col, desc, asc
   df.sort("count").show(5)
   df.orderBy("count", "DEST_COUNTRY_NAME").show(5)
   df.orderBy(col("count"), col("DEST_COUNTRY_NAME")).show(5)
   df.orderBy(expr("count desc")).show(2)
   df.orderBy(col("count").desc(), col("DESC_COUNTRY_NAME").asc()).show(2)

.. code:: sql

   SELECT *
   FROM dfTable
   ORDER BY count DESC, DEST_COUNTRY_NAME ASC 
   LIMIT 2

为了优化的目的，建议对每个分区的数据在进行 transformations
之前进行排序：

.. code:: scala

   // in Scala
   spark.read.format("json")
       .load("/data/flight-data/json"*-summary.json)
       .sortWithPartitions("count")

.. code:: python

   # in Python
   spark.read.format("json")
       .load("/data/flight-data/json"*-summary.json)
       .sortWithPartitions("count")

.. _header-n436:

2.4.15 Limit
^^^^^^^^^^^^

.. code:: scala

   // in Scala
   df.limit(5).show()

.. code:: python

   # in Python
   df.limit(5).show()

.. code:: sql

   -- in SQL
   SELECT *
   FROM dfTable
   LIMIT 6

.. _header-n440:

2.4.16 Repartiton(重新分区) and Coalesce(分区聚合)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   -  为了优化的目的，可以根据一些常用来进行筛选(filter)操作的 column
      进行进行分区；

   -  Repartition
      会对数据进行全部洗牌，来对数据进行重新分区(未来的分区数 >=
      当前分区数)；

   -  Coalesce 不会对数据进行全部洗牌操作，会对数据分区进行聚合；

.. code:: scala

   // in Scala
   import org.apache.spark.sql.functions.col
   df.rdd.getNumPartitions

   df.repartition(5)
   df.repartition(col("DEST_COUNTRY_NAME"))
   df.repartition(5, col("DEST_COUNTRY_NAME"))

   df.repartition(5, col(DEST_COUNTRY_NAME)).coalesce(2)

.. code:: python

   # in Python
   from pyspark.sql.functions import col
   df.rdd.getNumPartitions()

   df.repartition(5)
   df.repartition(col("DEST_COUNTRY_NAME"))
   df.repartition(5, col("DEST_COUNTRY_NAME"))

   df.repartition(5, col(DEST_COUNTRY_NAME)).coalesce(2)

.. _header-n452:

2.4.17 Collecting Rows to the Driver
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   -  Spark 在驱动程序(driver)上维持集群的状态；

   -  方法：

      -  ``.collect()``

         -  get all data from the entire DataFrame

      -  ``.take(N)``

         -  select the first N rows

      -  ``.show(N, true)``

         -  print out a number of rows nicely

      -  ``.toLocalIterator()``

         -  collect partitions to the dirver as an iterator

.. code:: scala

   // in Scala
   val collectDF = df.limit(10)

   collectDF.take(5) // take works with on Integer count

   collectDF.show()  // print out nicely
   collectDf.show(5, false)

   collectDF.collect()

.. code:: python

   # in Python
   collectDF = df.limit(10)

   collectDF.take(5) // take works with on Integer count

   collectDF.show()  // print out nicely
   collectDf.show(5, false)

   collectDF.collect()

.. _header-n482:

2.4.18 Converting Spark Types
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   -  Convert native types to Spark types；

   -  ``org.apache.spark.sql.functions.lit``

**读取数据，创建 DataFrame:**

.. code:: scala

   // in Scala
   val df = spark.read.format("csv")
       .option("header", "true")
       .option("inferSchema", "ture")
       .load("/data/retail-data/by-day/2010-12-01.csv")
   df.printSchema()
   df.createOrReplaceTempView("dfTable")

.. code:: python

   # in Python
   df = spark.read.format("csv") \
       .option("header", "true") \
       .option("inferSchema", "ture") \
       .load("/data/retail-data/by-day/2010-12-01.csv")
   df.printSchema()
   df.createOrReplaceTempView("dfTable")

**转换为 Spark 类型数据：**

.. code:: scala

   // in Scala
   import org.apache.spark.sql.functions.lit
   df.select(lit(5), lit("five"), lit(5.0))

.. code:: python

   # in Python
   from pyspark.sql.functions import lit
   df.select(lit(5), lit("five"), lit(5.0))

.. code:: sql

   SELECT 5, "five", 5.0

.. _header-n497:

2.4.19 Boolean 
^^^^^^^^^^^^^^^

   -  Spark Boolean 语句:

      -  ``and``

      -  ``or``

      -  ``true``

      -  ``false``

   -  Spark 中的相等与不等:

      -  ``===``

         -  ``.equalTo()``

      -  ``=!=``

         -  ``not()``

**等于，不等于：**

.. code:: scala

   // in Scala
   import org.apache.spark.sql.functions.col

   // equalTo(), ===
   df.where(col("InvoiceNo").equalTo(536365))
     .select("InvoiceNo", "Description")
     .show(5, false)

   df.where(col("InvoiceNo") === 536365)
     .select("InvoiceNo", "Description")
     .show(5, false)


   // not(), =!=
   df.where(col("InvoiceNo").not(536365))
     .select("InvoiceNo", "Description")
     .show(5, false)

   df.where(col("InvoiceNo") =!= 536365)
     .select("InvoiceNo", "Description")
     .show(5, false)


   // specify the predicate as an expression in a string, =/<>
   df.where("InvoiceNo = 536365")
     .show(5, false)

   df.where("InvoiceNo <> 536365")
     .show(5, false)

.. code:: python

   # in Python
   from pyspark.sql.functions import col

   # Spark: equalTo(), ===
   df.where(col("InvoiceNo").equalTo(536365)) \
     .select("InvoiceNo", "Description") \
     .show(5, false)

   df.where(col("InvoiceNo") === 536365) \
     .select("InvoiceNo", "Description") \
     .show(5, false)


   # Spark: not(), =!=
   df.where(col("InvoiceNo").not(536365)) \
     .select("InvoiceNo", "Description") \
     .show(5, false)

   df.where(col("InvoiceNo") =!= 536365) \
     .select("InvoiceNo", "Description") \
     .show(5, false)

   # python
   df.where(col("InvoiceNo") != 536365) \
     .select("InvoiceNo", "Description") \
     .show(5, false)

   # specify the predicate as an expression in a string, =/<>
   df.where("InvoiceNo = 536365")
     .show(5, false)

   df.where("InvoiceNo <> 536365")
     .show(5, false)

**and, or:**

.. code:: scala

   // in Scala
   val priceFilter = col("UnitPrice") > 600
   val descripFilter = col("Description").contains("POSTAGE")
   df.where(col("StockCode").isin("DOT"))
     .where(priceFilter.or(descripFilter))
     .show()

.. code:: python

   # in Python

   from pyspark.sql.functions import inst

   priceFilter = col("UnitPrice") > 600
   descripFilter = instr(df.Description, "POSTAGE") >= 1
   df.where(df.StockCode.isin("DOT")) \
     .where(priceFilter | descripFilter) \
     .show()

.. code:: sql

   -- in SQL
   SELECT *
   FROM 
       dfTable
   WHERE 
       StockCode IN ("DOT") AND
       (UnitPrice > 600 OR instr(Description, "POSTAGE") >= 1)

**使用 Boolean column 筛选 DataFrame：**

.. code:: scala

   // in Scala
   val DOTCodeFilter = col("StockCode") == "DOT"
   val priceFilter = col("UnitPrice") > 600
   val descripFilter = col("Description").contains("POSTAGE")
   df.withColumn("isExpensive", DOTCodeFilter.and(priceFilter.or(descripFilter)))
     .where("isExpensive")
     .select("unitPrice", "isExpensive")
     .show(5)

.. code:: python

   # in Python
   from pyspark.sql.functions import instr

   DOTCodeFilter = col("StockCode") == "DOT"
   priceFilter = col("UnitPrice") > 600
   descripFilter = instr(col("Description"), "POSTAGE") >= 1
   df.withColumn("isExpensive", DOTCodeFilter & (priceFilter | descripFilter)) \
     .where("isExpensive") \
     .select("unitPrice", "isExpensive") \
     .show(5)

.. code:: sql

   -- in SQL
   SELECT 
       UnitPrice
       ,(
           StockCode = "DOT" AND 
           (UnitPrice > 600 OR instr(Description, "POSTAGE") >= 1)
       ) AS isExpensive
   FROM 
       dfTable
   WHERE 
       (
           StockCode = "DOT" AND 
           (UnitPrice > 600 OR instr(Description, "POSTAGE") >= 1)
       )

**其他：**

.. code:: scala

   // in Scala
   import org.apache.spark.sql.functions.{expr, not, col}

   df.withColumn("isExpensive", not(col("UnitPrice").len(250)))
     .filter("isExpensive")
     .select("Description", "UnitPrice")
     .show()

   df.withColumn("isExpensive", expr("NOT UnitPrice <= 250"))
     .filter("isExpensive")
     .select("Description", "UnitPrice")
     .show()

.. code:: python

   # in Python
   from pyspark.sql.functions import expr

   df.withColumn("isExpensive", expr("NOT UnitPrice <= 250")) \
     .where("isExpensive") \
     .select("Description", "UnitPrice") \
     .show()

.. _header-n539:

2.4.20 Number
^^^^^^^^^^^^^

   -  functions:

      -  select(pow())

      -  selectExpr("POWER()")

**Function: ``pow``:**

.. code:: scala

   // in Scala
   import org.apache.spark.sql.functions.{expr, pow}

   val fabricateQuantity = pow(col("Quantity") * col("UnitPrice"), 2) + 5
   df.select(expr("CustomerId"), fabricateQuantity.alias("realQuantity"))
     .show(2)

   df.selectExpr(
       "CustomerId",
       "(POWER((Quantity * UnitPrice), 2.0) + 5) as realQuantity"
   )
     .show()

.. code:: python

   # in Python
   from pyspark.sql.functions import expr, pow

   fabricateQuantity = pow(col("Quantity") * col("UnitPrice"), 2) + 5
   df.select(expr("CustomerId"), fabricateQuantity.alias("realQuantity")) \
     .show()

   df.selectExpr(
       "CustomerId",
       "POWER((Quantity * UnitPrice), 2.0) + 5 as realQuantity") \
     .show()

.. code:: sql

   -- SQL
   SELECT 
       customerId,
       (POWER((Quantity * UnitPrice), 2.0) + 5) as realQuantity
   FROM 
       dfTable

.. _header-n555:

2.4.21 String
^^^^^^^^^^^^^

   -  字符串函数

      -  ``initcap``

      -  ``lower``

      -  ``upper``

      -  ``ltrim``

      -  ``rtrim``

      -  ``trim``

      -  ``lpad``

      -  ``rpad``

   -  正则表达式(Regular Expressions)

.. code:: scala

   // in Scala
   import org.apache.spark.sql.functions.{initcap, lower, upper, lit, col, ltrim, rtrim, trim, lpad, rpad}
   df.select(initcap(col("Description")))
     .show(2, false)

   df.select(col("Description"), lower(col("Description")), upper(lower(col("Description"))))
     .show(2)

   df.select(
       ltrim(lit("    HELLO    ")).as("ltrim"),
       rtrim(lit("    HELLO    ")).as("rtrim"),
       trim(lit("    HELLO    ")).as("trim"),
       lpad(lit("HELLO"), 3, " ").as("lp"),
       rpad(lit("HELLO"), 3, " ").as("rp"))
     .show(2)

.. code:: python

   # in Python
   from pyspark.sql.functions import initcap, lower, upper, lit, col, ltrim, rtrim, trim, lpad, rpad
   df.select(initcap(col("Description"))) \
     .show(2, False)

   df.select(col("Description"), lower(col("Description")), upper(lower(col("Description")))) \
     .show(2)

   df.select(
       ltrim(lit("    HELLO    ")).alias("ltrim"),
       rtrim(lit("    HELLO    ")).alias("rtrim"),
       trim(lit("    HELLO    ")).alias("trim"),
       lpad(lit("HELLO"), 3, " ").alias("lp"),
       rpad(lit("HELLO"), 3, " ").alias("rp")) \
     .show(2)

.. code:: sql

   -- in SQL
   SELECT initcap(Description)
   FROM dfTable
   LIMIT 2


   SELECT 
       Description,
       lower(Description),
       upper(lower(Description))
   FROM dfTable
   LIMIT 2



   SELECT 
       ltrim('    HELLLOOO  '),
       rtrim('    HELLLOOO  '),
       trim('    HELLLOOO  '),
       lpad('HELLLOOO  ', 3, ' '),
       rpad('HELLLOOO  ', 10, ' ')
   FROM dfTable

.. _header-n585:

2.4.22 Date and Timestamp
^^^^^^^^^^^^^^^^^^^^^^^^^

.. _header-n587:

2.4.23 Null Data
^^^^^^^^^^^^^^^^

.. _header-n588:

2.4.23.1 Coalesce
'''''''''''''''''

.. code:: scala

   df.na.replace("Description", Map("" -> "UNKNOWN"))

.. code:: python

   df.na.replace([""], ["UNKNOWN"], "Description")

.. _header-n591:

2.4.23.2 ifnull, nullif, nvl, nvl2
''''''''''''''''''''''''''''''''''

.. code:: scala

.. _header-n594:

2.4.23.3 drop
'''''''''''''

.. _header-n597:

2.4.23.4 fill
'''''''''''''

.. _header-n599:

2.4.23.5 replace
''''''''''''''''

.. _header-n603:

2.4.24 Ordering
^^^^^^^^^^^^^^^

-  asc\ *null*\ first()

-  asc\ *null*\ last()

-  desc\ *null*\ first()

-  desc\ *null*\ last()

.. _header-n613:

2.4.25 复杂类型 Complex Types
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   -  Struct(s)

   -  Array(s)

   -  split

   -  Array Length

   -  array_contains

   -  explode

   -  Maps

.. _header-n630:

2.4.25.1 Struct
'''''''''''''''

   -  Struct: DataFrames in DataFrame

.. code:: scala

   // in Scala

   df.selectExpr("(Description, InvoiceNo) as complex", "*")
   df.selectExpr("struct(Description, InvoiceNo) as complex", "*")

   val complexDF = df.select(struct("Description", "InvoiceNo").alias("complex"))
   complexDF.createOrReplaceTempView("complexDF")

   complexDF.select("complex.Description")
   complexDF.select(col("complex").getField("Description"))
   complexDF.select("complex.*")

.. code:: python

   # in Python

   df.selectExpr("(Description, InvoiceNo) as complex", "*")
   df.selectExpr("struct(Description, InvoiceNo) as complex", "*")

   from pyspark.sql.functions import struct
   complexDF = df.select(struct("Description", "InvoiceNo").alias("complex"))
   complexDF.createOrReplaceTempView("complexDF")

   complexDF.select("complex.Description")
   complexDF.select(col("complex").getField("Description"))
   complexDF.select("complex.*")

.. code:: sql

   SELECT 
       complex.*
   FROM
       complexDF

.. _header-n638:

2.4.25.2 Array
''''''''''''''

-  split

-  Array Length

-  array_contains

-  explode

**split():**

.. code:: scala

   // in Scala
   import org.apache.spark.sql.functions.split
   df.select(split(col("Description"), " "))
     .alias("array_col")
     .selectExpr("array_col[0]")
     .show()

.. code:: python

   # in Python
   from pyspark.sql.functions import split
   df.select(split(col("Description"), " ")) \
     .alias("array_col") \
     .selectExpr("array_col[0]") \
     .show()

.. code:: sql

   -- sql
   SELECT 
       split(Description, ' ')[0]
   FROM 
       dfTable

**Array Length: size()**

.. code:: scala

   import org.apache.spark.sql.functions.size
   df.select(size(split(col("Description"), " ")))
     .show()

.. code:: python

   from pyspark.sql.functions import size
   df.select(size(split(col("Description"), " "))) \
     .show()

**array_contains():**

.. code:: scala

   // in Scala
   import org.apache.spark.sql.functions.array_contains
   df.select(array_contains(split(col("Description"), " "), "WHITE"))
     .show()

.. code:: python

   # in Python
   from pyspark.sql.functions import array_contains
   df.select(array_contains(split(col("Description"), " "), "WHITE"))

.. code:: sql

   -- in SQL
   SELECT 
       array_contains(split(Description, ' '), "WHITE")
   FROM 
       dfTable

**explode():**

.. code:: scala

   // in Scala
   import org.apache.spark.sql.functions.{split, explode}

   df.withColumn("splitted", split(col("Description"), " ")) 
     .withColumn("exploded", explode(col(splitted)))
     .select("Description", "InvoiceNo", "exploded")
     .show()

.. code:: python

   # in Python
   from pyspark.sql.functions import split, explode

   df.withColumn("splitted", split(col("Description"), " ")) \
     .withColumn("exploded", explode(col(splitted))) \
     .select("Description", "InvoiceNo", "exploded") \
     .show()

.. code:: sql

   -- in SQL
   SELECT 
       Description,
       InvoiceNO,
       exploded
   FROM 
       (
           SELECT 
               *,
               split(Description, " ") AS splitted 
           FROM dfTable
       )
   LATERAL VIEW explode(splitted) AS exploded

.. _header-n666:

2.4.25.3 Maps
'''''''''''''

.. code:: scala

   // in Scala
   import org.apache.spark.sql.functions.map

   df.select(map(col("Description"), col("InvoiceNo")).alias("complex_map"))
     .selectExpr("complex_map['WHITE_METAL_LANTERN']")
     .show()

   df.select(map(col("Description"), col("InvoiceNo")).alias("complex_map"))
     .selectExpr("explode(complex_map)")
     .show()

.. code:: python

   # in Python
   from pyspark.sql.functions import map
   df.select(map(col("Description"), col("InvoiceNo")).alias("complex_map")) \
     .selectExpr("complex_map['WHITE_METAL_LANTERN']") \
     .show()

   df.select(map(col("Description"), col("InvoiceNo")).alias("complex_map")) \
     .selectExpr("explode(complex_map)") \
     .show()

.. code:: sql

   SELECT 
       map(Description, InvoiceNo) AS complex_map
   FROM dfTable
   WHERE Description IS NOT NULL

.. _header-n671:

2.4.26 Json
^^^^^^^^^^^

-  Spark 有一些对 JSON 数据的独特的操作

   -  可以直接操作 JSON 字符串

   -  可以从 JSON 对象中解析或提取数据为 DataFrame

   -  把 StructType 转换为 JSON 字符串

**可以直接操作 JSON 字符串：**

.. code:: scala

   // in Scala

   val jsonDF = spark.range(1).selectExpr("""
       '{
           "myJSONKey": {
               "myJSONValue": {
                   1, 2, 3
               }
           }
       }' as jsonString
   """)

.. code:: python

   # in Python

   jsonDF = spark.range(1).selectExpr("""
       '{
           "myJSONKey": {
               "myJSONValue": {
                   1, 2, 3
               }
           }
       }' as jsonString
   """)

**可以从 JSON 对象中解析或提取数据为 DataFrame：**

.. code:: scala

   // in Scala

   import org.apache.spark.sql.functions.{get_json_object, json_tuple}

   jsonDF.select(
       get_json_object(col("jsonString"), "$.myJSONKey.myJSONValue[1]") as "column",
       json_tuple(col("jsonString"), "myJSONKey"))
       .show()

.. code:: python

   # in Python

   from pyspark.sql.functions import get_json_object, json_tuple

   jsonDF.select(
       get_json_object(col("jsonString"), "$.myJSONKey.myJSONValue[1]") as "column",
       json_tuple(col("jsonString"), "myJSONKey")) \
       .show()

**把 StructType 转换为 JSON 字符串：**

.. code:: scala

   // in Scala

   import org.apache.spark.sql.functions.to_json
   df.selectExpr("(InvoiceNo, Description) as myStruct")
     .select(to_json(col("myStruct")))

.. code:: python

   # in Python

   from pyspark.sql.functions import to_json
   df.selectExpr("(InvoiceNo, Description) as myStruct") \
     .select(to_json(col("myStruct")))

.. code:: scala

   // in Scala

   import org.apache.spark.sql.functions.from_json
   import org.apache.spark.sql.types._

   val parseSchema = new SturctType(Array(
       new StructField("InvoiceNo", StringType, true),
       new StructField("Description", StringType, true)
       )
   )

   df.selectExpr("(InvoiceNo, Description) as myStruct")
     .select(to_json(col("myStruct")).alias("newJSON"))
     .select(from_json(col("newJSON"), parseShcema), col("newJSON"))
     .show()

.. code:: python

   from pyspark.sql.functions import from_json
   from pyspark.sql.types import *

   parseSchema = SturctType((
       StructField("InvoiceNo", StringType, True),
       StructField("Description", StringType, True)
   )

   df.selectExpr("(InvoiceNo, Description) as myStruct")
     .select(to_json(col("myStruct")).alias("newJSON"))
     .select(from_json(col("newJSON"), parseShcema), col("newJSON"))
     .show()

.. _header-n696:

2.4.27 用户自定义函数 User-Defined Functions(UDF)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

   -  User-Defined Functions (UDF) make it possible for you to write
      your own custom transformations using Python or Scala and even use
      external libraries.

示例：

.. code:: scala

   // in Scala

   // DataFrame
   val udfExampleDF = spark.range(5).toDF("num")

   // UDF power of 3
   def power3(number: Double): Double = {
       number * number * number
   }

   power3(2.0)

   // Register the UDF to make it available as a DataFrame Function
   import org.apache.spark.sql.functions.udf
   val power3udf = udf(power3(_:Double):Double)

   // Use UDF as DataFrame function
   udfExampleDF.select(power3udf(col("num"))).show()

   // Register UDF as a Spark SQL function
   spark.udf.register("power3", power3(_:Double):Double)
   udfExampleDF.selectExpr("power3(num)").show()

.. code:: python

   # in Python

   # DataFrame
   udfExampleDF = spark.range(5).toDF("num")

   # UDF power of 3
   def power3(double_value):
       return double_value ** 3

   power3(2.0)

   # Register the UDF to make it available as a DataFrame Function
   from pyspark.sql.functions import udf
   power3udf = udf(power3)

   # Use UDF as DataFrame function
   udfExampleDf.select(power3udf(col("num"))).show()

   # Regiser UDf as a Spark SQL function
   spark.udf.regiser("power3", power3)
   udfExampleDF.selectExpr("power3(num)").show()

Hive UDFs：

   -  You can user UDF/UDAF creation via a Hive syntax.

      -  First, must enable Hive support when they create their
         SparkSession.

      -  Then you register UDFs in SQL.

   -  ``SparkSession.builder().enableHiveSupport()``

.. code:: sql

   -- in SQL
   CREATE TEMPORARY FUNCTION myFunc AS 'com.organization.hive.udf.FunctionName'

   CREATE FUNCTION myFunc AS 'com.organization.hive.udf.FunctionName'

.. _header-n719:

2.4.28 Aggregations
^^^^^^^^^^^^^^^^^^^

   -  In an aggregation, you will specify a ``key`` or ``grouping`` and
      an ``aggregation function`` that specifies how you should
      transform one or more columns.

      -  ``aggregation function`` must produce one result for each
         group, given multiple input values；

   -  Spark can aggregate any kind of value into an ``array``, ``list``,
      ``map``\ ；

   -  Spark 聚合方式：

      -  select 语句

      -  group by

         -  one or more keys

         -  one or more aggregation function

      -  window

         -  one or more keys

         -  one or more aggregation function

      -  group set

         -  aggregate at multiple different levels;

      -  rollup

         -  one or more keys

         -  one or more aggregation function

         -  summarized hierarchically

      -  cube

         -  one or more keys

         -  one or more aggregation function

         -  summarized across all combinations of columns

   -  每个 grouping 返回一个 ``RelationalGroupedDataset``
      用来表示聚合(aggregation)；

**读入数据：**

.. code:: scala

   // in Scala
   val df = spark.read.format("csv")
       .option("header", "true")
       .option("inferSchema", "true")
       .load("/data/retail-data/all/*.csv")
       .coalesce(5)
   df.cache()
   df.createOrReplaceTempView("dfTable")


   // 最简单的聚合
   df.count()

.. code:: python

   # in Python
   df = spark.read.format("csv") \
       .option("header", "true") \
       .option("inferSchema", "true") \
       .load("/data/retail-data/all/*.csv") \
       .coalesce(5)
   df.cache()
   df.createOrReplaceTempView("dfTable")

   # 最简单的聚合
   df.count()

.. _header-n776:

2.4.28.1 Aggregation Functions
''''''''''''''''''''''''''''''

-  ``org.apache.spark.sql.functions`` or ``pyspark.sql.functions``

   -  count / countDistinct / approx\ *count*\ distinct

   -  first / last

   -  min / max

   -  sum/sumDistinct

   -  avg

   -  Variance / Standard Deviation

   -  skewness / kurtosis

   -  Covariance/Correlation

   -  Complex Types

**Transformation: count:**

   -  ``count(*)`` 会对 null 计数；

   -  ``count("OneColumn")`` 不会对 null 计数；

.. code:: scala

   // in Scala
   import org.apache.spark.sql.functions.count
   df.select(count("StockCode")).show()
   df.select(count(*)).show()
   df.select(count(1)).show()

.. code:: python

   # in Python
   from pyspark.sql.functions import count
   df.select(count("StockCode")).show()
   df.select(count(*)).show()
   df.select(count(1)).show()

.. code:: sql

   -- in SQL
   SELECT COUNT(StockCode)
   FROM dfTable

   SELECT COUTN(*)
   FROM dfTable

   SELECT COUNT(1)
   FROM dfTable

**countDistinct:**

.. code:: scala

   // in Scala
   import org.apache.spark.sql.functions.countDistinct
   df.select(countDistinct("StockCode"))
     .show()

.. code:: python

   # in Python
   from pyspark.sql.functions import first, last
   df.select(countDistinct("StockCode")) \
     .show()

.. code:: sql

   -- in SQL
   SELECT 
       COUNT(DISTINCT *)
   FROM dfTable

**approx\ count\ distinct:**

.. code:: scala

   // in Scala
   import org.apache.spark.sql.functions.approx_count_distinct
   df.select(approx_count_distinct("StockCode", 0.1))
     .show()

.. code:: python

   # in Python
   from pyspark.sql.functions import approx_count_distinct
   df.select(approx_count_distinct("StockCode", 0.1)) \
     .show()

.. code:: sql

   -- in SQL
   SELECT 
       approx_count_distinct(StockCode, 0.1)
   FROM dfTable

**first and last:**

.. code:: scala

   // in Scala
   import org.apache.spark.sql.functions.{first, last}
   df.select(first("StockCode"), last("StockCode"))
     .show()

.. code:: python

   # in Python
   from pyspark.sql.functions import first, last
   df.select(first("StockCode"), last("StockCode")) \
     .show()

.. code:: sql

   -- in SQL
   SELECT 
       first(StockCode),
       last(StockCode)
   FROM dfTable

**min and max:**

.. code:: scala

   // in Scala
   import org.apache.spark.sql.functions.{min, max}
   df.select(min("StockCode"), max("StockCode"))
     .show()

.. code:: python

   # in Python
   from pyspark.sql.functions import min, max
   df.select(min("StockCode"), max("StockCode")) \
     .show()

.. code:: sql

   -- in SQL
   SELECT 
       min(StockCode),
       max(StockCode)
   FROM dfTable

**sum:**

.. code:: scala

   // in Scala
   import org.apache.spark.sql.functions.sum
   df.select(sum("Quantity"))
     .show()

.. code:: python

   # in Python
   from pyspark.sql.functions import sum
   df.select(sum("Quantity")) \
     .show()

.. code:: sql

   -- in SQL
   SELECT 
       sum(Quantity)
   FROM dfTable

**sumDistinct**

.. code:: scala

   // in Scala
   import org.apache.spark.sql.functions.sumDistinct
   df.select(sumDistinct("Quantity"))
     .show()

.. code:: python

   # in Python
   from pyspark.sql.functions import sumDistinct
   df.select(sumDistinct("Quantity")) \
     .show()

.. code:: sql

   -- in SQL
   SELECT 
       sumDistinct(Quantity)
   FROM dfTable

**avg:**

.. code:: scala

   // in Scala
   import org.apache.spark.sql.functions.{sum, count, avg, mean}
   df.select(
       count("Quantity").alias("total_transactions"),
       sum("Quantity").alias("total_pruchases"),
       avg("Quantity").alias("avg_purchases"),
       expr("mean(Quantity)").alias("mean_purchases")
       )
     .selectExpr(
       "total_pruchases" / "total_transactions", 
       "avg_purchases",
       "mean_purchases")
     .show()

   // Distinct version
   df.select(
       countDistinct("Quantity").alias("total_transactions"),
       sumDistinct("Quantity").alias("total_pruchases"),
       avg("Quantity").alias("avg_purchases"),
       expr("mean(Quantity)").alias("mean_purchases")
       )
     .selectExpr(
       "total_pruchases" / "total_transactions", 
       "avg_purchases",
       "mean_purchases")
     .show()

.. code:: python

   # in Python
   from pyspark.sql.functions import sum, count, avg, mean
   df.select(
       count("Quantity").alias("total_transactions"),
       sum("Quantity").alias("total_pruchases"),
       avg("Quantity").alias("avg_purchases"),
       expr("mean(Quantity)").alias("mean_purchases")
       ) \
     .selectExpr(
       "total_pruchases" / "total_transactions", 
       "avg_purchases",
       "mean_purchases") \
     .show()

**Variance and Standard Deviation:**

-  样本方差，样本标准差

   -  ``var_samp``

   -  ``stddev_samp``

-  总体方差，总体标准差

   -  ``var_pop``

   -  ``stddev_pop``

.. code:: scala

   // in Scala
   import org.apache.spark.sql.functions.{var_pop, stddev_pop, var_samp, stddev_samp}
   df.select(
       var_pop("Quantity"), 
       var_samp("Quantity"),
       stddev_pop("Quantity"),
       stddev_samp("Quantity"))
     .show()

.. code:: python

   # in Python
   from pyspark.sql.functions import var_pop, stddev_pop, var_samp, stddev_samp
   df.select(
       var_pop("Quantity"), 
       var_samp("Quantity"),
       stddev_pop("Quantity"),
       stddev_samp("Quantity")
       ) \
     .show()

.. code:: sql

   -- in SQL
   SELECT 
       var_pop("Quantity"),
       var_samp("Quantity"),
       stddev_pop("Quantity"),
       stddev_samp("Quantity")
   FROM dfTable

**skewness and kurtosis:**

.. code:: scala

   // in Scala
   import org.apache.spark.sql.functions.{skewness, kurtosis}
   df.select(
       skewness("Quantity"),
       kurtosis("Quantity"))
     .show()

.. code:: python

   # in Python
   from pyspark.sql.functions import skewness, kurtosis
   df.select(
       skewness("Quantity"),
       kurtosis("Quantity")) \
     .show()

.. code:: sql

   -- in SQL
   SELECT 
       skewness(Quantity),
       kurtosis(Quantity)
   FROM dfTable

**Covariance and Correlation:**

.. code:: scala

   // in Scala
   import org.apache.spark.sql.functions.{corr, covar_pop, covar_samp}
   df.select(
       corr("InvoiceNo", "Quantity"),
       covar_samp("InvoiceNo", "Quantity"),
       covar_pop("InvoiceNo", "Quantity")) 
     .show()

.. code:: python

   # in Python
   from pyspark.sql.functions import corr, covar_pop, covar_samp
   df.select(
       corr("InvoiceNo", "Quantity"),
       covar_samp("InvoiceNo", "Quantity"),
       covar_pop("InvoiceNo", "Quantity")) \
     .show()

.. code:: sql

   -- in SQL
   SELECT 
       corr("InvoiceNo", "Quantity"),
       covar_samp("InvoiceNo", "Quantity"),
       covar_pop("InvoiceNo", "Quantity")
   FROM dfTable

**Aggregation to Complex Types:**

.. code:: scala

   // in Scala
   import org.apache.spark.sql.functions.{collect_set, collect_list}
   df.agg(
       collect_set("Country"), 
       collect_list("Country")
       )
     .show()

.. code:: python

   # in Python
   from pyspark.sql.functions import collect_set, collect_list
   df.agg(
       collect_set("Country"), 
       collect_list("Country")
       ) \
     .show()

.. code:: sql

   -- in SQL
   SELECT 
       collect_set(Country),
       collect_list(Country)
   FROM dfTable

.. _header-n873:

2.4.28.2 Grouping
'''''''''''''''''

-  First, specify the columns on which would like to group;

   -  return ``RelationalGroupedDataset``

-  Then, specify the aggregation functions;

   -  return ``DataFrame``

.. code:: scala

   // in Scala
   df.groupBy("Invoice", "CustomerId")
     .count()
     .show()

.. code:: python

   # in Python
   df.groupBy("Invoice", "CustomerId") \
     .count()
     .show()

.. code:: sql

   -- in SQL
   SELECT 
       COUNT(*)
   FROM dfTable
   GROUP BY 
       Invoice,
       CustomId

.. _header-n889:

Grouping with Expression
                        

.. code:: scala

   import org.apache.spark.sql.functions.count

   df.groupBy("InvoiceNo")
     .agg(
       count("Quantity").alias("quan"),
       expr("count(Quantity)"))
     .show()

.. code:: python

   # in Python
   from pyspark.sql.functions import count
   df.groupBy("InvoiceNo") \
     .agg(
       count("Quantity").alias("quan"),
       expr("count(Quantity)")) \
     show()

.. _header-n892:

Group with Maps
               

.. code:: scala

   // in Scala
   df.groupBy("InvoiceNo")
     .agg(
       "Quantity" -> "avg", 
       "Quantity" -> "stddev_pop")
     .show()

.. code:: python

   df.groupBy("InvoiceNo") \
     .agg(
       expr("avg(Quantity)"), 
       expr("stddev_pop(Quantity)")) \
     .show()

.. code:: sql

   -- in SQL
   SELECT 
       avg(Quantity),
       stddev_pop(Quantity)
   FROM dfTable
   GROUP BY 
       InvoiceNo

.. _header-n896:

2.4.28.3 Window Functions
'''''''''''''''''''''''''

-  ranking functions

-  analytic functions

-  aggregate functions

.. code:: scala

   // in Scala
   import org.apache.spark.sql.functions.{col, to_date}

   val dfWithDate = df.withColumn("date", to_date(col("InvoiceDate"), "MM/d/yyyy H:mm"))
   dfWithDate.createOrReplaceTempView("dfWithDate")

.. code:: python

   # in Python
   from pyspark.sql.functions import col, to_date
   dfWithDate = df.withColumn("date", to_date(col("InvoiceDate"), "MM/d/yyyy H:mm"))
   dfWithDate.createOrReplaceTempView("dfWithDate")

示例：

.. code:: scala

   // in Scala
   import org.apache.spark.sql.expressions.Window
   import org.apache.spark.sql.functions.{col, max, dense_rank, rank}

   val windowSpec = Window
       .partitionBy("CustomerId", "date")
       .orderBy(col("Quantity").desc)
       .rowsBetween(Window.unboundedPreceding, Window.currentRow))

   val maxPurchaseQuantity = max(col("Quantity")).over(windowSpec)
   val purchaseDenseRank = dense_rank().over(windowSpec)
   val purchaseRank = rank().over(windowSpec)

   dfWithDate
       .where("CustomerId IS NOT NULL")
       .orderBy("CustomerId")
       .select(
           col("CustomerId"),
           col("date"),
           col("Quantity"),
           purchaseRank.alias("quantityRank"),
           purchaseDenseRank.alias("quantityDenseRank"),
           maxPurchaseQuantity.alias("maxPurchaseQuantity"))
       .show()

.. code:: python

   # in Python
   from pyspark.sql.expressions import Window
   from pyspark.sql.functions import col, max, dense_rank, rank

   windowSpec = Window \
       .partitionBy("CustomerId", "date") \
       .orderBy(desc("Quantity")) \
       .rowsBetween(Window.unboundedPreceding, Window.currentRow)

   maxPurchaseQuantity = max(col("Quantity")) \
       .over(windowSpec)

   purchaseDenseRank = dense_rank().over(windowSpec)
   purchaseRank rank().over(windowSpec)

   dfWithDate
       .where("CustomerId IS NOT NULL") \
       .orderBy("CustomerId") \
       .select(
           col("CustomerId"),
           col("date"),
           col("Quantity"),
           purchaseRank.alias("quantityRank"),
           purchaseDenseRank.alias("quantityDenseRank"),
           maxPurchaseQuantity.alias("maxPurchaseQuantity")) \
       .show()

.. code:: sql

   -- in SQL
   SELECT 
       CustomerId,
       date,
       Quantity,
       rank(Quantity) over(partition by CustomerId, date
                           order by Quantity desc null last
                           rows between 
                               unbounded preceding and 
                               current row) as rank,
       dense_rank(Quantity) over(partition by CustomerId, date
                                 order by Quantity desc null last
                                 rows between 
                                     unbounded preceding and 
                                     urrent row) as drank,
       max(Quantity) over(partition by CustomerId, date
                          order by Quantity desc null last
                          rows between 
                              unbounded preceding and
                              current row) as maxPurchase
   FROM dfTable 
   WHERE CustomerId IS NOT NULL 
   ORDER BY CustomerId

.. _header-n911:

2.4.28.4 Grouping Set
'''''''''''''''''''''

-  rollups

-  cube

-  Grouping Medadata

-  Pivot

.. code:: scala

   // in Scala
   val dfNotNull = dfWithDate.drop()
   dfNotNull.createOrReplaceTempView("dfNotNull")

.. code:: python

   # in Python
   dfNotNull = dfWithDate.drop()
   dfNotNull.createOrReplaceTempView("dfNotNull")

示例：

.. code:: sql

   -- in SQL
   SELECT 
       CustomerId,
       stockCode,
       sum(Quantity) 
   FROM dfNotNull
   GROUP BY 
       CustomerId,
       stockCode,
   ORDER BY 
       CustomerId DESC,
       stockCode DESC

.. code:: sql

   -- in SQL
   SELECT 
       CustomerId,
       stockCode,
       sum(Quantity) 
   FROM dfNotNull
   GROUP BY 
       CustomerId,
       stockCode
   GROUPING SETS((CustomerId, stockCode))
   ORDER BY 
       CustomerId DESC, 
       stockCode DESC

.. code:: sql

   -- in SQL
   SELECT 
       CustomerId,
       stockCode,
       sum(Quantity)
   FROM dfNotNull
   GROUP BY 
       CustomerId, 
       stockCode
   GROUPING SETS((CustomerId, stockCode), ())
   ORDER BY 
       CustomerId DESC, 
       stockCode DESC

.. _header-n928:

2.4.28.4.1 Rollups
                  

.. code:: scala

   // in Scala
   val rolledUpDF = dfNotNull.rollup("Date", "Country")
       .agg(sum("Quantity"))
       .selectExpr("Date", "Country", "`sum(Quantity)` as total_quantity")
       .orderBy("Date")
   rolledUpDF.show()
   rolledUpDF.where("Country IS NULL").show()
   rolledUpDF.where("Date IS NULL").show()

.. code:: python

   # in Python
   rolledUpDF = dfNotNull.rollup("Date", "Country") \
       .agg(sum("Quantity")) \
       .selectExpr("Date", "Country", "`sum(Quantity)` as total_quantity") \
       .orderBy("Date")
   rolledUpDF.show()
   rolledUpDF.where("Country IS NULL").show()
   rolledUpDF.where("Date IS NULL").show()

.. _header-n931:

2.4.28.4.1 Cube
               

.. code:: scala

   // in Scala
   val rolledUpDF = dfNotNull.rollup("Date", "Country")
       .agg(sum("Quantity"))
       .selectExpr("Date", "Country", "`sum(Quantity)` as total_quantity")
       .orderBy("Date")
   rolledUpDF.show()
   rolledUpDF.where("Country IS NULL").show()
   rolledUpDF.where("Date IS NULL").show()

.. code:: python

   # in Python
   rolledUpDF = dfNotNull.rollup("Date", "Country") \
       .agg(sum("Quantity")) \
       .selectExpr("Date", "Country", "`sum(Quantity)` as total_quantity") \
       .orderBy("Date")
   rolledUpDF.show()
   rolledUpDF.where("Country IS NULL").show()
   rolledUpDF.where("Date IS NULL").show()

.. _header-n935:

2.4.28.4.1 Grouping Metadata
                            

.. _header-n936:

2.4.28.4.1 Pivot
                

.. _header-n939:

2.4.28.5 UDF Aggregation Functions
''''''''''''''''''''''''''''''''''

.. _header-n943:

2.4.29 Joins
^^^^^^^^^^^^

-  Join Expression

-  Join Types

   -  Inner Join

   -  Outer Join

   -  Left outer join

   -  Right outer join

   -  Left semi join

   -  Left anti join

   -  Nature join

   -  Cross join(Cartesian)

**数据：**

.. code:: scala

   // in Scala
   val person = Set(
       (0, "Bill Chambers", 0, Seq(100)),
       (1, "Matei Zaharia", 1, Seq(500, 250, 100)),
       (2, "Michael Armbrust", 1, Seq(250, 100))
       )
       .toDF("id", "name", "graduate_program", "spark_status")

   val graduateProgram = Seq(
       (0, "Master", "School of Information", "UC Berkeley"),
       (2, "Master", "EECS", "UC Berkeley"),
       (1, "Ph.D", "EECS", "UC Berkeley")
       )
       .toDF("id", "degree", "department", "school")

   val sparkStatus = Seq(
       (500, "Vice President"),
       (250, "PMC Member"),
       (100, "Contributor")
       )
       .toDF("id", "status")

   person.createOrReplaceTempView("person")
   graduateProgram.createOrReplaceTempView("graduateProgram")
   sparkStatus.createOrReplaceTempView("sparkStatus")

.. code:: python

   # in Pyton
   person = spark.createDataFrame([
       (0, "Bill Chambers", 0, [100]),
       (1, "Matei Zaharia", 1, [500, 250, 100]),
       (2, "Michael Armbrust", 1, [250, 100])
       ]) \
       .toDF("id", "name", "graduate_program", "spark_status")

   graduateProgram = spark.createDataFrame([
       (0, "Master", "School of Information", "UC Berkeley"),
       (2, "Master", "EECS", "UC Berkeley"),
       (1, "Ph.D", "EECS", "UC Berkeley")
       ]) \
       .toDF("id", "degree", "department", "school")

   val sparkStatus = spark.createDataFrame([
       (500, "Vice President"),
       (250, "PMC Member"),
       (100, "Contributor")
       ]) \
       .toDF("id", "status")

   person.createOrReplaceTempView("person")
   graduateProgram.createOrReplaceTempView("graduateProgram")
   sparkStatus.createOrReplaceTempView("sparkStatus")

.. _header-n970:

2.4.29.1 Inner join 
''''''''''''''''''''

.. code:: scala

   // in Scala
   val joinExpression = person.col("graduate_program") == graduateProgram.col("id")
   person
       .join(graduateProgram, joinExpression)
       .show()

   val joinType = "inner"
   person
       .join(graduateProgram, joinExpression, joinType)
       .show()

.. code:: python

   # in Python
   joinExpression = person["graduate_program"] == graduateProgram["id"]
   person \
       .join(graduateProgram, joinExpression) \
       .show()

   joinType = "inner"
   person \
       .join(graduateProgram, joinExpression, joinType) \
       .show()

.. code:: sql

   -- in SQL
   SELECT
       *
   FROM person
   JOIN graduateProgram
       ON person.graduate_program = graduateProgram.id

   SELECT
       *
   FROM person
   INNER JOIN graduateProgram
       ON person.graduate_program = graduateProgram.id

.. _header-n974:

2.4.29.2 Outer join
'''''''''''''''''''

.. code:: scala

   // in Scala
   val joinExpression = person.col("graduate_program") == graduateProgram.col("id")
   val joinType = "outer"
   person
       .join(graduateProgram, joinExpression, joinType)
       .show()

.. code:: python

   # in Python
   joinExpression = person["graduate_program"] == graduateProgram["id"]
   joinType = "outer"
   person \
       .join(graduateProgram, joinExpression, joinType) \
       .show()

.. code:: sql

   -- in SQL
   SELECT
       *
   FROM person
   OUTER JOIN graduateProgram
       ON person.graduate_program = graduateProgram.id

.. _header-n980:

2.4.29.3 Left Outer join
''''''''''''''''''''''''

.. code:: scala

   // in Scala
   val joinExpression = person.col("graduate_program") == graduateProgram.col("id")
   val joinType = "left_outer"
   person
       .join(graduateProgram, joinExpression, joinType)
       .show()

.. code:: python

   # in Python
   joinExpression = person["graduate_program"] == graduateProgram["id"]
   joinType = "left_outer"
   person \
       .join(graduateProgram, joinExpression, joinType) \
       .show()

.. code:: sql

   -- in SQL
   SELECT
       *
   FROM person
   LEFT JOIN graduateProgram
       ON person.graduate_program = graduateProgram.id

.. _header-n984:

2.4.29.4 Right Outer join
'''''''''''''''''''''''''

.. code:: scala

   // in Scala
   val joinExpression = person.col("graduate_program") == graduateProgram.col("id")
   val joinType = "right_outer"
   person
       .join(graduateProgram, joinExpression, joinType)
       .show()

.. code:: python

   # in Python
   joinExpression = person["graduate_program"] == graduateProgram["id"]
   joinType = "right_outer"
   person \
       .join(graduateProgram, joinExpression, joinType) \
       .show()

.. code:: sql

   -- in SQL
   SELECT
       *
   FROM person
   RIGHT JOIN graduateProgram
       ON person.graduate_program = graduateProgram.id

.. _header-n988:

2.4.29.5 Left Semi join
'''''''''''''''''''''''

.. code:: scala

   // in Scala
   val joinExpression = person.col("graduate_program") == graduateProgram.col("id")
   val joinType = "left_semi"
   person
       .join(graduateProgram, joinExpression, joinType)
       .show()

   val gradProgram2 = graduateProgram
       .union(
           Seq((0, "Master", "Duplicated Row", "Duplicated School"))
       )
       .toDF()
   val gradProgram2.createOrReplaceTempView("gradProgram2")
   gradProgram2
       .join(person, joinExpression, joinType)
       .show()

.. code:: python

   # in Python
   joinExpression = person["graduate_program"] == graduateProgram["id"]
   joinType = "left_semi"
   person \
       .join(graduateProgram, joinExpression, joinType) \
       .show()

   gradProgram2 = graduateProgram
       .union(
           spark.crateDataFrame([(0, "Master", "Duplicated Row", "Duplicated School")])
       )
       .toDF()
   val gradProgram2.createOrReplaceTempView("gradProgram2")
   gradProgram2
       .join(person, joinExpression, joinType)
       .show()

.. code:: sql

   -- in SQL
   SELECT
       *
   FROM person
   JOIN graduateProgram
       ON person.graduate_program = graduateProgram.id


   SELECT *
   FROM gradProgram2
   LEFT SEMI JOIN person
       ON gradProgram2.id = person.graduate_program

.. _header-n992:

2.4.29.6 Left Anti join
'''''''''''''''''''''''

.. code:: scala

   // in Scala
   val joinExpression = person.col("graduate_program") == graduateProgram.col("id")
   val joinType = "left_anti"
   person
       .join(graduateProgram, joinExpression, joinType)
       .show()

.. code:: python

   # in Python
   joinExpression = person["graduate_program"] == graduateProgram["id"]
   joinType = "left_anti"
   person \
       .join(graduateProgram, joinExpression, joinType) \
       .show()

.. code:: sql

   -- in SQL
   SELECT
       *
   FROM person
   LEFT ANTI JOIN graduateProgram
       ON person.graduate_program = graduateProgram.id

.. _header-n996:

2.4.29.7 Natural join
'''''''''''''''''''''

.. code:: sql

   -- in SQL
   SELECT
       *
   FROM person
   NATURAL JOIN graduateProgram

.. _header-n999:

2.4.29.8 Cross join
'''''''''''''''''''

.. code:: scala

   // in Scala
   val joinExpression = person.col("graduate_program") == graduateProgram.col("id")
   val joinType = "cross"
   person
       .join(graduateProgram, joinExpression, joinType)
       .show()

   person
       .crossJoin(graduateProgram)
       .show()

.. code:: python

   # in Python
   joinExpression = person["graduate_program"] == graduateProgram["id"]
   joinType = "cross"
   person \
       .join(graduateProgram, joinExpression, joinType) \
       .show()

   person \
       .crossJoin(graduateProgram) \
       .show()

.. code:: sql

   -- in SQL
   SELECT
       *
   FROM person
   CROSS JOIN graduateProgram
       ON person.graduate_program = graduateProgram.id

   SELECT 
       *
   FROM graduateProgram 
   CROSS JOIN person

.. _header-n1008:

3.SQL
-----

.. _header-n1009:

3.1 表 (tables)
~~~~~~~~~~~~~~~

.. _header-n1010:

3.1.1 Spark SQL 创建表
^^^^^^^^^^^^^^^^^^^^^^

读取 flight data 并创建为一张表：

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

3.2 视图 (views)
~~~~~~~~~~~~~~~~

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

3.3 数据库 (databases)
~~~~~~~~~~~~~~~~~~~~~~

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

3.4 数据查询语句
~~~~~~~~~~~~~~~~

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

其中：

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

3.5 其他
~~~~~~~~

.. _header-n1106:

4.DataSet
---------

DataSet 的使用场景:

.. _header-n1108:

4.1 创建 DataSet
~~~~~~~~~~~~~~~~

   创建一个 DataSet 是一个纯手工操作，需要事先知道并且定义数据的 schema;

.. _header-n1111:

Java: ``Encoders``
^^^^^^^^^^^^^^^^^^

.. code:: java

   import org.apache.spark.sql.Encoders;

   public class Flight implements Serializable{
       String DEST_COUNTRY_NAME;
       String ORIGIN_COUNTRY_NAME;
       Long DEST_COUNTRY_NAME;
   }

   DataSet<Flight> flights = spark.read
       .parquet("/data/flight-data/parquet/2010-summary.parquet/")
       .as(Encoders.bean(Flight.class));

.. _header-n1113:

Scala: ``case class``
^^^^^^^^^^^^^^^^^^^^^

Scala ``case class`` 的特征：

-  不可变(Immutable)

-  通过模式匹配可分解(Decomposable through pattern matching)

-  允许基于结构而不是参考进行比较(Allows for comparision based on
   structrue instead of reference)

-  易用、易操作(Easy to use and manipulate)

.. code:: scala

   // 定义 DataSet Flight 的 schema
   case class Flight(
       DEST_COUNTRY_NAME: String, 
       ORIGIN_COUNTRY_NAME: Stringf, 
       count: BigInt
   )

   val flightsDF = spark.read.
       .parquet("/data/flight-data/parquet/2010-summary.parquet/")
   val flights = flightsDF.as[Flight]

.. _header-n1126:

4.2 Actions
~~~~~~~~~~~

   DataFrame 上的 Action 操作也对 DataSet 有效;

.. code:: scala

   flights.show(2)
   flights.collect()
   flights.take()
   flights.count()

   flights.first.DEST_COUNTRY_NAME

.. _header-n1131:

4.3 Transformations
~~~~~~~~~~~~~~~~~~~

   -  DataFrame 上的 Transformation 操作也对 DataSet 有效;

   -  除了 DataFrame 上的 Transformation，DataSet
      上也有更加复杂和强类型的 Transformation 操作，因为，操作 DataSet
      相当于操作的是原始的 Java Virtual Machine (JVM) 类型.

.. _header-n1138:

DataFrame 上的 Transformation 操作
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. _header-n1140:

DataSet 特有的 Transformation 操作
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

-  Filtering

.. code:: scala

   def originIsDestination(flight_row: Flight): Boolean = {
       return flight_row.ORIGIN_COUNTRY_NAME == flight_row.DEST_COUNTRY_NAME
   }


   flights
       .filter(flight_row => originIsDestination(flight_row))
       .first()

-  Mapping

.. code:: scala

   val destinations = flights.map(f => f.DEST_COUNTRY_NAME)
   val localDestinations = destinations.take(5)

.. _header-n1149:

4.4 Joins
~~~~~~~~~

.. code:: scala

   case class FlightMetadata(
       count: BigInt, 
       randomData: BigInt
   )

   val flightsMeta = spark
       .range(500)
       .map(x => (x, scala.unit.Random.nextLong))
       .withColumnRenamed("_1", "count")
       .withColumnRenamed("_2", "randomData")
       .as[FlightMetadata]

   val flights2 = flights
       .joinWith(flightsMeta, flights.col("count") === flightsMeta.col("count"))

.. code:: scala

   flights2.selectExpr("_1.DEST_COUNTRY_NAME")
   flights2.take(2)
   val flights2 = flights.join(flightsMeta, Seq("count"))
   val flights2 = flights.join(flightsMeta.toDF(), Seq("count"))
   val flights2 = flights.join(flightsMeta.toDF(), Seq("count"))

.. _header-n1152:

4.5 Grouping and Aggregations
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

   -  DataSet 中的 Grouping 和 Aggregation 跟 DataFrame 中的 Grouping 和
      Aggregation 一样的用法，因此，\ ``groupBy``, ``rollup`` 和
      ``cube`` 对 DataSet 依然有效，只不过不再返回 DataFrame，而是返回
      DataSet，实际上是丢弃了 type 信息.

   -  如果想要保留 type
      信息，有一些方法可以实现，比如：\ ``groupByKey``\ ，\ ``groupByKey``
      可以通过 group 一个特殊的 DataSet key，然后返回带有 type 信息的
      DataSet；但是 ``groupByKey`` 不再接受一个具体的 column
      名字，而是一个函数，这样使得可以使用一些更加特殊的聚合函数来对数据进行聚合。但是这样做虽然灵活，却失去了性能上的优势。

.. code:: scala

   flights.groupBy("DEST_COUNTRY_NAME").count()
   flights.groupByKey(x => x.DEST_COUNTRY_NAME).count()

.. code:: scala

   flights.groupByKey(x => x.DEST_COUNTRY_NAME).count().explain

.. code:: scala

   def grpSum(countryName: String, values: Iterator[Flight]) = {
       values.dropWhile(_.count < 5).map(x => (countryName, x))
   }
   flights
       .groupByKey(x => x.DEST_COUNTRY_NAME)
       .flatMapGroups(grpSum)
       .show(5)

.. code:: scala

   def grpSum2(f: Flight): Integer = {
       1
   }
   flights2
       .groupByKey(x => x.DEST_COUNTRY_NAME)
       .mapValues(grpSum2)
       .count()
       .take(5)

.. code:: scala

   def sum2(left: Flight, right: Flight) = {
       Flight(left.DEST_COUNTRY_NAME, null, left.count + right.count)
   }

   flights
       .groupByKey(x => x.DEST_COUNTRY_NAME)
       .reduceGroups((l, r) => sum2(l, r))

.. code:: scala

   flights.groupBy("DEST_COUNTRY_NAME").count().explain
