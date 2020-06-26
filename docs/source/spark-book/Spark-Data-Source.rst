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

**(1) 核心结构:**

Spark 读取数据的核心结构如下:

.. code:: 

   DataFrameReader.format().option("key", "value").schema().load()

其中:

   -  ``format``: 是可选的,因为 Spark 默认读取 Parquet 格式的文件;

   -  ``option``: 可以设置很多自定义配置参数,不同的数据源格式都有自己的一些可选配置参数可以手动设置;

      -  ``mode``: ;

      -  ``inferSchema``: 是否启用 schema 推断;

      -  ``path``: ;

   -  ``schema``: 是可选的,有些数据文件提供了schema,可以使用模式推断(schema inference) 进行自动推断;也可以设置自定义的 schema 配置;

**(2) Spark 读取数据的基本操作:**

Spark 读取数据的操作的示例:

.. code:: scala

   // in Scala

   spark.read.format("parquet")
   	.option("mode", "FAILFAST")
   	.option("inferSchema", "true")
   	.option("path", "path/to/file(s)")
   	.schema(someSchema)
   	.load()

其中:

   -  ``spark.read``: DataFrameReader 是 Spark 读取数据的基本接口,可以通过 ``SparkSession`` 的 ``read`` 属性获取;

   - ``format``: 

   - ``schema``: 

   -  ``option``: 选项的设置可以通过创建一个配置项的映射结构来设置;

   -  read modes: 从外部源读取数据很容易会遇到错误格式的数据, 尤其是在处理半结构化数据时. 读取模式指定当 Spark 遇到错误格式的记录时应采取什么操作;

      -  ``option("mode", "permissive")``

         -  默认选项, 当遇到错误格式的记录时, 将所有字段设置为 ``null`` 并将所有错误格式的记录放在名为 ``_corrupt_record`` 字符串中;

      -  ``option("mode", "dropMalformed")``

         -  删除包含错误格式记录的行;

      -  ``option("mode", "failFast")``

         -  遇到错误格式的记录后立即返回失败;

.. note::

   ``format``, ``option`` 和 ``schema`` 都会返回一个 ``DataFrameReader``,它可以进行进一步的转换,并且都是可选的.每个数据源都有一组特定的选项,用于设置如何将数据读入 Spark.

.. _header-n76:

1.2 Write API Structure
~~~~~~~~~~~~~~~~~~~~~~~

**(1) 核心结构:**

Spark 写数据的核心结构如下:

.. code:: 

   DataFrameWriter.format().option().partitionBy().bucketBy().sortBy().save()

其中:

   -  ``format``: 是可选的,因为 Spark 默认会将数据保存为 Parquet 文件格式;

   -  ``option``: 可以设置很多自定义配置参数,不同的数据源格式都有自己的一些可选配置参数可以手动设置;

   -  ``partitionBy``, ``bucketBy``, ``sortBy``: 只对文件格式的数据起作用,可以通过设置这些配置对文件在目标位置存放数据的结构进行配置;

**(2) Spark 读取数据的基本操作:**

Spark 写数据的操作的示例:

.. code:: scala
   
   // in Scala

   dataframe.write.format("parquet")
   	.option("mode", "OVERWRITE")
   	.option("dataFormat", "yyyy-MM-dd")
   	.option("path", "path/to/file(s)")
   	.save()

其中:

   -  ``dataframe.write``: DataFrameWriter 是 Spark 写出数据的基本接口,可以通过 ``DataFrame`` 的 ``write`` 属性来获取;

   -  ``format``: ;

   -  ``option`` 选项的设置还可以通过创建一个配置项的映射结构来设置;

   -  write modes:

      -  ``option("mode", "errorIfExists")``

         -  默认选项, 如果目标路径已经存在数据或文件,则抛出错误并返回写入操作失败;
      
      -  ``option("mode", "append")``

         -  将输出文件追加到目标路径已经存在的文件上或目录的文件列表;

      -  ``option("mode", "overwrite")``

         -  将完全覆盖目标路径中已经存在的任何数据;

      -  ``option("mode", "ignore")``

         -  如果目标路径已经存在数据或文件,则不执行任何操作;

