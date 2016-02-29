# stevenniu.github.com
this is my personal homepage

 《“Spark大数据处理”阅读笔记》
 
 1.Spark简介
 
 
 1.1 Spark是什么.----------------------------------------------------------------------------------------
     Spark是基于内存计算的大数据并行计算框架。Spark基于内存计算，提高了在大数据环境下数据处理的实时性，同时保证了高容错性和可伸缩性，允许用户将Spark部署在大量廉价硬件上，形成集群。
    Spark于2009年诞生于加州大学伯克利分校AMPLab.目前，已经成为Apache（Apache是世界使用排名第一的Web服务器软件。它可以运行在几乎所有广泛使用的计算机平台上，由于其跨平台和安全性被广泛使用，是最流行的Web服务器端软件之一）软件基金会旗下的顶级开源项目。

（1）Spark之于Hadoop
    准确的是，Spark是一个计算框架，Hadoop中包含计算框架MapReduce和分布式文件系统HDFS,Hadoop更广泛地说还包含在其生态系统上的其他系统，如Hbase、Hive（Hive和Hbase是两种基于Hadoop的不同技术--Hive是一种类SQL的引擎，并且运行MapReduce任务，Hbase是一种在Hadoop之上的NoSQL 的Key/vale数据库。Hive可以用来进行统计查询，HBase可以用来进行实时查询，数据也可以从Hive写到Hbase，设置再从Hbase写回Hive）等。
    
    Spark优于Hadoop MapReduce之处：
    i. 中间结果输出： 中间结果输出形成的多个Stage可不用输出到HDFS中而是直接串联或者并行执行。
    ii. 数据格式和内存布局： 相比于MapReduce Schema on Read处理方式产生的较大的处理开销，Spark抽象出分布式内存存储结构弹性分布式数据集RDD之处粗粒度写操作（http://bbs.tianya.cn/post-414-57390-1.shtml）和精确到每一条记录的读操作。能够用来分布式检索，Spark的特性是能够控制数据在不同节点上的分区，用户可以自定义分区策略。
    iii. 执行策略： Spark能够减轻MapReduce在数据Shuffle之前花费大量时间的排序工作。调度中使用更为通用的任务执行计划图DAG，每轮次输出结果在内存中缓存。
    iiii. 任务调度的开销： Spark采用事件驱动类库AKKA来启动任务，通过线程池复用线程来避免进程或者线程启动和切换开销。（MapReduce系统是为了运行长达数小时的批量作业而设计的在某些极情况下，提交一个任务的延迟非常高）
    
 （2）Spark带来的好处
 i. 打造全栈多计算范式的高效数据流水线：
     支持复杂查询。除了 简单的“Map”"Reduce"操作外，Spark还支持SQL查询、流式计算、机器学习和图计法。同事用户可以在同一个工作流中无缝搭配这些计算范式。
 ii. 轻量级快速处理：
     核心代码只有4万行。Spark通过将中间结果缓存在内存中减少磁盘I/O来达到性能的提升。
 iii. 易于使用，支持多种语言：
     支持Scala、Java、Python.自带80多个算子。
 iiii. 于HDFS等存储层兼容：
 Spark可以独立运行还可以读取Hadoop数据。
 iiiii. 社区活跃度高：
 2009年起源，截止2015年，50机构，260工程师贡献代码。
 
 其实Spark并不完美，仅仅适用于粗粒度的全局数据并行计算。在各个领域还有更优于它的大数据系统。
 
 1.2 Spark生态系统BDAS------------------------------------------------------------------------------
 伯克利将Spark的整个生态系统称为伯克利数据分析栈（BDAS），还了众多的子项目。
 
 （1）Spark
         BDAS的核心组件，是一个大数据分布式编程框架，有不仅是“Map”"Reduce"还有filter、join\groupByKey等算子。
         
 （2）Shark
         SHark是构建在Spark和Hive基础之上的数据仓库。目前已经完成学术使命，中止开发。
         
 （3） Spark SQL
         Spark SQL提供在大数据上的SQL查询功能。Spqrk SQL使用Catalyst做查询编译和优化器。可以避免之前Shark一样依赖并需要维护一套Hive分支。
         
 （4） Spark Streaming
         Spark Streaming通过将流数据按照指定时间片累计为RDD，然后将每个RDD进行批量处理，进而实现大规模的流数据处理。
         
 （5） GraphX
         GraphX基于BSP模型，在Spark之上封装类似Pregel(一种大规模图计算模型)。在用户多轮次迭代时基于内存的Spark内存计算有显著优势。
         
 （6） Tachyon
         一种分布式内存文件系统，可以理解为内存中的HDFS。
         
 （7） Mesos
         一种资源管理框架，提供类似于YARN的功能。用户可以在其中插件式地运行Spark\MapReduce\Tez等计算框架的任务。Mesos会对资源和任务进行隔离，并且实现高效的资源任务调度。
         
 （8） BlinkDB
         一种用于在海量数据上进行交互式SQL的近似查询引擎。它允许用户通过在查询准确度和查询响应时间之间做出权衡，完成近似查询。其数据的精度被控制在允许的误差范围内（什么叫做允许的误差范围内？）为了达到这个目标，BlinkDB的核心思想是：通过一个自适应优化框架，随着时间的推移，从原始数据建立并维护一组多维样本；通过一个动态样本选择策略，选择一个适当大小的示例，然后基于查询的精确度和响应时间满足用户查询的需求。
         
 1.3 Spark 架构-----------------------------------------------------------------------------------
 （1） Spark的代码结构
 P7 图1-3
 Scheduler模块代码量最多，6200行。deploy其次，5600行。外部的mllib7400,SQL12000
 
 (2) Spark的架构
 Spark的架构采用了分布式计算中的Master-Slave模式。Client：用户的客户端提交应用-》Driver:负责控制整个应用的执行-》ClusterManager负责任务的分配-》Worker:接收并负责状态汇报-》Executor:负责任务执行-》Task.
 
 主节点启动Master进程，从节点启动Worker进程。
 
 spark的整体流程：     
 Client提交应用-》Master找到一个Worker启动Driver-》Driver向Master或者资源管理器申请资源,之后将应用转化为RDD Graph->由DAGScheduler将RDD Graph转化为Stage的有向无环图提交给TaskScheduler-》TaskScheduler提交任务给Executor执行。执行过程中其他组件协同工作。
 
 （3） Spark运行逻辑     
