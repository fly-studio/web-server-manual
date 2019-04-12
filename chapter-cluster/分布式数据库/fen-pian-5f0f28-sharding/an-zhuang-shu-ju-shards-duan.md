# 安装数据Shards端



```
net:
  port: 27019
  bindIp: 0.0.0.0
  
replication:
  replSetName: mongod # 这个自定义，但是集群中此名字要保持一致，并且不能与Config的名称相同

sharding:
  clusterRole: shardsvr
```