.. _header-n119:

2.Spark 读取 CSV 文件
---------------------

CSV (comma-separated values), 是一种常见的文本文件格式, 其中每行表示一条记录, 用逗号分隔记录中的每个字段. 
虽然 CSV 文件看起来结构良好, 实际上它存在各种各样的问题, 是最难处理的文件格式之一,
这是因为实际应用场景中遇到的数据内容或数据结构并不会那么规范. 因此, CSV 读取程序包含大量选项, 
通过这些选项可以帮助你解决像解决忽略特定字符等的这种问题,比如当一列的内容也以逗号分隔时, 
需要识别出该逗号是列中的内容,还是列间分隔符.


.. _header-n120:

2.1 CSV Read/Write Options
~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------+------------------------------+-----------------------------+----------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
| Read/Write | Key                          | Potential values            | Default                          | Description                                                                                                                        |
+============+==============================+=============================+==================================+====================================================================================================================================+
| Read/Write | ``sep``                      | Any single string character | ``,``                            | This single character that is used as separator for each field and value.                                                          |
+------------+------------------------------+-----------------------------+----------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
| Read/Write | ``header``                   | ``true/false``              | ``false``                        | A Boolean flag that declares whether the first line in the file(s) are the names of the columns.                                   |
+------------+------------------------------+-----------------------------+----------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
| Read       | ``escape``                   | Any string character        | ``\``                            | The character Spark should use to escape other characters in the file.                                                             |
+------------+------------------------------+-----------------------------+----------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
| Read       | ``inferSchema``              | ``true/false``              | ``false``                        | Specifies whether Spark should infer column types when reading the file.                                                           |
+------------+------------------------------+-----------------------------+----------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
| Read       | ``ignoreLeadingWhiteSpace``  | ``true/false``              | ``false``                        | Declares whether leading spaces from value being read should be skipped.                                                           |
+------------+------------------------------+-----------------------------+----------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
| Read       | ``ignoreTrailingWhiteSpace`` | ``true/false``              | ``false``                        | Declares whether trailing spaces from value being read should be skipped.                                                          |
+------------+------------------------------+-----------------------------+----------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
| Read/Write | ``nullValue``                | Any string character        | ``""``                           | Declares what character represents a ``null`` value in the file.                                                                   |
+------------+------------------------------+-----------------------------+----------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
| Read/Write | ``nanValue``                 | Any string character        | ``NaN``                          | Declares what character represents a ``NaN``  or missing character in the CSV file.                                                |
+------------+------------------------------+-----------------------------+----------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
| Read/Write | ``positiveInf``              | Any string or character     | ``Inf``                          | Declares what character(s) represent a positive infinite value.                                                                    |
+------------+------------------------------+-----------------------------+----------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
| Read/Write | ``negativeInf``              | Any String or character     | ``-Inf``                         | Declares what character(s) represent a positive negative infinite value.                                                           |
+------------+------------------------------+-----------------------------+----------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
| Read/Write | ``compression/codec``        | None, uncom pressed, bzip2, | ``none``                         | Declares  what compression codec Spark should use to read or write the file.                                                       |
|            |                              | deflate,gzip,lz4, or snappy |                                  |                                                                                                                                    |
+------------+------------------------------+-----------------------------+----------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
| Read/Write | ``dateFormat``               | Any string or character that| ``yyyy-MM-dd``                   | Declares the date format for any columns that are date type.                                                                       |
|            |                              | conform to java's           |                                  |                                                                                                                                    |
|            |                              | SimpleDataFormat            |                                  |                                                                                                                                    |
+------------+------------------------------+-----------------------------+----------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
| Read/Write | ``timestampFormat``          | Any string or character that| ``yyyy-MM-dd'T' HH:mm:ss.SSSZZ`` | Declares the timestamp format for any columns that are timestamp type                                                              |
|            |                              | conform to java's           |                                  |                                                                                                                                    |
|            |                              | SimpleDataFormat            |                                  |                                                                                                                                    |
+------------+------------------------------+-----------------------------+----------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
| Read       | ``maxColumns``               | Any integer                 | ``20480``                        | Declares the maximum number for columns in the file.                                                                               |
+------------+------------------------------+-----------------------------+----------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
| Read       | ``maxCharsPerColumn``        | Any integer                 | ``1000000``                      | Declares the maximum number of character in a column.                                                                              |
+------------+------------------------------+-----------------------------+----------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
| Read       | ``escapeQuotes``             | ``true/false``              | ``true``                         | Declares whether Spark should escape quotes that are found in lines.                                                               |
+------------+------------------------------+-----------------------------+----------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
| Read       |``maxMalformeLogPerPartition``| Any integer                 | ``10``                           | Sets the maximum number of malformed rows Spark will log for each partition. Malformed records beyond this number will be ignore.  |
+------------+------------------------------+-----------------------------+----------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
| Read       | ``multiLine``                | ``true/false``              | ``false``                        | This option allows you read multiline CSV files where each logical row in the CSV file might span multipe rows in the file itself. |
+------------+------------------------------+-----------------------------+----------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
| Write      | ``quoteAll``                 | ``true/false``              | ``false``                        | Specifies whether all values should be enclosed in quotes, as opposed to just escaping values that have a quote character.         |
+------------+------------------------------+-----------------------------+----------------------------------+------------------------------------------------------------------------------------------------------------------------------------+

