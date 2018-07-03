## ## 初始化数据库
```
$ mysqld --initialize  --user=mysql
```

## ## 启动第一个节点

如果启动第一个集群数据库，要保持`wsrep_cluster_address`的配置为空，等启动完毕之后，再填写了`restart`

```
/usr/sbin/mysqld --wsrep-new-cluster --user=mysql &
```