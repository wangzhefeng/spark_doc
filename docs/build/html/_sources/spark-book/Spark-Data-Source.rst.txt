.. _header-n0:

Spark Data Sources
===============================

Spark 核心数据源:

-  CSV

-  JSON

-  Parquet

-  ORC

-  JDBC/ODBC connection

-  Plain-text file

其他数据源(Spark Community):

-  Cassandra

-  HBase

-  MongoDB

-  AWS Redshift

-  XML

-  others...

.. _header-n31:

1.Spark 数据源 API
------------------

.. _header-n32:

1.1 Read API Structure
~~~~~~~~~~~~~~~~~~~~~~

**(1) 核心结构：**

.. code:: 

   DataFrameReader.format().option("key", "value").schema().load()

-  ``format`` 是可选的，因为 Spark 默认读取 Parquet 格式的文件；

-  ``option``
   可以设置很多自定义配置参数，不同的数据源格式都有自己的一些可选配置参数可以手动设置；

   -  ``mode``

   -  ``inferSchema``

   -  ``path``

-  ``schema`` 是可选的，有些数据文件提供了schema，可以使用模式推断
   (schema inference) 进行自动推断；也可以设置自定义的 schema 配置；

**(2) Spark 读取数据的基本操作：**

.. code:: scala

   spark.read.format("parquet")
   	.option("mode", "FAILFAST")
   	.option("inferSchema", "true")
   	.option("path", "path/to/file(s)")
   	.schema(someSchema)
   	.load()

-  ``DataFrameReader`` 是 Spark 读取数据的基本接口，可以通过
   ``spark.read`` 获取；

-  ``option`` 选项的设置还可以通过创建一个配置项的映射结构来设置；

-  读取模式(read modes):

   -  ``option("mode", "permissive")``

      -  默认选项

      -  Set all fields to ``null`` when it encounters a corrupted
         record and places all corrupted records in a string column
         called ``_corrupt_record``;

   -  ``option("mode", "dropMalformed")``

      -  Drop the row that contains malformed records;

   -  ``option("mode", "failFast")``

      -  Fails immediately upon encountering malformed malformed
         records;

.. _header-n76:

1.2 Write API Structure
~~~~~~~~~~~~~~~~~~~~~~~

**(1) 核心结构：**

.. code:: 

   DataFrameWriter.format().option().partitionBy().bucketBy().sortBy().save()

-  ``format`` 是可选的，因为 Spark 默认会将数据保存为 Parquet 文件格式；

-  ``option``
   可以设置很多自定义配置参数，不同的数据源格式都有自己的一些可选配置参数可以手动设置；

-  ``partitionBy``, ``bucketBy``, ``sortBy``
   只对文件格式的数据起作用，可以通过设置这些配置对文件在目标位置存放数据的结构进行配置；

**(2) Spark 读取数据的基本操作：**

.. code:: scala

   dataframe.write.format("parquet")
   	.option("mode", "OVERWRITE")
   	.option("dataFormat", "yyyy-MM-dd")
   	.option("path", "path/to/file(s)")
   	.save()

-  ``DataFrameWriter`` 是 Spark 写出数据的基本接口，可以通过
   ``dataframe.write`` 获取；

-  ``option`` 选项的设置还可以通过创建一个配置项的映射结构来设置；

-  读取模式(read modes):

   -  ``option("mode", "append")``

      -  Appends the output files to the list of files that already
         exist at that location;

   -  ``option("mode", "overwrite")``

      -  Will completely overwrite any data that already exists there;

   -  ``option("mode", "errorIfExists")``

      -  默认选项

      -  Throw an error and fails the write if data or files already
         exists at the specified location;

   -  ``option("mode", "ignore")``

      -  If data or files exist at the location, do nothing with the
         current DataFrame;

.. _header-n119:

2.Spark 读取 CSV 文件
---------------------

.. _header-n120:

2.1 CSV Read Options
~~~~~~~~~~~~~~~~~~~~