.. _header-n178:

2.2 Spark Reading CSV 文件
~~~~~~~~~~~~~~~~~~~~~~~~~~

示例 1:

.. code-block:: scala  

   // in Scala

   val csvFile = spark.read.format("csv")
      .option("header", "true")
      .option("mode", "FAILFAST")
      .option("inferSchema", "true")
      .load("some/path/to/file.csv")
      .show()


示例 2:

.. code:: python

   # in Python
   
   csvFile = spark.read.format("csv") \
   	.option("header", "true") \
   	.option("mode", "FAILFAST") \
   	.option("inferSchema", "true") \
   	.load("/data/flight-data/csv/2010-summary.csv")
      .show()


示例 3:

.. code-block:: scala  

   // in Scala

   import org.apache.spark.sql.types.{StructType, StructField, StringType, LongType}

   val myManualSchema = new StructType(Array(
      new StructField("DEST_COUNTRY_NAME", StringType, true),
      new StructField("ORIGIN_COUNTRY_NAME", StringType, true),
      new StructField("count", LongType, false)
   ))

   val csvFile = spark.read.format("csv")
      .option("header", "true")
      .option("mode", "FAILFAST")
      .schema(myManualSchema)
      .load("/data/flight-data/csv/2010-summary.csv")
      .show()


.. note::

   通常, Spark 只会在作业执行而不是 DataFrame 定义时发生失败.


.. _header-n216:

2.3 Spark Writing CSV 文件
~~~~~~~~~~~~~~~~~~~~~~~~~~

示例 1:

.. code:: scala

   // in Scala

   csvFile.write.format("csv")
   	// .mode("overwrite")
   	.option("mode", "overwrite")
   	.option("sep", "\t")
   	.save("/tmp/my-tsv-file.tsv")

示例 2:

.. code:: python

   # in Python

   csvFile.write.format("csv") \
   	# .mode("overwrite") \
   	.option("mode", "overwrite") \
   	.option("sep", "\t") \
      .save("/tmp/my-tsv-file.tsv")

.. _header-n224:

3.Spark 读取 JSON 文件
----------------------

