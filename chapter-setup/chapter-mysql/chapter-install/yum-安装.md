# # yum安装方式

## ## 安装官网源

### ### 5.7

```bash
# 7.x
$ wget http://repo.mysql.com/mysql57-community-release-el7-10.noarch.rpm

# 6.x
$ wget http://repo.mysql.com/mysql57-community-release-el6-10.noarch.rpm
```

```
$ rpm -ivh mysql*-community-release-*.noarch.rpm
```

> `MySQL 5.7`在`CentOS 6.x`下安装，需要`glibc 2.17`以上
> 查看 [glibc的安装教程](/chapter-started/服务器「必要组建」和「开放端口」/glibc.md "glibc的安装教程")


### ### 5.5 

```bash
# 7.x
$ wget http://repo.mysql.com/yum/mysql-5.5-community/el/7/x86_64/mysql-community-release-el7-5.noarch.rpm

# 6.x
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

## ## PHP 服务器端需安装

```bash
$ yum --enablerepo=remi-php74,remi install php-mysqli
```

> 配合php的版本，需启用remi-php55或php74








