# 安装
分片式 MongoDB 将自动根据相关规则将数据放入每个分片中，分片之间有多重备份，在一个服务器



## 安装 Config 端

比如现在有三台机器，IP 为 192.168.1.101 ~ 192.168.1.103

### 现在将这些IP加入/etc/hosts

```
192.168.1.101 mongoc1
192.168.1.102 mongoc2
192.168.1.102 mongoc2
```

### 安装

在这三台机器上正常安装

```
yum install mongo-repo
```

### 建立库的文件夹

在三台上均创建

```
$ mkdir -R /www/database/mongoc
$ chown mongod:mongod /www/database/mongoc
```

### 配置

在三台上均配置

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

### 设置启动项

在三台上均配置

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

#### 这样启动方法就是
```
# 开机启动项
systemctl enable mongoc
# 启动
systemctl start mongoc
# 关闭
systemctl stop mongoc
```