JSON (JavaScript Object Notation). 在 Spark 中, 提及的 ``JSON 文件`` 指 ``换行符分隔的 JSON``, 
每行必须包含一个单独的、独立的有效 JSON 对象, 这与包含大的 JSON 对象或数组的文件是有区别的.

换行符分隔 JSON 对象还是一个对象可以跨越多行, 这个可以由 ``multiLine`` 选项控制, 当 ``multiLine`` 为 ``true`` 时, 
则可以将整个文件作为一个 JSON 对象读取, 并且 Spark 将其解析为 DataFrame. 换行符分隔的 JSON 实际上是一种更稳定的格式, 
因为它可以在文件末尾追加新纪录(而不是必须读入整个文件然后再写出).

换行符分隔的 JSON 格式流行的另一个关键原因是 JSON 对象具有结构化信息, 并且(基于 JSON的) JavaScript 也支持基本类型, 这使得它更易用, Spark 可以替我们完成很多对结构化数据的操作. 
由于 JSON 结构化对象封装的原因, 导致 JSON 文件选项比 CSV 的要少很多.

3.1 JSON Read/Write Options
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+------------+-------------------------------+-----------------------------+-----------------------------------------------------+-----------------------------------------------------------------------------------------+
| Read/Write | Key                           | Potential values            | Default                                             | Description                                                                             |
+============+===============================+=============================+=====================================================+=========================================================================================+
| Read/Write | ``compression/codec``         | None, uncom pressed, bzip2, | ``none``                                            | Declares  what compression codec Spark should use to read or write the file.            |
|            |                               | deflate,gzip,lz4, or snappy |                                                     |                                                                                         |
+------------+-------------------------------+-----------------------------+-----------------------------------------------------+-----------------------------------------------------------------------------------------+
| Read/Write | ``dateFormat``                | Any string or character that| ``yyyy-MM-dd``                                      | Declares the date format for any columns that are date type.                            |
|            |                               | conform to Java's           |                                                     |                                                                                         |
|            |                               | SimpleDataFormat            |                                                     |                                                                                         |
+------------+-------------------------------+-----------------------------+-----------------------------------------------------+-----------------------------------------------------------------------------------------+
| Read/Write | ``timestampFormat``           | Any string or character that| ``yyyy-MM-dd'T' HH:mm:ss.SSSZZ``                    | Declares the timestamp format for any columns that are timestamp type                   |
|            |                               | conform to java's           |                                                     |                                                                                         |
|            |                               | SimpleDataFormat            |                                                     |                                                                                         |
+------------+-------------------------------+-----------------------------+-----------------------------------------------------+-----------------------------------------------------------------------------------------+
| Read       | ``primitiveAsString``         | ``true/false``              | ``false``                                           | Infers all primitive values as string type.                                             |
+------------+-------------------------------+-----------------------------+-----------------------------------------------------+-----------------------------------------------------------------------------------------+
| Read       | ``allowComments``             | ``true/false``              | ``false``                                           | Ignores Java/C++ style comment in JSON  records.                                        |
+------------+-------------------------------+-----------------------------+-----------------------------------------------------+-----------------------------------------------------------------------------------------+
| Read       | ``allowUnquotedFieldNames``   | ``true/false``              | ``false``                                           | Allows unquotes JSON field names.                                                       |
+------------+-------------------------------+-----------------------------+-----------------------------------------------------+-----------------------------------------------------------------------------------------+
| Read       | ``allowSingleQuotes``         | ``true/false``              | ``true``                                            | Allows single quotes in addition to double quotes.                                      |
+------------+-------------------------------+-----------------------------+-----------------------------------------------------+-----------------------------------------------------------------------------------------+
| Read       | ``allNumericLeadingZeros``    | ``true/false``              | ``false``                                           | Allows leading zeroes in number (e.g., 00012).                                          |
+------------+-------------------------------+-----------------------------+-----------------------------------------------------+-----------------------------------------------------------------------------------------+
| Read       | ``allowBackslashEscAPIngAny`` | ``true/false``              | ``false``                                           | Allows accepting quoting of all characters using backlash quoting mechanism.            |
+------------+-------------------------------+-----------------------------+-----------------------------------------------------+-----------------------------------------------------------------------------------------+
| Read       | ``columnNameOfCorruptRecord`` | Any string                  | Value of ``spark.sql.column & NameOfCorruptRecord`` | Allows renaming the new field having a malformed string created by ``permissive`` mode. |
|            |                               |                             |                                                     | This will override the configuration value.                                             |
+------------+-------------------------------+-----------------------------+-----------------------------------------------------+-----------------------------------------------------------------------------------------+
| Read       | ``multiLine``                 | ``true/false``              | ``false``                                           | Allows for reading in non-line-delimited JSON files.                                    |
+------------+-------------------------------+-----------------------------+-----------------------------------------------------+-----------------------------------------------------------------------------------------+

