# 启用分片

`mongo`连接上路由

> 本文主要用法请查看 https://docs.mongodb.com/manual/core/sharding-shard-key/

## 设置分片文件大小

系统会默认设置分片大小为64M，如果不满足需求，可以自己设置。

```
use config
db.settings.save( { _id:"chunksize", value: <sizeInMB> } )
```

## 启动分片负载均衡

### 启用数据库的分片

```
sh.enableSharding("<database>")
```

### 启用集合的分片

> https://docs.mongodb.com/manual/reference/method/sh.shardCollection/

```
sh.shardCollection("<database>.<collection>", key, unique, options)
```








