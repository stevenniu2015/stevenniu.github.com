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
    
    
    《项目管理》k课后作业
    走在繁华的街道上，艾凡悠闲地晃着手中刚买的熟食和一罐汽水，想着虽然自己一个人来旅游不好去那家百年老店里点些名小吃来吃，不过坐在青旅特意在窗前布置的榻榻米，望着成都的夜景，吃着自己喜欢的熟食也是不错的。自己一个上路也总是自在一些，不趁着年轻多去些自己想去的地方看看怎么行。一边想着，艾凡加快了脚步。

现在天还没完全黑，还是万圣节，大家都没有回来，青旅里略微显得空荡。艾凡来到窗前，发现榻榻米的一边已经摆上了散开的袋子，里面也是熟食，旁边还放着一罐啤酒。“也有人和我一样吗？”艾凡忽然想到。窗前的榻榻米不大，艾凡坐在了另一边，一边欣赏着成都的夜景，一边吃起了买来的食物。作为喜欢一个人到处走的家伙，艾凡还是很愿意住在青旅的，人多热闹，不认识的人之间也可以很快熟络，谈笑，游戏。而这种自己安安静静的欣赏夜景的时候还是很少见的。

慢慢的天略微泛灰，夜晚街道上零星的彩灯开始预示着万圣节的狂欢。艾凡有些看的出神，忽然，在转头的间隙，他看到榻榻米的另一边坐着一位估摸着三十多岁的大叔，粗壮的胳膊，结实的大腿，略微黝黑的皮肤，平和的面相，虽然没有像电视剧里那样与一位爽朗又漂亮的女生偶遇，不过又能认识一位路上的友人艾凡还是很高兴的。而那位大叔看到艾凡看向了他，也抬起头对着他微笑。

一个人在外面旅行大多都是很能谈的来的，没一会，两个人就有一搭没一搭的聊了起来。通过聊天，艾凡知道了这位大叔来自上海，也喜欢导出走走，大江南北都快走遍了，虽然也是去过西藏的，不过艾凡觉得自己的经历跟这位大叔完全没的比。
“其实我都55啦”大叔一边喝了口啤酒，一边说着。
“啊？真的，完全看不出来啊！”我惊讶道。平整的皮肤，结实的让艾凡都羡慕的肌肉，还有一双时刻放着光的眼睛，那是充满活力的象征。艾凡顿时对这位大叔充满敬意。
大叔这个人早年在部队饭店当主厨，也干过生意，一半军人，一半厨师，后来又连续换了几家饭店都做的相当不错。干了大半辈子争取了提前退休之后就开始了旅行的生活，这待3天，那留宿5晚，在来成都之前，大叔可是刚从可可西里出来的。艾凡顿时想看到了知己一样，跟大叔越聊越起劲，平时都是他向其他人讲述自己的旅行经历，终于轮到他来请教别人了，艾凡问了大叔在无人区的经历，也料到大叔在城市随便找个小吃店旁的巷子扎帐篷。窗外灯光渐亮了起来。

不得不佩服大叔的精力旺盛，聊了不知道多久，艾凡有些累了，可大叔还在不停的说着，真的是完全不停的再说。艾凡略微有些跟不上大叔的节奏了，是不是溜号一下，不是回应大叔一声。汽水喝完了，装熟食的袋子也空掉了。大叔似乎有说不完的话，对之前工作的描述，对上司的态度，对自我的自信，似乎大叔想跟我讲完他的一生。

终于我有些熬不下去了，于是用手遮遮口，打个哈气，是不是，眼光发直。“作为第一次见面的人应该能够理解的吧。”艾凡想着。大叔毕竟阅历丰富，他眼光一闪，微笑着说道“你看，我很是善于看人的，你有些不耐烦了吧?不过处于礼貌，应该还是要回应我的提问，眼睛还看着我才好的”。大叔像是又看透了一个人而感到自豪似的嘴角上扬地看着艾凡。忽然一愣，艾凡一下子没有反应过来。这时候对方不是应该停止话题，结束交谈或者直接望着窗外静静的欣赏已经有些绚烂的狂欢节的夜了吗。不过自己旅行惯了,与人总要谦逊一些，所以他只好打起精神，继续听着大叔说的话。

大叔讲他55岁，从来没有结婚过，他说也不需要。倒是有几个侄子，不过他都保持着适当的距离，逢年过节来看看他，也吃吃饭。住在上海一座高档小区，喜欢和红酒，没事跟友人聚餐，更多的则是到处走走。他说：“不需要跟别人一起过，自己不是挺好的。不用分享，也不需要分担，自由自在的。”大叔越说越精神，说的越快。

