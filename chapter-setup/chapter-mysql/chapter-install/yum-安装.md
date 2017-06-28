# # yum安装方式

## ## 安装官网源

> 需要 glibc 2.17以上，而centos 6很难编译，建议在centos 7下安装， 笔者在CentOS 6.5下安装glibc不成功，最终还是用的remi安装的 MySQL 5.5  
> 有兴趣折腾的：查看 [glibc的安装教程](/base/manual/23#h2--gcc-0-1 "glibc的安装教程")

### ### repo库

```bash
# CentOS 7
$ wget http://repo.mysql.com/mysql57-community-release-el7-9.noarch.rpm
# CentOS 6
$ wget http://repo.mysql.com/mysql57-community-release-el6-9.noarch.rpm
$ rpm -ivh mysql*-community-release-*.noarch.rpm
```

## ## 数据库服务器上安装

```bash
# MySQL 5.5
$ yum install --enablerepo=remi mysql-server
# MySQL 5.7
$ yum install mysql-server
```

> 无需启用remi-php55或php70 会导致mysql的版本过低

## ## PHP 服务器端需安装

```bash
$ yum --enablerepo=remi-php71,remi install php-mysqli
```

> 配合php的版本，需启用remi-php55或php71








