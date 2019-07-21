#  RDD

    RDD(Resilient Distributed Dateset)，弹性分布式数据集。

# 特点
    
    RDD内部由partition组成
        读取HDFS时
        
    算子(函数)作用在partition上
    rdd之间有依赖关系
    分区器作用在kv格式的RDD上
    partition提供计算的最佳位置
        计算向数据移动
    
    图例        
    





Spark编程模型RDD设计以及运行原理
一、RDD介绍

RDD：弹性分布式数据集,是一个分区的只读记录的集合。
也可以这样理解，是一个提供了许多操作接口的数据集合。它还包括容错、数据集内的数据可以并行处理等。

二、RDD操作类型
RDD的操作类型分为两类，转换（transformations）和行动（action），转换是根据原有的RDD创建一个新的RDD，行动是对RDD操作后把结果返回给driver。
RDD的所有转换操作都是lazy模式，即Spark不会立刻结算结果，而只是简单的记住所有对数据集的转换操作。这些转换只有遇到action操作的时候才会开始计算。


三、RDD依赖关系
RDD提供了许多转换操作，每个转换操作都会生成新的RDD，这时候新的RDD便依赖于原有的RDD，这种RDD之间的依赖关系最终形成DAG。
RDD之间的依赖关系分为两种，为窄依赖和宽依赖。
宽依赖：RDD的每个partition都依赖于父RDD的所有Partition。
窄依赖：只依赖一个或部分的Partition。

tu

四、RDD partitioner与并行度
 每个RDD都有Partitioner属性，它决定了该RDD如何分区，当然Partition的个数还将决定每个Stage的Task个数。当前Spark需要应用设置Stage的并行Task个数（配置项为：spark.default.parallelism），在未设置的情况下，子RDD会根据父RDD的Partition决定，如map操作下子RDD的Partition与父Partition完全一致，Union操作时子RDD的Partition个数为父Partition个数之和。
    如何设置spark.default.parallelism对用户是一个挑战，它会很大程度上决定Spark程序的性能。

 
