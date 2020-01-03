## ## 启动第一个节点

如果启动第一个集群数据库

```
/usr/sbin/mysqld --wsrep-new-cluster --wsrep-cluster-address='gcomm://' --user=mysql
```

其它集群数据库用`systemctl`、`service` 正常启动即可

启动之后，可以关闭第一个数据库，使用`systemctl`、`service` 正常启动


## ## 查看集群状态

```
SHOW STATUS LIKE 'wsrep_%';
```

`wsrep_incoming_addresses` 是否有所有的IP，如果只有自己的IP，则表示加入集群失败