(HDFS is a Java-based file system that provides scalable and reliable data storage, and it was designed to span large clusters of commodity servers.)
根据RDD之间的依赖关系切分成不同的阶段（Stage）（类似各个不同的可并行进行的分工？），每个Stage中包含了一个或者几个RDD之间的操作，最后各个Stage之间join之后saveAsSequenceFile
输出并保存到HDFS中。
 P9 图1-5 
 
 
 1.4 Spark分布式架构与单机多核架构的异同-------------------------------------------------------------
 我们通常所说的分布式系统主要指的是分布式软件系统，它是在通信网络互连的多处理机（？）的架构上执行任务的软件系统，包括分布式操作系统、分布式程序设计语言、分布式文件系统和分布式数据库系统等。Spark是分布式软件系统中的分布式计算框架，基于Spark可以编写分布式计算程序和软件。
 
 分布式架构和单机架构的主要区别在于内存和磁盘的共享问题，这也是我们在书写和优化程序的过程中需要注意的地方。具体对比P10图1-6.
 
 单机多核环境下，多CPU共享内存和磁盘。当系统需要的计算和存储资源不够时，需要扩展CPU和存储时，单机多核系统显得力不从心。
 对比而言，大规模分布式并行处理系统是由许多松耦合的处理单元组成的（是处理单元而非处理器）每个单元的CPU都有自己的私有资源如总线，内存，硬盘等。这种结构的最大特点就是不共享资源（share Nothing）。这种架构下节点可以实现无线扩展，即计算能力和存储的扩展性可以成倍的增长--------------》分布式运算下，数据尽可能的本地运算，减少了网络I/O的开销。当操作相互之间没有什么关系，处理单元之间需要进行的通信比较少的时候，采用分布式系统比较好。
 
 1.5 Spark的企业级应用----------------------------------------------------------------
 亚马逊---Yahoo!---西班牙电信---淘宝
 
 3. Spark计算模型
 

