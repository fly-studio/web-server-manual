# # yum安装方式

## ## 安装官网源

> CentOS 6.x 需要 glibc 2.17以上
> 查看 [glibc的安装教程](/chapter-started/服务器「必要组建」和「开放端口」/glibc.md "glibc的安装教程")

### ### 5.7 repo库

```bash
# CentOS 7
$ wget http://repo.mysql.com/mysql57-community-release-el7-10.noarch.rpm
# CentOS 6
$ wget http://repo.mysql.com/mysql57-community-release-el6-10.noarch.rpm

$ rpm -ivh mysql*-community-release-*.noarch.rpm
```

### ### 5.5 
```bash
# CentOS 7
$ wget http://repo.mysql.com/yum/mysql-5.5-community/el/7/x86_64/mysql-community-release-el7-5.noarch.rpm
# CentOS 6
$ wget http://repo.mysql.com/yum/mysql-5.5-community/el/6/x86_64/mysql-community-release-el6-5.noarch.rpm

$ rpm -ivh mysql*-community-release-*.noarch.rpm
```
默认打开`MySQL 5.6`的源，关闭之后启动`5.5`的源
```
vim /etc/yum.repos.d/mysql-community.repo
```
```
[mysql55-community]
...
enabled=1


# Enable to use MySQL 5.6
[mysql56-community]
...
enabled=0
```


## ## 数据库服务器上安装

```bash
$ yum install mysql-server
```

> 无需启用remi-php55或php70 会导致mysql的版本过低

## ## PHP 服务器端需安装

```bash
$ yum --enablerepo=remi-php71,remi install php-mysqli
```

> 配合php的版本，需启用remi-php55或php71








