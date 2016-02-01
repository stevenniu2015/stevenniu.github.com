# stevenniu.github.com
this is my personal homepage

 《“Spark大数据处理”阅读笔记》
 
 1.Spark简介
 1.1 Spark是什么.
     Spark是基于内存计算的大数据并行计算框架。Spark基于内存计算，提高了在大数据环境下数据处理的实时性，同时保证了高容错性和可伸缩性，允许用户将Spark部署在大量廉价硬件上，形成集群。
    Spark于2009年诞生于加州大学伯克利分校AMPLab.目前，已经成为Apache（Apache是世界使用排名第一的Web服务器软件。它可以运行在几乎所有广泛使用的计算机平台上，由于其跨平台和安全性被广泛使用，是最流行的Web服务器端软件之一）软件基金会旗下的顶级开源项目。

（1）Spark之于Hadoop
    准确的是，Spark是一个计算框架，Hadoop中包含计算框架MapReduce和分布式文件系统HDFS,Hadoop更广泛地说还包含在其生态系统上的其他系统，如Hbase、Hive（Hive和Hbase是两种基于Hadoop的不同技术--Hive是一种类SQL的引擎，并且运行MapReduce任务，Hbase是一种在Hadoop之上的NoSQL 的Key/vale数据库。Hive可以用来进行统计查询，HBase可以用来进行实时查询，数据也可以从Hive写到Hbase，设置再从Hbase写回Hive）等。
    
    Spark优于Hadoop MapReduce之处：
    i. 中间结果输出： 中间结果输出形成的多个Stage可不用输出到HDFS中而是直接串联或者并行执行。
    ii. 数据格式和内存布局： 相比于MapReduce Schema on Read处理方式产生的较大的处理开销，Spark抽象出分布式内存存储结构弹性分布式数据集RDD之处粗粒度写操作（http://bbs.tianya.cn/post-414-57390-1.shtml）和精确到每一条记录的读操作。能够用来分布式检索，Spark的特性是能够控制数据在不同节点上的分区，用户可以自定义分区策略。
    iii. 执行策略： Spark能够减轻MapReduce在数据Shuffle之前花费大量时间的排序工作。调度中使用更为通用的任务执行计划图DAG，每轮次输出结果在内存中缓存。
    iiii. 任务调度的开销： Spark采用事件驱动类库AKKA来启动任务，通过线程池复用线程来避免进程或者线程启动和切换开销。（MapReduce系统是为了运行长达数小时的批量作业而设计的在某些极情况下，提交一个任务的延迟非常高）