3.2 Spark Reading JSON 文件
~~~~~~~~~~~~~~~~~~~~~~~~~~~

示例 1:

.. code-block:: scala

   // in Scala

   import org.apache.spark.sql.types.{StructType, StructField, StringType, LongType}

   val myManualSchema = new StructType(Array(
      new StructField("DEST_COUNTRY_NAME", StringType, true),
      new StructField("ORIGIN_COUNTRY_NAME", StringType, true),
      new StructField("count", LongType, false)
   ))

   spark.read.format("json")
      .option("mode", "FAILFAST")
      .schema("myManualSchema")
      .load("/data/flight-data/json/2010-summary.json")
      .show(5)

示例 2:

.. code-block:: python

   # in Python

   spark.read.format("json") \
      .option("mode", "FAILFAST") \
      .option("inferSchema", "true") \
      .load("/data/flight-data/json/2010-summary.json") \
      .show(5)


3.3 Spark Writing JSON 文件
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

示例 1:

.. code-block:: scala

   // in Scala

   csvFile.write.format("json")
      .mode("overwrite")
      .save("/tmp/my-json-file.json")


示例 2:

.. code-block:: python

   # in Python

   csvFile.write.format("json") \
      .mode("overwrite") \
      .save("/tmp/my-json-file.json")


.. note:: 

   每个数据分片作为一个文件写出, 而整个 DataFrame 将输出到一个文件夹. 文件中每行仍然代表一个 JSON 对象:

   .. code-block:: shell

      $ ls /tmp/my-json-file.json/
      /tmp/my-json-file.json/part-0000-tid-543....json


.. _header-n226:

4.Spark 读取 Parquet 文件
-------------------------

Parquet 是一种开源的面向列的数据存储格式,它提供了各种存储优化,尤其适合数据分析.
Parquet 提供列压缩从而可以节省空间,而且它支持按列读取而非整个文件地读取.

作为一种文件格式,Parquet 与 Apache Spark 配合得很好,而且实际上也是 Spark 的默认文件格式.
建议将数据写到 Parquet 以便长期存储,因为从 Parquet 文件读取始终比从 JSON 文件或 CSV 文件效率更高.

Parquet 的另一个优点是它支持复杂类型,也就是说如果一个数组(CSV 文件无法存储组列)、map 映射或 struct 结构体,
仍可以正常读取和写入,不会出现任何问题.


4.1 Parquet Read/Write Options
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

由于 Parquet 含有明确定义且与 Spark 概念密切一致的规范,所以它只有很少的可选项,实际上只有两个.
Parqute 的可选项很少,因为它在存储数据时执行本身的 schema.

虽然只有两个选项,但是如果使用的是不兼容的 Parquet 文件,仍然会遇到问题. 
并且,当使用不同版本的 Spark 写入 Parquet 文件时要小心,因为这可能会导致让人头疼的问题.

