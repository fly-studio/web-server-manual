# 路由端

路由是对外提供服务的唯一窗口，客户端只需要接入到路由便可以进行CRUD的操作

`mongos`官方推荐路由是`1~N`个节点，这里先用一个节点来实现，配置结束并加入节点后，此时再加入其它路由，只需要设置相同的`mongos.conf`配置，然后启动即可，无需再次执行`sh.addShard`

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

全部内容

```
systemLog:
  destination: file
  logAppend: true
  path: /var/log/mongodb/mongos.log

sharding:
  configDB: mongoc/mongoc1:27018,mongoc2:27018,mongoc3:27018
# 这里必须按照这个格式<configReplSetName>/ip1:port,ip2:port,
# <configReplSetName>就是上文设置的Config集群名称
# 4.x中Config Server必须由副本集群提供

net:
  port: 27017
  bindIp: 0.0.0.0

processManagement:
  fork: true
  pidFilePath: /var/run/mongodb/mongos.pid

```

## 设置启动项

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

> **注意：**请不要对外开放Config、Shard端的端口，也不允许直接对这两个端进行操作

## 添加Shard节点到路由

启动之后，还需要将Shard节点加入到路由中

> 如果你有多个路由，以下指令**仅仅只需要在一个路由上执行**，因为具体的配置已经写入到Config Server

连接到路由

```
$ mongo --port 27017
```

### 添加由副本集群构成的Shard节点

服务器充足，Shard节点由副本集群构成，按照下面操作

注意下文的格式是`<replSetName>/ip1:port,ip2:port,ip3:port`，其中`<replSetName>`就是上文Shard集群的名字

```
sh.addShard("mongod/mongod1:27019,mongod2:27019,mongod3:27019")
```

返回的结果中 `ok: 1` 即成功，其它情况查看`errmsg`

> **注意：**这里只演示加入1个副本集群，这只会让集群拥有一个Shard节点，这对项目并没有帮助，因为只有1个Shard节点的情况下无法分片

需要再次加入多个副本集群(或单独的数据节点)，比如：

```
sh.addShard("其它集群名/mongod3:27019,mongod4:27019,mongod5:27019")
```

### 添加由单个数据节点构成的Shard节点

服务器有限，Shard节点由单独的数据节点构成，按照下面的操作

多次添加即可
```
sh.addShard("ip:port")
```

以上添加可以混合进行，但是请勿将副本集群中的节点服务器单独添加






