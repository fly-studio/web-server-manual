# 安装Shard端

## 副本集构成的Shard

如果有充足的服务器，并且架构是1个副本集群构成1个Shard，这样可以直接参见[副本集的节点安装](/chapter-cluster/分布式数据库/fu-ben-shi/an-zhuang-jie-dian.md)

> 这里再次重申，1个副本集只构成一个Shard，副本集中每个节点的数据是完全一致的


配置参考下文

```
net:
  port: 27019
  bindIp: 0.0.0.0
  
replication:
  replSetName: mongod # 这个自定义，但是集群中此名字要保持一致，并且不能与Config的名称相同

sharding:
  clusterRole: shardsvr
```

## 单节点构成的Shard

如果服务器有限，那就将1的MongoDB节点设置成为1个Shard，安装也可以参考[副本集的节点安装](/chapter-cluster/分布式数据库/fu-ben-shi/an-zhuang-jie-dian.md)

配置参考下文

```
net:
  port: 27019
  bindIp: 0.0.0.0

sharding:
  clusterRole: shardsvr
```

注意：
- 去掉配置中的`replication`
- 无需执行`rs.initiate(...)`
