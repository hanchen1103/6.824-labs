## lab1

第一节课需要做的lab是实现一个简易的MapReduce。

要求在：https://pdos.csail.mit.edu/6.824/labs/lab-mr.html。

首先准备的工作大概是：阅读了**MapReduce: Simplified Data Processing on Large Clusters**

这篇论文，原文在：https://pdos.csail.mit.edu/6.824/papers/mapreduce.pdf。

总结了一些重点。列举在这里之后做lab的时候方便来看。

>MapReduce is a programming model and an associated implementation for processing and generating large data sets. 

MapReduce 是一种处理（计算）大数据的编程模型实现。google在03年的时候创造了它，后续也在改进优化。

应用场景有很多：比如1TB数据排序，统计大文本单词频率，计算URL访问频率，倒排索引等。

回到MapReduce本身，大概分为两部分，很直白：map和reduce。

作为用户，首先编写一个用来处理键值对(Key/Value)的**map**函数来生成一个中间键值对集合。比方说对一个文件进行单词统计，那么key为文件名，value为文件内容，这个map函数因为遍历每一个单词，并记录出现次数。

然后，再指定一个**reduce**函数， 合并所有具有相同中间key的中间value。在map函数之后，会产生出很多个中间结果（因为分割了大文件会有很多小文件），那么reduce将聚合这些结果，得到输出结果。

它是如何做的，简单来说就是切割后处理再聚合。难点在于容灾处理，性能优化，数据正确性（基本上分布式领域所需要考虑的）。

如何去执行？有几个重要概念，Master，worker, 分区。

流程大概为：

1. MapReduce会将输入文件切为M个块，比如10TB下M通常为200000，每一个块大小大概在16M~64M。然后会启动多个程序副本。

2. 程序副本有两种，Master（唯一）和worker，Master的职责是分配任务给worker做，每个worker会被分配到一个Map任务或者Reduce任务，Map任务有M个，worker为R个，R为分区数量，大概为5000，为worker倍数。

3. 做map任务的worker会读取（这里是一个优化点）,首先解析文件然后使用用户编写的map函数做处理，得到的中间结果首先被缓存到内存里，接着定期刷到磁盘内。

   





