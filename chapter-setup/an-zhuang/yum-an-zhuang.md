# Yum 安装

## Repo 仓库


在`CentOS`上面安装最新版本的`MongoDB` 需要先设置其`repo仓库`

```
$ vim /etc/yum.repos.d/mongodb-org-4.0.repo
```

```
[mongodb-org-4.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/4.0/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-4.0.asc
```

## 安装指令

```
$ yum install mongodb-org
```

或者单独安装

```
$ yum install mongodb-org-server mongodb-org-mongos mongodb-org-shell mongodb-org-tools
```

- mongodb-org 包含上面的所有组建
- mongodb-org-server 主服务，配置文件在`/etc/mongod.conf`
- mongodb-org-mongos 路由
- mongodb-org-shell 客户端，`mongo`命令行
- mongodb-org-tools 工具箱，包含：[mongoimport](https://docs.mongodb.com/manual/reference/program/mongoimport/#bin.mongoimport) [bsondump](https://docs.mongodb.com/manual/reference/program/bsondump/#bin.bsondump), [mongodump](https://docs.mongodb.com/manual/reference/program/mongodump/#bin.mongodump), [mongoexport](https://docs.mongodb.com/manual/reference/program/mongoexport/#bin.mongoexport), [mongofiles](https://docs.mongodb.com/manual/reference/program/mongofiles/#bin.mongofiles), [mongorestore](https://docs.mongodb.com/manual/reference/program/mongorestore/#bin.mongorestore), [mongostat](https://docs.mongodb.com/manual/reference/program/mongostat/#bin.mongostat), [mongotop](https://docs.mongodb.com/manual/reference/program/mongotop/#bin.mongotop)

