# 设置分片

`mongo`连接上路由

## 启动分片负载均衡

如果您想为哪个数据库、集合启用分片

```
sh.enableSharding("<database>")
sh.shardCollection(namespace, key, unique, options)
```
> sh.shardCollection() 用法请查看https://docs.mongodb.com/manual/reference/method/sh.shardCollection/







