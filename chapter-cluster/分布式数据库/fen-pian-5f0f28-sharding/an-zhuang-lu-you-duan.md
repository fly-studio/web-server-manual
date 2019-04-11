# 路由端

`mongos`官方推荐是1~N台，并不需要和上面台数相同，这里只用一台来实现

## 安装

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








