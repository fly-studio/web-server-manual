# 配置

默认的配置文件在`/etc/mongod.conf
`

## 建立库的文件夹

在三台上均创建

```
$ mkdir -p /www/database/mongod
$ chown mongod:mongod /www/database/mongod
```

## 修改配置
```
$ vim /etc/mongod.conf
```

需要修改的内容如下，注意保持其它内容不变

```
storage:
  dbPath: /www/database/mongod
  
net:
  port: 27017
  bindIp: 0.0.0.0
```

## 启动方法



