# 安装节点

这里举例另外三台机器，IP 为 `192.168.1.201` ~ `192.168.1.203`

## 将这些IP加入`/etc/hosts`

```
192.168.1.201 mongod1
192.168.1.202 mongod2
192.168.1.203 mongod3
```

## 安装

安装方法请参见[MongoDB Yum 安装](/chapter-setup/chapter-mongodb/an-zhuang/yum-an-zhuang.md)

## 修改配置

```
$ vim /etc/mongod.conf
```

配置请参见[MongoDB 配置](/chapter-setup/chapter-mongodb/pei-zhi.md)

需要修改的内容如下，注意保持其它内容不变

```  
net:
  port: 27019
  bindIp: 0.0.0.0
  
replication:
  replSetName: mongod # 这个自定义，但是集群中此名字要保持一致

```

## 加入并激活集群

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