可艾凡初始还能够听着可后来貌似听的出了神，似乎低声回应了一声：“您自己一个人高兴就好。”大叔听到，愣了一下，眼神中多了一丝不理解。
“我自己高兴就好？嗯”。大叔喃喃道，然后又接着滔滔不绝地说了起来，说他去过多少地方，遇到了多少人，经历了多少事情。
外边不时传来欢呼，街道已经完全被五颜六色的灯光所淹没，人们聚在一起欢闹着，笑着。艾凡似乎也不在乎大叔的看法了，说了句该休息了，下了榻榻米就回到自己床位去了，躺下喃喃道：“我也去过很多地方，可我也一直是一个人”。


R笔记语言
http://cran.r-project.org
统计计算和绘图
解释型语言
区分大小写 

赋值 <-  or ->
向量赋值函数 :c() eg. age <- c(1,3,4)

R绘制的图形:demo()

help.start()
help("foo") or ?foo
help.search("foo") or ??foo
example("foo")
RSiteSearch("foo")
vignette() vignette("foo")

工作空间（可保存）workspace:存储用户定义对象：向量 矩阵 函数 数据框 列表 
getwd() 当前目录
setwd("") 设定当前你也可以
load(工作空间)
\转义字符   c:/m/

输入：source("filename") 
文本输出：sink("filename") 参数append spilt=TRUE: 同时输出到屏幕和文件
图形输出：最后使用dev.off()将输出返回终端
pdf("")
win.metafile("")
png("")
jpeg("")
bmp("")
postscript("")

包
R自带包 base datasets utils grDevices graphics stats methods
library() 查看包
函数.libPaths()
install.packages()
update.packages()
installed.packages()
serach():那些包已加载可使用
包下载->library(包)载入包
help(package="package_name")

创建数据集
1.数据结构： 量 因子 矩阵 数据框 列表
2.导入数据到数据结构
行： 观测 列： 变量
R可处理数据：数值（包括日期类型） 字符 逻辑（TRUE FALSE） 复数 原生型（字节）
向量：1维 数值 字符 逻辑   相同数据类型 标量是只含一个元素的向量 c()  vector_name[3] vector_name[c(2,4)] vector_name[2:6]
矩阵：2维 数值 字符 逻辑   matrix(vector,nrow=4,ncol=4,byrow=TURE,dimnames=list(rnames,cnames)) x[2,] x[1,4] x[1,c(4,5)]
数组：array(vctor, dimensions, dimnames)  arry(1:24,c(2,3,4),dimnames=list(dim1,dim2,dim3)) dim1<-c("A1","A2")
数据框:列数据模式唯一 data.frame(patientID,age,diabetes,status[,row.names=patientID]) patientdata[1:2] patientdata[c("diabetes","status")]  row.names指定实例标识符
      变量patientdata$age table(patientdata$diabetes,patientdata$status)
      attach(patientdata) 将数据框加入R搜索路径
      plot(age,status)
      detach()            移除
      with(patientdata,{    名称相同的对象不止一个
        plot(age,status)
        temp<-sumary(age,status)   赋值仅在此括号内生效
        temps<<-sumary(age,status) 全局变量
      }) 

因子(factor)：名义型和有序型变量 决定数据分析方式和视觉呈现方式 不再是数值而是符号
    变量可归结为 名义型 有序性 连续型变量
    名义型：没有顺序之分的类别变量
    有序型：顺序，非数量关系
    连续性：顺序和数量

    factor()以一个整数向量的形式存储类别值  因子水平默认依字母顺序创建 
    status<-c("Poor","Improved","Excellent","Poor")
    status<-factor(status)               名义型变量
    status<-factor(status,ordered=TRUE)  有序型变量  将向量编码为（3,2,1,3） 内部关联值为1=Excellent.... 
    status<-factor(status,ordered=TRUE, levels=c("Poor","Improved","Excellent"))  指定顺序
列表：对象的有序集合 list()
     list(name1=object1,names=object2) 指定名称
     mylist$name1 mylist[[3]] mylist[["name1"]]

# 注释 if（FALSE）{....} 
赋值给某个数据结构中不存在的一个元素时，R自动扩展数据结构

数据输入：
键盘： edit()  
mydata <- data.frame(age=numeric(0),gender=character(0),weight=numeric(0)) 创建空数据框或矩阵
mydata <- edit(mydata)   操作对象副本，因此需要赋值  等价简介写法fix(mydata)
带分隔符的ASCII文本文件：read.table(file, header=TRUE/FALSE, sep=",",row.name="name")   file 可以是csv文件 返回数据框
                       默认情况字符变量转变成因子 stringAsFactors=FALSE或使用选项 colClasses 为每一列指定类eg logical numeric character factor
