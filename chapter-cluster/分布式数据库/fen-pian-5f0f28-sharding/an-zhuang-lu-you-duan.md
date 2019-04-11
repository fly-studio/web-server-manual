# 路由端

`mongos`官方推荐是1~N台，并不需要和上面台数相同，这里只用一台来实现

## 安装

```
$ yum install mongodb-org-mongos mongodb-org-shell
```

如果你已经安装过完整的`mongodb-org`，以下指令无需执行

```
$ groupadd mongod
$ useradd -g mongod mongod -s /sbin/nologin

$ mkdir -p /var/log/mongodb/
$ chown mongod:mongod /var/log/mongodb/
```

## 配置

```
$ vim /etc/mongos.conf
```

内容

```
systemLog:
  destination: file
  logAppend: true
  path: /var/log/mongodb/mongos.log

sharding:
  configDB: mongoc/mongoc1:27018,mongoc2:27018,mongoc3:27018
# 这里必须按照这个格式<configReplSetName>/ip1:port,ip2:port,
# <configReplSetName>就是上文设置的Config集群名称

net:
  port: 27017
  bindIp: 0.0.0.0

processManagement:
  fork: true
  pidFilePath: /var/run/mongodb/mongos.pid

```

## 设置启动项

在三台上均配置

```
$ vim /usr/lib/systemd/system/mongos.service
```

```
[Unit]
Description=MongoDB Router Server
After=network.target
Documentation=https://docs.mongodb.org/manual

[Service]
User=mongod
Group=mongod
Environment="OPTIONS=-f /etc/mongos.conf"
EnvironmentFile=-/etc/sysconfig/mongod
ExecStart=/usr/bin/mongos $OPTIONS
ExecStartPre=/usr/bin/mkdir -p /var/run/mongodb
ExecStartPre=/usr/bin/chown mongod:mongod /var/run/mongodb
ExecStartPre=/usr/bin/chmod 0755 /var/run/mongodb
PermissionsStartOnly=true
PIDFile=/var/run/mongodb/mongos.pid
Type=forking
# file size
LimitFSIZE=infinity
# cpu time
LimitCPU=infinity
# virtual memory size
LimitAS=infinity
# open files
LimitNOFILE=64000
# processes/threads
LimitNPROC=64000
# locked memory
LimitMEMLOCK=infinity
# total threads (user+kernel)
TasksMax=infinity
TasksAccounting=false
# Recommended limits for for mongod as specified in
# http://docs.mongodb.org/manual/reference/ulimit/#recommended-settings

[Install]
WantedBy=multi-user.target
```

## 启动方法

```
# 设置为开机启动项
systemctl enable mongos
# 启动
systemctl start mongos
# 关闭
systemctl stop mongos
```

## 外部访问

启动后，`27017`便是对外服务的端口

> **注意：**请不要对外开放Config、Shard集群的端口，也不允许直接对这两个集群进行操作

## 添加Shard集群到路由

启动之后，还需要将Shard集群加入到路由中，如果你有多个路由，以下指令需要重复在每台路由上执行

连接到路由
```
$ mongo --port 27017
```

注意下文的格式是`<replSetName>/ip1:port,ip2:port,ip3:port`，其中`<replSetName>`就是上文Shard集群的名字
```
sh.addShard("mongod/mongod1:27019,mongod2:27019,mongod3:27019")
```

> 这里加入1个集群，只会让路由有一个分片，所以建议新建多个集群。
> 如果Shard端并没有激活集群（也是单独服务），这里需要多次添加单台`sh.addShard("ip:port")
`

返回的结果中 `ok: 1` 即成功，其它情况查看`errmsg`

## 启动分片负载均衡

如果您想为哪个数据库、集合启用分片

```
sh.enableSharding("<database>")
sh.shardCollection()
```
> sh.shardCollection() 请查看https://docs.mongodb.com/manual/reference/method/sh.shardCollection/