+------------+------------------------------+-----------------------------+-----------------------------------+------------------------------------------------------------------------------+
| Read/Write | Key                          | Potential values            | Default                           | Description                                                                  |
+============+==============================+=============================+===================================+==============================================================================+
| Write      | ``compression`` or ``codec`` | None, uncom pressed, bzip2, | ``none``                          | Declares  what compression codec Spark should use to read or write the file. |
|            |                              | deflate,gzip,lz4, or snappy |                                   |                                                                              |
+------------+------------------------------+-----------------------------+-----------------------------------+------------------------------------------------------------------------------+
| Read       | ``merge Schema``             | ``true/false``              | Value of the configuration        | You can incrementally add columns to newly written Parquet files             |
|            |                              |                             | ``spark.sql.parquet.mergeSchema`` | in the same table/folder. Use this option to enable or disable this feature. |
+------------+------------------------------+-----------------------------+-----------------------------------+------------------------------------------------------------------------------+

4.2 Spark Reading Parquet 文件
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

示例 1:

.. code-block:: scala

   // in Scala

   spark.read.format("parquet")
      .load("/data/flight-data/parquet/2010-summary.parquet")
      .show(5)

示例 2:

.. code-block:: python

   # in Python
   
   spark.read.format("parquet") \
      .load("/data/flight-data/parquet/2010-summary.parquet") \
      . show(5)

4.3 Spark Writing Parquet 文件
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

示例 1:

.. code-block:: scala

   // in Scala

   csvFile.write.format("parquet")
      .mode("overwrite")
      .save("/tmp/my-parquet-file.parquet")

示例 2:

.. code-block:: python

   # in Python

   csvFile.write.format("parquet") \
      .mode("overwrite") \
      .save("/tmp/my-parquet-file.parquet")


.. _header-n228:

5.Spark 读取 ORC 文件
---------------------

ORC 是为 Hadoop 作业而设计的自描述、类型感知的列存储文件格式.它针对大型流式数据读取进行优化, 
但集成了对快速查找所需行的相关支持.

实际上,读取 ORC 文件数据时没有可选项,这是因为 Spark 非常了解该文件格式.

ORC 和 Parquet 有什么区别？在大多数情况下,他们非常相似,本质区别是, 
Parquet 针对 Spark 进行了优化,而 ORC 则是针对 Hive 进行了优化.

5.2 Spark Reading ORC 文件
~~~~~~~~~~~~~~~~~~~~~~~~~~~

示例 1:

.. code-block:: scala

   // in Scala

   spark.read.format("orc")
      .load("/data/flight-data/orc/2010-summary.orc")
      .show()

示例 2:

.. code-block:: python

   # in Python

   spark.read.format("orc") \
      .load("/data/flight-data/orc/2010-summary.orc") \
      .show()

5.3 Spark Writing ORC 文件
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

示例 1:

.. code-block:: scala
   
   // in Scala

   csvFile.write.format("orc")
      .mode("overwrite")
      .save("/tmp/my-json-file.orc")

示例 2:

.. code-block:: python

   # in Python

   csvFile.write.format("orc") \
      .mode("overwrite") \
      .save("/tmp/my-json-file.orc")

.. _header-n230:

6.Spark 读取 SQL Database
-------------------------

数据库不仅仅是一些数据文件，而是一个系统，有许多连接数据库的方式可供选择。
需要确定 Spark 集群网络是否容易连接到数据库系统所在的网络上

读写数据库中的文件需要两步：

   1. 在 Spark 类路径中为指定的数据库包含 Java Database Connectivity(JDBC) 驱动;

   2. 为连接驱动器提供合适的 JAR 包;

示例:

   .. code-block:: bash

      ./bin/spark-shell \
      --driver-class-path postgresql-9.4.1207.jar \
      --jars postgresql-9.4.1207.jar


6.1 SQLite
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

