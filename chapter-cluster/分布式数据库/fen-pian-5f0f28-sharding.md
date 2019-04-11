# MongoDB Shard Cluster

分片式 MongoDB 将自动根据相关规则将数据放入每个服务器的分片中，服务器之间有多重备份，共同承担读写压力，并且保证数据的一致性，并且随时可以**新增服务器来进行水平扩展**。

> 本篇主要内容来自于：https://docs.mongodb.com/manual/tutorial/deploy-shard-cluster/

整个架构是

![](/assets/sharded-cluster-production-architecture.bakedsvg.svg)

- 其中Config Server存储的是Meta数据，也就是分片的一些元数据，如果这个集群数据丢失，则是灾难性的。
- 1个Shard可以有多个台构成集群，也可以是单台服务器


安装步骤见下级文档






