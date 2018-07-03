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

本机一定不要安装MySQL

```
$ yum install galera-3 mysql-wsrep-5.7
```

本机一定不要安装MySQL，不然会遇到这个错误：`Transaction Check Error`：
```
file /usr/share/mysql/bulgarian/errmsg.sys from install of mysql-wsrep-common-5.7-5.7.21-25.14.el7.x86_64 conflicts with file from package mysql-community-common-5.7.22-1.el7.x86_64
```

卸载MySQL

```
yum remove mysql-*
```


