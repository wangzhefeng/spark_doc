.. _header-n0:

Spark 应用程序
==============

.. _header-n3:

1 Spark Run on cluster
----------------------

.. _header-n5:

2 开发 Spark 应用程序
---------------------

Spark 应用程序：

-  a Spark cluster

-  application code

.. _header-n12:

2.1 Spark App
~~~~~~~~~~~~~

.. _header-n13:

2.1.1 Scala App
^^^^^^^^^^^^^^^

Build applications using Java Virtual Machine(JVM) based build tools:

-  sbt

-  Apache Maven

**1.Build applications using sbt**

-  Configure an sbt build for Scala application with a ``build.sbt``
   file to manage the package information:

   -  Project metadata(package name, package versioning information,
      etc.)

   -  Where to resolve dependencies

   -  Dependencies needed for your library

.. code:: sbt

   // build.stb

   name := "example"
   organization := "com.databricks"
   scalaVersion := "2.11.8"

   // Spark Information
   val sparkVersion = "2.2.0"

   // allows us to include spark packages
   resolvers += "bintray-spark-packages" at 
   	"https://dl.bintray.com/spark-package/maven/"

   resolvers += "Typesafe Simple Repository" at
   	"http://repo.typesafe.com/typesafe/simple/maven-releases/"

   resolvers += "MavenRepository" at
   	"https://mvnrepository.com/"

   libraryDependencies ++= Seq(
   	// Spark core
   	"org.apache.spark" %% "spark-core" % sparkVersion,
   	"org.apache.spark" %% "spark-sql" % sparkVersion,
   	// the rest of the file is omitted for brevity
   )

**2.Build the Project directories using standard Scala project
structure**

.. code:: shell

   src/
   	main/
   		resources/
   			<files to include in main jar here>
   		scala/
   			<main Scala sources>
   		java/
   			<main Java sources>
   	test/
   		resources/
   			<files to include in test jar here>
   		scala/
   			<test Scala sources>
   		java/
   			<test Java sources>

**3.Put the source code in the Scala and Java directories**

.. code:: scala

   // in Scala
   // src/main/scala/DataFrameExample.scala

   import org.apache.spark.sql.SparkSession

   object DataFrameExample extends Seriallizable {
   	def main(args: Array[String]) = {

   		// data source path
   		val pathToDataFolder = args(0)

   		// start up the SparkSession along with explicitly setting a given config
   		val spark = SparkSession
   			.builder()
   			.appName("Spark Example")
   			.config("spark.sql.warehouse.dir", "/user/hive/warehouse")
   			.getOrCreate()

   		// udf registration
   		spark.udf.register(
   			"myUDF", someUDF(_: String): String
   		)

   		// create DataFrame
   		val df = spark
   			.read
   			.format("json")
   			.option("path", pathToDataFolder + "data.json")

   		// DataFrame transformations an actions
   		val manipulated = df
   			.groupBy(expr("myUDF(group"))
   			.sum()
   			.collect()
   			.foreach(x => println(x))
   	}
   }

**4.Build Project**

-  (1) run ``sbt assemble``

   -  build an ``uber-jar`` or ``fat-jar`` that contains all of the
      dependencies in one JAR

   -  Simple

   -  cause complications(especially dependency conflicts) for others

-  (2) run ``sbt package``

   -  gather all of dependencies into the target folder

   -  not package all of them into one big JAR

**5.Run the application**

.. code:: shell

   # in Shell
   $ SPARK_HOME/bin/spark-submit \
   	--class com.databricks.example.DataFrameExample\
   	--master local \
   	target/scala-2.11/example_2.11-0.1-SNAPSHOT.jar "hello"

.. _header-n56:

2.1.2 Python App
~~~~~~~~~~~~~~~~

-  build Python scripts;

-  package multiple Python files into egg or ZIP files of Spark code;

-  use the ``--py-files`` argument of ``spark-submit`` to add
   ``.py, .zip, .egg`` files to be distributed with application;

**1.Build Python scripts of Spark code**

.. code:: python

   # in python
   # pyspark_template/main.py

   from __future__ import print_function

   if __name__ == "__main__":
   	from pyspark.sql import SparkSession
   	spark = SparkSession \
   		.builder \
   		.master("local") \
   		.appName("Word Count") \
   		.config("spark.some.config.option", "some-value") \
   		.getOrCreate()

   	result = spark \
   		.range(5000) \
   		.where("id > 500") \
   		.selectExpr("sum(id)") \
   		.collect()
   	print(result)

**2.Running the application**

.. code:: shell

   # in Shell
   $SPARK_HOME/bin/spark-submit --master local pyspark_template/main.py

.. _header-n69:

2.1.3 Java App
^^^^^^^^^^^^^^

**1.Build applications using mvn**

.. code:: xml

   <!-- pom.xml -->
   <!-- in XML -->
   <dependencies>
   	<dependency>
   		<groupId>org.apache.spark</groupId>
   		<artifactId>spark-core_2.11</artifactId>
   		<version>2.1.0</version>
   	</dependency>
   	<dependency>
   		<groupId>org.apahce.spark</groupId>
   		<artifactId>spark-sql_2.11</artifactId>
   		<version>2.1.0</version>
   	</dependency>
   	<dependency>
   		<groupId>org.apache.spark</groupId>
   		<artifactId>graphframes</artifactId>
   		<version>0.4.0-spark2.1-s_2.11</version>
   	</dependency>
   </dependencies>
   <repositories>
   	<!-- list of other repositores -->
   	<repository>
   		<id>SparkPackageRepo</id>
   		<url>http://dl.bintray.com/spark-packages/maven</url>
   	</repository>
   </repositories>

**2.Build the Project directories using standard Scala project
structure**

.. code:: 

   src/
   	main/
   		resources/
   			<files to include in main jar here>
   		scala/
   			<main Scala sources>
   		java/
   			<main Java sources>
   	test/
   		resources/
   			<files to include in test jar here>
   		scala/
   			<test Scala sources>
   		java/
   			<test Java sources>

**3.Put the source code in the Scala and Java directories**

.. code:: java

   // in Java
   import org.apache.spark.sql.SparkSession;
   public class SimpleExample {
   	public static void main(String[] args) {
   		SparkSession spark = SparkSession
   			.builder()
   			.getOrCreate();
   		spark.range(1, 2000).count();
   	}
   }

**4.Build Project**

-  Package the source code by using ``mvn`` package;

**5.Running the application**

.. code:: shell

   # in Shell
   $SPARK_HOME/bin/spark-submit \
   	--class com.databricks.example.SimpleExample \
   	--master local \
   	target/spark-example-0.1-SNAPSHOT.jar "Hello"

.. _header-n82:

2.2 Testing Spark App
~~~~~~~~~~~~~~~~~~~~~

-  Strategic Principles

-  Tactial Takeaways

-  Connecting to Unit Testing Frameworks

-  Connecting to Data Source

.. _header-n93:

2.3 
~~~~

.. _header-n94:

2.4 Configuring Spark App 
~~~~~~~~~~~~~~~~~~~~~~~~~~

.. _header-n96:

3 部署 Spark 应用程序
---------------------

.. _header-n98:

4 Spark 应用程序监控和Debug(Monitoring and Debugging)
-----------------------------------------------------

.. _header-n100:

5 Spark 应用程序性能调优
------------------------
