# YUM 安装

> MariaDB 是MySQL的分支，拥有MySQL的全部功能，并且有些功能较之更强大

## ## 添加源

```bash
$ vim /etc/yum.repos.d/MariaDB.repo
```

输入如下内容保存，下面的内容来源于[https://downloads.mariadb.org/mariadb/repositories/](https://downloads.mariadb.org/mariadb/repositories/)

```
# MariaDB 10.2 CentOS repository list - created 2017-06-28 07:07 UTC
# http://downloads.mariadb.org/mariadb/repositories/
[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/10.2/centos$releasever-amd64
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1
```

## 数据库服务器上安装

```bash
$ yum install MariaDB-server MariaDB-client
```

## PHP服务器上安装

```bash
$ yum --enablerepo=remi-php71,remi install MariaDB-devel php-mysqlnd
```

## 如果需要TukuDB

> TukuDB：1、高压缩比 2、高insert性能 3、增删字段秒级。

```bash
$ yum install MariaDB-tokudb-engine
```



