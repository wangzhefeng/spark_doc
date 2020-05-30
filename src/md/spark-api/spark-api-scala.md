
# spark(scala) API

* org.apache.spark.SparkConf
* org.apache.spark.SparkContext
	- Spark的主要入口, Spark集群的连接
	- setLoglevel()
	- textFile()
	- wholeTextFiles()
	- sequenceFile()
	- parallelize()
	- stop()
* org.apache.spark.ExecutorPlugin
* org.apache.spark.FutureAction
* org.apache.spark.HashPartitioner
* org.apache.spark.JobExecutionStatus
* org.apache.spark.Partition
* org.apache.spark.Partitioner
* org.apache.spark.RangePartitioner
* org.apahce.spark.SparkException
* org.apache.spark.SparkExecutorInfo
* org.apache.spark.SparkFirehoseListener
* org.apache.spark.SparkJobInfo
* org.apache.spark.SparkStageInfo
* org.apache.spark.SparkStatusTracker
* org.apache.spark.TaskContext

## package




* org.apache.spark.rdd
	- org.apache.spark.rdd.RDD
		- 分布式数据集合
	- org.apache.spark.rdd.PairRDDFunctions
		- key-value RDD操作
		- `groupByKey`
		- `join`
	- org.apache.spark.rdd.DoubleRDDFunctions
		- Doubles RDD操作
	- org.apache.spark.rdd.SequenceFileRDDFunctions
		- 能够保存为SequenceFiles格式的RDD操作
* org.apache.spark.sql
	- org.apache.spark.sql.SparkSession
		- 使用Dataset和DataFrame API进行Spark编程的主要入口
* org.apache.spark.streaming
* org.apache.spark.ml
* org.apache.spark.mllib
* org.apache.spark.graphx