SQLite 可以在本地计算机以最简配置工作，但在分布式环境中不行，如果想在分布式环境中运行这里的示例，则需要连接到其他数据库

SQLite 是目前使用最多的数据库引擎，它功能强大、速度快且易于理解，只是因为 SQLite 数据库只是一个文件.



6.2 JDBC 数据源 Options
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

   - Url: 要连接的 JDBC URL

   - dbtable: 表示要读取的 JDBC 表

   - dirver: 用于连接到此 URL 的 JDBC 驱动器的类名

   - partitionColumn, lowerBound, upperBound: 描述了如何在从多个 worker 并行读取时对表格进行划分

   - numPartitions: 在读取和写入数据表时，数据表可用于并行的最大分区数，这也决定了并发 JDBC 连接的最大数目

   - fetchsize: 表示 JDBC 每次读取多少条记录

   - batchsize: 表示 JDBC 批处理的大小，用于指定每次写入多少条记录

   - isolationLevel: 表示数据库的事务隔离级别(适用于当前连接)

   - truncate: 

   - createTableOptions: 

   - createTableColumnTypes: 表示创建表时使用的数据库列数据类型，而不使用默认值



6.2 Spark Reading From SQL Database
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

示例 1:

.. code-block:: scala

   // in Scala

   import java.sql.DirverManager

   // 指定格式和选项
   val dirver = "org.sqlite.JDBC"
   val path = "/data/flight-data/jdbc/my-sqlite.db"
   val url = s"jdbc::sqlite:/${path}"
   val tablename = "flight_info"

   // 测试连接
   val connection = DirverManager.getConnection(url)
   connection.isClosed()
   connection.close()

   // 从 SQL 表中读取 DataFrame
   val dbDataFrame = spark
      .read
      .format("jdbc")
      .option("url", url)
      .option("dbtable", tablename)
      .option("driver", driver)
      .load()


示例 2:

.. code-block:: python

   # in Python
   
   # 指定格式和选项
   driver = "org.sqlite.JDBC"
   path = "/data/flight-data/jdbc/my-sqlite.db"
   url = "jdbc:sqlite:"  + path
   tablename = "flight_info"

   # 从 SQL 表中读取 DataFrame
   dbDataFrame = spark \
      .read \
      .format("jdbc") \
      .option("url", url) \
      .option("dbtable", tablename) \
      .option("driver", driver)
      .load() 

示例 3:

.. code-block:: scala

   // in Scala

   // 指定格式和选项
   val driver = "org.postgresql.Driver"
   val url = "jdbc:postgresql://database_server"
   val tablename = "schema.tablename"
   val username = "username"
   val password = "my-secret-password"

   // 从 SQL 表中读取 DataFrame
   val pgDF = spark
      .read
      .format("jdbc")
      .option("driver", driver)
      .option("url", url)
      .option("dbtable", tablename)
      .option("user", username)
      .option("password", password)
      .load()

示例 4:

.. code-block:: python
   
   # in Python

   // 指定格式和选项
   driver = "org.postgresql.Driver"
   url = "jdbc:postgresql://database_server"
   tablename = "schema.tablename"
   username = "username"
   password = "my-secret-password"

   # 从 SQL 表中读取 DataFrame
   pgDF = spark \
      .read \
      .format("jdbc") \
      .option("driver", driver) \
      .option("url", url) \
      .option("dbtable", tablename) \
      .option("user", username) \
      .option("password", password) \
      .load()


结果查询:

.. code-block:: scala

   dbDataFrame.select("DEST_COUNTRY_NAME").distinct().show(5)


6.3 查询下推
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


6.3.1 并行读取数据库
^^^^^^^^^^^^^^^^^^^^^^^^^



6.3.2 基于滑动窗口的分区
^^^^^^^^^^^^^^^^^^^^^^^^^




6.3 Spark Writing To SQL Database
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

写入 SQL 数据库只需指定 URI 并指定写入模式来写入数据库即可.

