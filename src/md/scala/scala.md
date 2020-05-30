
# Scala Array

## Scala 数组

* 定长数组
* 变长数组

整型定长数组：

```scala
val intValueArr = new Array[Int](3)
intValueArr(0) = 12
intValueArr(1) = 45
intValueArr(2) = 33
```

字符串数组：

```scala
val myStrArr = new Array[String](3)
myStrArr(0) = "BigData"
myStrArr(1) = "Hadoop"
myStrArr(2) = "Spark"

for (i <- 0 to 2)
	println(myStrArr(i))
```

简洁的数组声明和初始化方法：

```scala
val intValueArr = Array(12, 45, 33)
val myStrArr = Array("BigData", "Hadoop", "Spark")
```