EXCEL:导入包 RODBC
install.package("RODBC") 下载安装包
library(RODBC) 载入包
channel <- odbcConnectExecl("myfile.xls") 获取RODBC连接对象
mydattafram <- sqlFetch(channel, "mysheet") 读取sheet
odbcClose()
XML：XML包
网页抓取数据：readLines()下载网页 grep()gsub() 一类函数处理 或使用RCurl XML 包

数据库管理系统：
1.ODBC接口：RODBC包 ODBC前提是数据库拥有ODBC驱动，因此需要下载
  odbcConnect(dns,uid="",pwd="")
  sqlFetch(channel,sqltable)
  sqlQuery（channel, query）
  sqlSave(channel,mydf,tablename=sqltable,append=FALSE) 将数据框写入或更新（append=TRUE）到ODBC数据库的某个表中
  sqlDrop(channel,sqtable)
  close(channel)
2.DBI相关包：基于DBI的包RJDBC RMySQL ROracleRPostgreSQL RSQLite

通过Stat/Transfer 导入数据：商业软件

.axis坐标轴刻度文字
.lab 坐标轴
.main 标题
.sub 副标题
修改图形参数：
par(optionname=value,opeionname=name,)no.readonly=TRUE 可修改当前图形参数列表
图形绘制时设定 plot(,,lty=2,pch=17) hist() boxplot()
R中颜色的参数col fg bg    colors() 返回所有颜色名称 rainbow()
文本属性cex（大小） font
title(main="",sub="",xlab="",ylab="")
axis()

基本数据管理
处理不完整数据，缺失值
数据集的合并和子集
重新编码

%% 求余 5%%2=1
%/% 整数除法 5%/%2=2

按需创建变量并将其保存到数据框
mydata$sumx <- mydata$x1 + mydata$x2 sumx是新变量
mydata <- transform(mydata, sumx = x1+x2)
变量重编码
leadership <- data.frame(manager,date,country,gender,age,q1,q2,q3,q4,q5)
1.leadership$age[leadership$age == 99] <- NA 重新编码缺失值 variable[condition] <- expression
  leadership$agecut[leadership$age > 75] <-"Elder"
2.leadership <- within(leadership,{               within() 与with() 相似，它允许修改数据框
		     agecat <-NA   将每一行设置为缺失值
		     agecat[age>75] <- "Elder"
		     agecat[age>=55 & age <=75] <-"Middle Aged"
		     agecat[age < 55] <-"Young"})
变量重命名
1.fix(leadership) 调用交互式编辑器
2.reshape 包 rename(dataframe,c(oldname="newname",oldname="newname",...))
3.names(leadership)[2] <-"testDate"  names(leadership)[2:6] <- c("","","",..)

缺失值（Not Available） 不可能出现的值（Not a Number）NaN eg.被0除的结果
is.na(对象) y<- c(1,2,3,NA) is.na(y) 返回 c(FALSE,FALSE,FALSE,TRUE)
is.na(leadership[,6:10]) 限定到6-10列
含有缺失值的算术表达式和函数计算结果也是缺失值 多数数值函数都有选项na.rm=TRUE 移除缺失值
na.omit() 行删除：移除所有含有缺失值的观测

