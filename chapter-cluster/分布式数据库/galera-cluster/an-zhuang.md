# 安装

## ## 安装源
```
$ vim /etc/yum.repos.d/galera.repo
```
CentOS 7 写入如下内容

> 如果是`CentOS 6` 下文网址则改为 `centos/6/x86_64`
> `mysql-wsrep-5.7` 改成你需要的`MySQL`版本

```
[galera]
name = Galera
baseurl = http://releases.galeracluster.com/galera-3/centos/7/x86_64
gpgkey = http://releases.galeracluster.com/GPG-KEY-galeracluster.com
gpgcheck = 1

[mysql-wsrep]
name = MySQL-wsrep
baseurl =  http://releases.galeracluster.com/mysql-wsrep-5.7/centos/7/x86_64
gpgkey = http://releases.galeracluster.com/GPG-KEY-galeracluster.com
gpgcheck = 1
```

## ## 安装软件

```
$ yum install galera-3 mysql-wsrep-5.7
```

这么直接安装会遇到`Transaction Check Error`的错误：
```
file /usr/share/mysql/bulgarian/errmsg.sys from install of mysql-wsrep-common-5.7-5.7.21-25.14.el7.x86_64 conflicts with file from package mysql-community-common-5.7.22-1.el7.x86_64
```

这句话的意思是本地安装了最新的MySQL 5.7.22 但是mysql-wsrep-5.7还只支持5.7.21，解决这个问题，重装MySQL-5.7.21
```
yum remove mysql-*
yum install mysql-community-server-5.7.21-1.el7.x86_64
```
