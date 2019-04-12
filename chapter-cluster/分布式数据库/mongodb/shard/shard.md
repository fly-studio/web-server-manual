# MongoDB Shard Cluster

`MongoDB` 分片集群将自动根据相关规则将数据分别放入其中分片节点中，
也就是说，它和副本集群之前的区别是，副本集群内部节点数据保证完整一致，但是分片集群存储的是差异化数据。
比如根据_id的hash值，将然后写入其中一个节点，故而分片节点之间数据完全不同，共同承担读写压力，并且保证数据的一致性，并且随时可以**新增节点来进行水平扩展**。

> 本篇主要内容来自于：https://docs.mongodb.com/manual/tutorial/deploy-shard-cluster/

## 生产环境架构

![](/assets/sharded-cluster-production-architecture.bakedsvg.svg)

特征：

- 路由可以有1个或多个组成，如果是多个，需要相同的配置，以及注册相同的Shard节点

- Config Server存储的是Meta数据，也就是分片规则的元数据，如果这个ConfigServer数据丢失，将是灾难性的。
    
    在4.x版本中，Config Server必须由副本集群提供服务
    
- Shard可以由多个副本集群组成，注意1个副本集群仅仅代表一个Shard节点（因为副本集群中每个节点的数据是完全相同的），如果要设置多个Shard，就需要多个副本集群。

    如果服务器毕竟紧张，可以由多个的MongoDB节点来构成多个Shard，也就是这些节点不组成副本集群
    

## 开发环境架构

服务器有限，也可以使用这个配置

![](/assets/sharded-cluster-test-architecture.bakedsvg.svg)

- 路由器1个
- Config Server副本集群
- 单数据节点构成的Shard节点


安装步骤见下级文档