3.1 Spark程序模型----------------------------------------------------------------------

经典程序初步了解Spark的计算模型
1）SparkContext中的textFile函数从HDFS读取日志文件，输出变量file。
val file=sc.textFile("hdfs://xxx")
2)RDD中的filter函数过滤带“ERROR”的行，输出errors(errors也是一个RDD）。
val errors=file.filter(line=>line.contains("ERROR"))
3)RDD的count函数返回“ERROR”的行数：errors.count()

RDD操作起来与Scala集合类型没有太大差别，这就是Spark追求的目标：像编写单机程序一样编写分布式程序，但它们的数据和运行模式有很大的不同，用户需要具备更强的系统把控能力和分布式系统知识。
 
 3.2 弹性分布式数据集--------------------------------------------------------------------
 
 3.2.1 RDD简介
 弹性分布式数据集（resilient distributed dataset, RDD），它是逻辑集中的实体，在集群中的多台机器之间的数据重排（data shuffling）。Spark提供了“partitionBy”运算符，能够通过集群中多台机器之间对原始RDD进行数据再分配来形成一个新的RDD。RDD是Spark的核心数据结构，通过RDD的依赖关系形成Spark的调度顺序。通过对RDD的操作形成整个Spark程序。
 
 （1）RDD的4中创建方式
 1）从hadoop文件系统（或者与Hadoop兼容的其他持久化存储系统，如Hive,Cassandra,Hbase）输出（如HDFS）创建。
 2）从父RDD转换得到新的RDD。
 3）调用SparkContext方法的parallelize，将Driver上的数据集并行化，转化为分布式的RDD。
 4）更改RDD的持久性（persistence）,例如cache()函数。默认RDD计算后会在内存清除。通过cache函数将计算后的RDD缓存在内存中（这什么方法，听不懂）。
 
 （2）RDD的两种操作算子
 transformation(转变)与Action（行动）。
 1.transformation: transformation操作是延迟计算的，也就是说从一个RDD转换生成另外一个RDD的转换操作不是马上执行的，需要等到有Action操作时候，才会真正出发运算。
 2.Action： Action算子会出发Spark提交作业（Job），并将数据输出到Spark系统。
 
（3） RDD的重要内部属性
1分区列表
2 计算每个分片的函数
3对父RDD的依赖列表
4对Key-Value对数据类型RDD的分区器，控制分区策略和分区数
5每个数据分区的地址列表（如HDFS上的数据块的地址）

3.2.2 RDD与分布式共享内存的异同
RDD是一种分布式的内存抽象，DSM是一种通用的内存数据抽象，在DSM（distributed Shared Memory）系统中，应用可以向全局地址空间的任意位置进行读写操作。但是这种通用性同事也使其在商用集群上实现有效的容错性和一致性更加困难。
    RDD与DSM主要区别在于，RDD不仅可以通过批量转换创建（即“写”）RDD，还可以对任意内存位置读写。RDD限制应用执行批量写操作，这样有利于实现有效的容错。特别是，由于RDD可以使用lineage来恢复分区，基本没有检查点开销。失效时只需要重新计算丢失的那些RDD分区，就可以在不同节点上并行执行，而不需要回滚整个程序。
    
    通过备份任务的复制，RDD可以处理落后任务（即运行很慢的节点），这点与MapReduce类似，DSM则难以实现备份任务，因为任务及其副本均需读写同一内存位置的数据。
    
    RDD对比与DSM的两个优势：
    第一，对于RDD中的批量操作，运行时将根据数据存放的位置来调度任务，从而提高性能。
    第二，对于扫描类型操作，如果内存不足已缓存整个RDD，就进行部分缓存，将内存容纳不下的分区存储到磁盘上。
    
    3.2.3 Spark的数据存储
    Spark数据存储的核心是弹性分布式数据集（RDD）。 RDD可以被抽象地理解为一个大的数组（Array）,但是整个数组是分布在集群上的。逻辑上RDD的每个分区叫做一个Partition。
    
    3.3 Spark算子分类及功能
    
    作用： 输入，转换运行，输出
    
    算子分类： Value, Key-Value， Action