日期值:字符串形式输入，数值形式存储 help(as.Date) help(strftime) help(ISOdatetime)日期格式
字符串->日期 as.Date(x,"input_format") x是字符型数据 默认格式yyyy-mm-dd(%Y-%m-%d)
input_format: %d(01`31)  %m(00~12) %y(07) %Y(2007) %a(Mon) %A(MOnday) %b(Jan) %B(January) 
Sys.Date() 当天日期eg.2011-12-01  date（）当天日期和时间eg.Wed Dec 01 16:28:21 2010
format(date(),format="%B %d %Y") 指定日期格式
R 使用自1970年1月1日以来的天数表示的，更早的日期为负数，可以执行算术运算
difftime(today,birth,units="weeks")
日期->字符串 as.character(dates)

类型判断 is.numeric() 数据转换 as.numeric()
is.numeric()  is.character() is.logical()  
is.vector() is.matrix() is.data.frame()is.factor()

order() 对数据框排序
newdata <-leadership[order(gender,-age)] 默认升序 age降序

合并数据集
merge(dataframeA,dataframeB,by=c("ID","Country")) 横向合并列增加 inner join  条件： 共同索引
cbind(A,B) 与merge 不同没有公共索引    条件：需要A,B有相同的行数，顺序
rbind(dataframeA,dataframeB) 纵向合并行增加 条件：数据框拥有相同的变量，顺序不要求

取子集
选取变量
1.newdata <- leadership[,c(6:10)] ,表示默认选择所有行
2.myvars <- c("q1","q2","q3","q4","q5")
  newdata <- leadership[myvars]
3.myvars <- paste("q",1:5,sep="")
 newdata <- leadership[myvars]
丢弃变量
1.myvars <- names(leadership) &in% c("q3","q4") 返回一个逻辑向量c(FALSE,FALSE,FALSE,FALSE,FALSE,FALSE,FALSE,TRUE,TRUE,FALSE)
  newdata <- leadership[!myvars]
2.newdata <- leadership[c(-8,-9)]
3.leadership$q3 <- leadership$4 <-NULL

选取观测
1.newdata <- leadership[1:3,]
2.attach(leadership)
  newdata <- leadership[which(gender=='M') & age>30] 返回c(1,4) which根据逻辑向量给出向量中值为TRUE的下标
  detach(leadership)

简单方法
subset(leadership, gender='M' & age >25,select=gender:q4)
subset(leadership, gender='M' & age >25,select=c(q1,q2,q3,q4))

随机抽样
sample()
leadership[sample(1:nrow(leadership),3,replace=FALSE)] 取样范围，长度，无放回抽样
sampling 包

使用SQL语句操作数据框 sqldf包
newdata <- sqldf("selec ManagerID,age from leadership where age>30 order by age")

高级数据管理
数学函数： x 可以使向量 矩阵 数据框
abs(x)             |x|
sqrt(x)            平方根
ceiling(x)         向上取整 ceiling(3.475) 4
floor(x)           向下取整 floor(3.475)  3
trunc(x)           向0方向取整  trunc(5.99)  5  
round(x,digits=n)  round(3.475,digits=2)  3.48
signif(x,digits=n) signif(3.475,digits=2) 3.5
log(x,base=n)
exp(x)             指数函数
cos sin tan acos asin.....

统计函数
mean(x)             平均值
median(x)           中位数
sd(x)               标准差
var(x)              方差
mad(x)              绝对中位差
quantile(x,probs)   求分位数 probs 是[0,1]之间的概率值存成的数值向量c(.8,.6,.4,.2)分位点
range(x)            求值域
sum(x)
min(x)
max(x)
diff(x,lag=n) 滞后差分
scale(x,center=TRUE,scale=TRUE) 为x进行中心化(center=TRUE) 或标准化（center=TRUE,scale=TRUE）

概率函数（略过，表示看不懂）

字符处理函数
nchar(x)     字符长度
substr(x,start,stop)   substr("abcdef",2,4) <- "22222"  x"a222ef"
grep(pattern,x,ignore.case=FALSE,fixed=FALSE) f ixed=TRUE,pattern是文本字符串，返回匹配下标 
sub(pattern,replacement,x,ignore.case=FALSE,fixed=FALSE) fixed=TRUE,pattern是文本字符串
strspilt(x,spilt,fixed=FALSE) y<-strspilt("abc","")  1个成分3个元素的列表"a" "b" "c"
paste(...,sep="")   连接字符串，分隔符sep paste("x",1:3,sep="M") c("xM1","xM2","xM3")
toupper(x)
tolower(x)

其他实用函数
length(x)
seq(from,to,by) 生成序列
rep(x,n)        repeat
cut(x,n)        将连续型变量x分割为有着n个水平的因子 选型ordered_result= TRUE 创建一个有序型因子
pretty(x,n)     创建美观的分割点 分为n个区间
cat(...,file="myfile",append=TRUE)  连接...对象（用空格分开），并假期输出到屏幕或文件（如果声明了一个的话） 

apply() 将函数应用到矩阵，数组，数据框的任何维度上
apply(x, MARGIN,FUN,...) MARGIN 维度下标 1：行 2：列

直方图：hist(x) 连续型变量 breaks freq
点图：dotchart(x,labels=)

基本统计分析
描述性统计分析：
sumary()  min max 1st Qu median 3rd Qu mean

sapply(x,FUN,options)

分组计算描述性统计量：
aggregate(data,by=list(am=mtcars$am),mean)
by(data,INDICES,FUN) INDICES 因子或因子组成的列表   自定义函数
reshape melt(data,id=) cast()

频数表和列联表
vcd gmodels 包
创建表 
table(A) xtabs() prop.table(mytable) 将频数转化为比例值
table(A,B) A行B列 xtabs(~A+B,data=mydata)
margin.table(mytable,1/2)生成边际频数 1/2按行或列求和
addmargins()添加边际和
gmodels CrossTable()
ftable()输出多为列联表

处理字符串的函数：strsplit() gsub() 
nchar('')向量元素字符个数 0 length('')向量元素个数 1
paste(x, y, sep="-", collapse='; ') 
R中使用\\作为转义字符
gsub() 替换所有 sub()替换一次
