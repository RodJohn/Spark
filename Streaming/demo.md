三、构建Spark Streaming

	作为构建于Spark之上的应用框架，Spark Streaming承袭了Spark的编程风格，对于已经了解Spark的用户来说能够快速地上手。
	接下来以Spark Streaming官方提供的WordCount代码为例来介绍Spark Streaming的使用方式。

	val conf = new SparkConf().setMaster("local[2]").setAppName("NetworkWordCount")
	val ssc = new StreamingContext(conf, Seconds(1))
	// Create a DStream that will connect to hostname:port, like localhost:9999
	val lines = ssc.socketTextStream("localhost", 9999)
	// Split each line into words
	val words = lines.flatMap(_.split(" "))
	import org.apache.spark.streaming.StreamingContext._
	// Count each word in each batch
	val pairs = words.map(word => (word, 1))
	val wordCounts = pairs.reduceByKey(_ + _)
	// Print the first ten elements of each RDD generated in this DStream to the console
	wordCounts.print()
	ssc.start()              // Start the computation
	ssc.awaitTermination()  // Wait for the computation 



	1.创建StreamingContext对象
		同Spark初始化需要创建SparkContext对象一样，使用Spark Streaming就需要创建StreamingContext对象。
		创建StreamingContext对象所需的参数与SparkContext基本一致，包括指明Master，设定名称(如NetworkWordCount)。需要注意的是参数Seconds(1)，Spark Streaming需要指定处理数据的时间间隔，如上例所示的1s，那么Spark Streaming会以1s为时间窗口进行数据处理。此参数需要根据用户的需求和集群的处理能力进行适当的设置；
	2.创建InputDStream如同Storm的Spout，Spark Streaming需要指明数据源。
		如上例所示的socketTextStream，Spark Streaming以socket连接作为数据源读取数据。
		当然Spark Streaming支持多种不同的数据源，包括Kafka、 Flume、HDFS/S3、Kinesis和Twitter等数据源；
		同样包括自己重写Receiver方法来实现自己的消息处理例如metaq。
	3.操作DStream对于从数据源得到的DStream，
		用户可以在其基础上进行各种操作，如上例所示的操作就是一个典型的WordCount执行流程：
		对于当前时间窗口内从数据源得到的数据首先进行分割，然后利用Map和ReduceByKey方法进行计算，当然最后还有使用print()方法输出结果；
	4.启动Spark Streaming之前所作的所有步骤只是创建了执行流程，
		程序没有真正连接上数据源，也没有对数据进行任何操作，只是设定好了所有的执行计划，
		当ssc.start()启动后程序才真正进行所有预期的操作。


