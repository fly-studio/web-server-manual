# 安装 Config 端

比如现在有三台节点，IP 为 192.168.1.101 ~ 192.168.1.103

## 将这些IP加入/etc/hosts

```
192.168.1.101 mongoc1
192.168.1.102 mongoc2
192.168.1.103 mongoc3
```

## 安装

在这三台节点上正常安装

```
$ yum install mongodb-org
```

## 建立库的文件夹

在三节点上均创建

```
$ mkdir -p /www/database/mongoc
$ chown mongod:mongod /www/database/mongoc
```

## 配置

在三节点上均配置

```
$ vim /etc/mongoc.conf
```

内容如下

```
storage:
  dbPath: /www/database/mongoc
  journal:
    enabled: true

systemLog:
  destination: file
  logAppend: true
  path: /var/log/mongodb/mongoc.log

sharding:
   clusterRole: configsvr

replication:
  replSetName: mongoc # 此处可以自定义，这三台必须是相同名字

net:
  port: 27018
  bindIp: 0.0.0.0

processManagement:
  fork: true
  pidFilePath: /var/run/mongodb/mongoc.pid
```

## 设置启动项

在三节点上均配置

```
$ vim /usr/lib/systemd/system/mongoc.service
```

```
[Unit]
Description=MongoDB Config Server
After=network.target
Documentation=https://docs.mongodb.org/manual

[Service]
User=mongod
Group=mongod
Environment="OPTIONS=-f /etc/mongoc.conf"
EnvironmentFile=-/etc/sysconfig/mongod
ExecStart=/usr/bin/mongod $OPTIONS
ExecStartPre=/usr/bin/mkdir -p /var/run/mongodb
ExecStartPre=/usr/bin/chown mongod:mongod /var/run/mongodb
ExecStartPre=/usr/bin/chmod 0755 /var/run/mongodb
PermissionsStartOnly=true
PIDFile=/var/run/mongodb/mongoc.pid
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
systemctl enable mongoc
# 启动
systemctl start mongoc
# 关闭
systemctl stop mongoc
```


## 激活集群

启动三节点之后，连接到任意一节点，**注意以下命令只能在一节点执行**

```
$ mongo --host mongoc1 --port 27018
```

执行下面的指令

```
rs.initiate(
  {
    _id: "mongoc", // 这里就是上面设置的replication.replSetName的集群唯一名字
    configsvr: true,
    members: [
      { _id : 0, host : "mongoc1:27018" },
      { _id : 1, host : "mongoc2:27018" },
      { _id : 2, host : "mongoc3:27018" }
    ]
  }
)
```

返回的结果中 `ok: 1` 即成功，其它情况查看`errmsg`

成功后可以使用 `rs.status()` 查看具体的集群情况，正常情况下会返回一长串结果，包含每个节点的主次信息：`PRIMARY` `SECONDARY`