+--------------------------+-----------------------------+-----------+------+
| Key                      | 可选项                      | 默认值    | 含义 |
+==========================+=============================+===========+======+
| sep                      | Any single string character | ``,``     |      |
+--------------------------+-----------------------------+-----------+------+
| header                   | ``true/false``              | ``false`` |      |
+--------------------------+-----------------------------+-----------+------+
| escape                   | Any string character        | ``\``     |      |
+--------------------------+-----------------------------+-----------+------+
| inferSchema              | ``true/false``              | ``false`` |      |
+--------------------------+-----------------------------+-----------+------+
| ignoreLeadingWhiteSpace  | ``true/false``              | ``false`` |      |
+--------------------------+-----------------------------+-----------+------+
| ignoreTrailingWhiteSpace | ``true/false``              | ``false`` |      |
+--------------------------+-----------------------------+-----------+------+
| nullValue                | Any string character        | ``""``    |      |
+--------------------------+-----------------------------+-----------+------+
| nanValue                 | Any string character        | ``NaN``   |      |
+--------------------------+-----------------------------+-----------+------+
| positiveInf              | Any string or character     | ``Inf``   |      |
+--------------------------+-----------------------------+-----------+------+
| negativeInf              | Any String or character     | ``-Inf``  |      |
+--------------------------+-----------------------------+-----------+------+

.. _header-n178:

2.2 Spark Reading CSV 文件
~~~~~~~~~~~~~~~~~~~~~~~~~~

.. _header-n179:

2.3 CSV Write Options
~~~~~~~~~~~~~~~~~~~~~

+-------------+-----------------------------+-----------+------+
| Key         | 可选项                      | 默认值    | 含义 |
+=============+=============================+===========+======+
| sep         | Any single string character | ``,``     |      |
+-------------+-----------------------------+-----------+------+
| header      | ``true/false``              | ``false`` |      |
+-------------+-----------------------------+-----------+------+
| nullValue   | Any string character        | ``""``    |      |
+-------------+-----------------------------+-----------+------+
| nanValue    | Any string character        | ``NaN``   |      |
+-------------+-----------------------------+-----------+------+
| positiveInf | Any string or character     | ``Inf``   |      |
+-------------+-----------------------------+-----------+------+
| negativeInf | Any String or character     | ``-Inf``  |      |
+-------------+-----------------------------+-----------+------+

.. _header-n216:

2.4 Spark Writing CSV 文件
~~~~~~~~~~~~~~~~~~~~~~~~~~

读取 CSV 文件：

.. code:: scala

   // in Scala
   val myManualSchema = 
   val csvFile = spark.read.format("csv")
   	.option("header", "true")
   	.option("mode", "FAILFAST")
   	.schema(myManualSchema)
   	.load("/data/flight-data/csv/2010-summary.csv")

.. code:: python

   # in Python
   csvFile = spark.read.format("csv") \
   	.option("header", "true") \
   	.option("mode", "FAILFAST") \
   	.option("inferSchema", "true") \
   	.load("/data/flight-data/csv/2010-summary.csv")

写入 CSV 文件：

.. code:: scala

   // in Scala
   csvFile.write.format("csv")
   	// .mode("overwrite")
   	.option("mode", "overwrite")
   	.option("sep", "\t")
   	.save("/tmp/my-tsv-file.tsv")

.. code:: python

   # in Python
   csvFile.write.format("csv") \
   	# .mode("overwrite") \
   	.option("mode", "overwrite") \
   	.option("sep", "\t") \

.. _header-n224:

3.Spark 读取 JSON 文件
----------------------

.. _header-n226:

4.Spark 读取 Parquet 文件
-------------------------

.. _header-n228:

5.Spark 读取 ORC 文件
---------------------

.. _header-n230:

6.Spark 读取 SQL Database
-------------------------

.. _header-n232:

7.Spark 读取 Text 文件
----------------------

.. _header-n234:

8.高级 I/O
----------
