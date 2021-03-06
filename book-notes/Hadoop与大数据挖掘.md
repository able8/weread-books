## Hadoop与大数据挖掘
> 张良均等

### 前言

最早提出“大数据”时代到来的是全球知名咨询公司麦肯锡，麦肯锡称：“数据，已经渗透到当今每一个行业和业务职能领域，成为重要的生产因素。人们对于海量数据的挖掘和运用，预示着新一波生产率增长和消费者盈余浪潮的到来。”

Hadoop无疑应用很广泛。Hadoop具有以下优势：高可靠性、高扩展性、高效性、高容错性、低成本、生态系统完善。一般来说，使用Hadoop相关技术可以解决企业相关大数据应用，特别是结合诸如Mahout、Spark MLlib等技术，不仅可以对企业相关大数据进行基础分析，还能构建挖掘模型，挖掘企业大数据中有价值的信息。

### 第1章 浅谈大数据

对这些“大数据”进行分析建模，如果可以预测股票的涨跌，那么这就是一个实实在在的大数据案例了

（1）理解客户，满足客户服务需求大数据的应用目前在这个领域是最广为人知的。重点是如何应用大数据更好地了解客户以及他们的爱好和行为。企业非常喜欢搜集社交方面的数据、浏览器的日志、分析文本和传感器的数据，从而更加全面地了解客户。

大数据平台分为两个方面，硬件平台和软件平台。硬件平台一般如OpenStack、Amazon云平台、阿里云计算等，类似这样的平台其实做的是虚拟化，即把多台机器或一台机器虚拟化成一个资源池，然后给成千上万人用，各自租用相应的资源服务等。而软件平台则是大家经常听到的，如Hadoop、MapReduce、Spark等，也可以狭义理解为Hadoop生态圈，即把多个节点资源（可以是虚拟节点资源）进行整合，作为一个集群对外提供存储和运算分析服务。

CDH是最成型的发行版本，拥有最多的部署案例，而且提供强大的部署、管理和监控工具

### 第2章 大数据存储与运算利器——Hadoop

HDFS（Hadoop Distributed File System）是可扩展、容错、高性能的分布式文件系统，异步复制，一次写入多次读取，主要负责存储。MapReduce为分布式计算框架，主要包含map（映射）和reduce（归约）过程，负责在HDFS上进行计算。

专门商业化Hadoop的公司Cloudera。同年，Facebook团队发现他们很多人不会写Hadoop的程序，而对SQL的一套东西很熟，所以他们就在Hadoop上构建了一个叫作Hive的软件，专门用于把SQL转换为Hadoop的MapReduce程序。

Hadoop是一个能够让用户轻松架构和使用的分布式计算的平台。用户可以轻松地在Hadoop上开发和运行处理海量数据的应用程序。

Hadoop框架最核心的设计就是HDFS和MapReduce。HDFS为海量的数据提供了存储，则MapReduce为海量的数据提供了计算。

K-Means算法是硬聚类算法，是典型的基于原型的目标函数聚类方法的代表。它是将数据点到原型的某种距离作为优化的目标函数

### 第3章 大数据查询——Hive

Hive是基于Hadoop的一个数据仓库工具，可以将结构化的数据文件映射为一张数据库表，并提供简单的类SQL查询功能，主要用于对大规模数据的提取转化加载（ETL）。其优点是学习成本低，可以通过类SQL语句快速实现简单的MapReduce统计，不必开发专门的MapReduce应用，十分适合数据仓库的统计分析。

同时，不断增长的数据所带来的价值也不言而喻，但要让蕴藏在海量数据中的价值高效地体现出来，必然涉及海量数据的计算，而传统的数据处理方式面对海量数据的挖掘计算可谓“心有余而力不足”。

### 第4章 大数据快速读写——HBase

HBase是Hadoop平台下的数据存储引擎，是一个非关系型数据库，即一个NoSQL数据库。它能够为大数据提供实时的读/写操作。由于HBase具备开源、分布式、可扩展性以及面向列的存储特点，使得HBase可以部署在廉价的PC服务器集群上，处理大规模的海量数据。

HBase最早由Google的Bigtable演变而来，其存储的数据从逻辑上就像一张很大的表，并且它的数据列可以根据需要动态增加。HBase的存储的是松散型数据，也就是半结构化数据，所以存储维度是动态可变的，也就是说HBase表中的每一行可以包含不同数量的列，并且某一行的某一列还可以有多个版本的数据，这主要通过时间戳范围进行区分。

ZooKeeper分布式服务框架是Apache Hadoop的一个子项目，它主要是用来解决分布式应用中经常遇到的一些数据管理问题，如：统一命名服务、状态同步服务、集群管理、分布式应用配置项的管理等。ZooKeeper各个服务节点组成一个集群（2n+1个节点允许n个失效），在ZooKeeper集群中有两个角色，一个是leader，主要负责写服务和数据同步；另一个是follower，提供读服务，leader失效后会在follower中重新选举新的leader。

### 第6章 大数据快速运算与挖掘——Spark

Spark在其官网的介绍中，重点突出“快”的特点，若从事大数据或集群等相关工作，需要快速计算，按Spark的说法，则无需再学习其他架构，Spark就能很好满足需求。Spark如何体现“快”呢？Spark的数据全部在内存中，因此数据都在内存中计算，而不会涉及类似于磁盘等低传输速率的硬件，以此保证数据处理快速而有效。但这也意味着你需要很好的硬件配置。