# MongoDB Shard Cluster

分片式 MongoDB 将自动根据相关规则将数据放入每个服务器的分片中，服务器之间有多重备份，共同承担读写压力，并且保证数据的一致性，并且随时可以**新增服务器来进行水平扩展**。

> 本篇主要内容来自于：https://docs.mongodb.com/manual/tutorial/deploy-shard-cluster/

整个架构是

![](/assets/sharded-cluster-production-architecture.bakedsvg.svg)


#


## II 安装数据Shards端

这里举例另外三台机器，IP 为 192.168.1.201 ~ 192.168.1.203

> 如果服务器有限，可以将数据端也安装到上面的服务器中，但是官方不推荐。

### 现在将这些IP加入/etc/hosts

```
192.168.1.201 mongod1
192.168.1.202 mongod2
192.168.1.203 mongod3
```

### 安装

在这三台机器上正常安装

```
$ yum install mongodb-org
```

### 建立库的文件夹

在三台上均创建

```
$ mkdir -p /www/database/mongod
$ chown mongod:mongod /www/database/mongod
```

### 修改配置
```
$ vim /etc/mongod.conf
```

需要修改的内容如下，注意保持其它内容不变

```
storage:
  dbPath: /www/database/mongod
  
net:
  port: 27019
  bindIp: 0.0.0.0
  
replication:
  replSetName: mongod # 这个自定义，但是集群中此名字要保持一致，并且不能与Config的名称相同

sharding:
  clusterRole: shardsvr
```

### 启动方法

```
# 设置为开机启动项
systemctl enable mongod
# 启动
systemctl start mongod
# 关闭
systemctl stop mongod
```

### 激活集群

启动三台之后，连接到任意一台，**注意以下命令只能在一台执行**

```
$ mongo --host mongod1 --port 27019
```

执行下面的指令

```
rs.initiate(
  {
    _id: "mongod", // 这里就是上面设置的replication.replSetName的集群唯一名字
    members: [
      { _id : 0, host : "mongod1:27019" },
      { _id : 1, host : "mongod2:27019" },
      { _id : 2, host : "mongod3:27019" }
    ]
  }
)
```

返回的结果中 `ok: 1` 即成功，其它情况查看`errmsg`

成功后可以使用 `rs.status()` 查看具体的集群情况，正常情况下会返回一长串结果，包含每个节点的主次信息：`PRIMARY` `SECONDARY`


## III 路由端

`mongos`官方推荐是1~N台，并不需要和上面台数相同，这里只用一台来实现

### 安装

```
$ yum install mongodb-org-mongos
```

如果你已经安装过完整的`mongodb-org`，以下指令无需执行
```
$ groupadd mongod
$ useradd -g mongod mongod -s /sbin/nologin

$ mkdir -p /var/log/mongodb/
$ chown mongod:mongod /var/log/mongodb/
```

### 配置

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

### 设置启动项

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

### 启动方法

```
# 设置为开机启动项
systemctl enable mongos
# 启动
systemctl start mongos
# 关闭
systemctl stop mongos
```