示例 1:

.. code-block:: scala

   // in Scala

   val newPath = "jdbc:sqlite://tmp/my-sqlite.db"

   csvFile
      .write
      .mode("overwrite")
      .jdbc(newPath, tablename, props)

示例 2:

.. code-block:: python

   # in Python

   newPath = "jdbc:sqlite://tmp/my-sqlite.db"

   csvFile
      .write
      .jdbc(newPath, tablename, mode = "overwrite", properites = props)

示例 3:

.. code-block:: scala

   // in Scala

   val newPath = "jdbc:sqlite://tmp/my-sqlite.db"

   csvFile
      .write
      .mode("append")
      .jdbc(newPath, tablename, props)

示例 4:

.. code-block:: python

   # in Python

   newPath = "jdbc:sqlite://tmp/my-sqlite.db"

   csvFile
      .write
      .jdbc(newPath, tablename, mode = "append", properites = props)


查看结果:

示例 1:

.. code-block:: scala

   // in Scala

   spark
      .read
      .jdbc(newPath, tablename, props)
      .count() // 255

示例 2:

.. code-block:: python

   # in Python

   spark
      .read
      .jdbc(newPath, tablename, properites = props)
      .count() # 255

示例 3:

.. code-block:: scala

   // in Scala

   spark
      .read
      .jdbc(newPath, tablename, props)
      .count() // 765

示例 4:

.. code-block:: python

   # in Python

   spark
      .read
      .jdbc(newPath, tablename, properites = props)
      .count() # 765



.. _header-n232:

7.Spark 读取 Text 文件
----------------------

Spark 还支持读取纯文本文件,文件中每一行将被解析为 DataFrame 中的一条记录,然后根据要求进行转换.

假设需要将某些 Apache 日志文件解析为结构化的格式,或是想解析一些纯文本以进行自然语言处理,这些都需要操作文本文件.
由于文本文件能够充分利用原生(native tye)的灵活性,因此它很适合作为 Dataset API 的输入.

7.2 Spark Reading Text 文件
~~~~~~~~~~~~~~~~~~~~~~~~~~~

读取文本文件非常简单, 只需指定类型为 ``textFile`` 即可:

   - 如果使用 ``textFile``, 分区目录名将被忽略. 

   - 如果要根据分区读取和写入文本文件, 应该使用 ``text``,它会在读写时考虑分区.

示例 1:

.. code-block:: scala

   spark.read.textFile("/data/flight-data/csv/2010-summary.csv")
      .selectExpr("split(value, ',') as rows")
      .show()

7.3 Spark Writing Text 文件
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

当写文本文件时, 需确保仅有一个字符串类型的列写出; 否则, 写操作将失败.

示例 1:

.. code-block:: scala

   // in Scala

   csvFile
      .select("DEST_COUNTRY_NAME")
      .write.text("/tmp/simple-text-file.txt")

示例 2:

.. code-block:: python

   # in Python

   csvFile \
      .limit(10) \
      .select("DEST_COUNTRY_NAME", "count") \
      .write.partitionBy("count") \
      .text("/tmp/five-csv-file2py.csv")


.. _header-n234:

8.高级 I/O
----------

8.1 可分割的文件类型和压缩
~~~~~~~~~~~~~~~~~~~~~~~~~~~



8.2 并行读数据
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~




8.3 并行写数据
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

8.3.1 数据划分
^^^^^^^^^^^^^^^^^^^^^



8.3.2 数据分桶
^^^^^^^^^^^^^^^^^^^^^



8.4 写入复杂类型
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


8.5 管理文件大小
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~



8.6 Cassandra Connector
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- `Cassandra Connector` https://github.com/datastax/spark-cassandra-connector

.. note:: 

   有很多方法可以用于实现自定义的数据源, 但由于 API 正在不断演化发展（为了更好地支持结构化流式处理）.