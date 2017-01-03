> :fa-cogs: 安装之前请确认安装了必须的 [Repo库](chapter-started/repo-仓库.md "Repo库")

:fa-warning: 请仔细确认你的PHP版本，并根据具体情况修改下文中的remi库
> --enablerepo=`remi` 表示安装PHP 5.4的扩展
>
> --enablerepo=`remi-php55,remi` 表示安装PHP 5.5的扩展
>
> --enablerepo=`remi-php56,remi` 表示安装PHP 5.6的扩展
>
> --enablerepo=`remi-php70,remi` 表示安装PHP 7.0的扩展
>
> 关于remi的解释，请查看「PHP 安装与配置」


# # 安装
## ## 设置运行目录
```
$ mkdir -m 777 -p /www/database/mysql/
```

## ## 安装

数据库服务器上安装
```
$ yum install --enablerepo=remi mysql-server
```
> 无需启用remi-php55或php70 会导致mysql的版本过低

PHP 服务器端需安装
```
$ yum --enablerepo=remi-php70,remi install php-mysqli
```
> 配合php的版本，需启用remi-php55或php70

## ## 从mysql官网安装(最新版)
> 需要 glibc 2.17以上，而centos 6很难编译，建议在centos 7下安装， 笔者在CentOS 6.5下安装glibc不成功，最终还是用的remi安装的 MySQL 5.5
> 有兴趣折腾的：查看 [glibc的安装教程](/base/manual/23#h2--gcc-0-1 "glibc的安装教程")

 repo库
```
# CentOS 7
$ wget http://repo.mysql.com/mysql57-community-release-el7-8.noarch.rpm
# CentOS 6
$ wget http://repo.mysql.com/mysql57-community-release-el6-8.noarch.rpm
$ rpm -ivh mysql*-community-release-*.noarch.rpm
```
接着正常安装


## ## 安装MariaDB
> MariaDB 是MySQL的分支，拥有MySQL的全部功能，并且有些功能较之更强大

添加源
```
$ vim /etc/yum.repos.d/MariaDB.repo
```
输入如下内容保存，下面的内容来源于https://downloads.mariadb.org/mariadb/repositories/
#### CentOS 6.5
```
# MariaDB 10.1 CentOS repository list - created 2016-03-31 14:00 UTC
# http://mariadb.org/mariadb/repositories/
[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/10.1/centos6-amd64
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1
```
#### CentOS 7
```
# MariaDB 10.1 CentOS repository list - created 2016-03-31 14:01 UTC
# http://mariadb.org/mariadb/repositories/
[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/10.1/centos7-amd64
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1
```
数据库服务器上安装
```
$ yum install MariaDB-server MariaDB-client
```
PHP服务器上安装
```
$ yum --enablerepo=remi-php70,remi install MariaDB-devel php-mysqlnd
```
如果需要TukuDB
>TukuDB：1、高压缩比 2、高insert性能 3、增删字段秒级。

```
$ yum install MariaDB-tokudb-engine
```

## ## 工作目录
```
$ chown -R mysql:mysql /www/database/mysql
```

# # 配置
## ## 配置MySQL
主配置文件
```
$ vim /etc/my.cnf
```
```
[client]
default-character-set = utf8mb4
[mysql]
default-character-set = utf8mb4
[mysqld]
character-set-client-handshake = FALSE
character-set-server = utf8mb4
collation-server = utf8mb4_unicode_ci
; init_connect='SET NAMES utf8mb4' ;这句可以不加
...
; 修改路径
datadir = /www/database/mysql
...
innodb_data_home_dir = /www/database/mysql
innodb_data_file_path = ibdata1:200M;ibdata2:10M:autoextend
innodb_file_per_table = 1
innodb_log_group_home_dir = /www/database/mysql
...

general_log = 1
general_log_file=/var/log/mysql-sql.log

# 其它配置，比如缓冲区、线程数、连接数等，酌情配置
```
## ## 配置MariaDB
主配置文件
```
$ vim /etc/my.cnf.d/server.cnf
```
```
[mysqld]
datadir=/www/database/mysql
socket=/var/lib/mysql/mysql.sock
#default-character-set=utf8mb4 ;这句话MariaDB无法识别
character_set_server=utf8mb4
slow_query_log=on
slow_query_log_file=/var/log/mysqld-slow.log
long_query_time=2
```
创建慢日志
> MariaDB无法自己创建该文件

```
$ touch /var/log/mysqld-slow.log
$ chmod 666 /var/log/mysqld-slow.log
```

# # 操作

> 在MariaDB上，以下语句如果无法执行，将<mark>mysqld</mark>换做<mark>mysql</mark>

## 自启服务
```
$ chkconfig --level 2345 mysqld on
# 7.0
$ systemctl enable mysqld.service
# MariaDB
$ chkconfig --level 2345 mysql on
# 7.0 MariaDB
$ systemctl enable mariadb.service 
```
## ## 初始化数据库
> MySQL第一次启动数据库时，会自动初始化数据库 但MariaDB不会

```
$ mysql_install_db --user=mysql --datadir=/www/database/mysql
```

## 启动
```
# MySQL(下同)
$ service mysqld start
# 7.0
$ systemctl start mysqld.service
# MariaDB(下同)
$ service mysql start
# 7.0
$ systemctl start mysql.service
```

## 重启
```
$ service mysqld restart
# 7.0
$ systemctl restart mysqld.service
$ service mysql restart
# 7.0
$ systemctl restart mysql.service
```

## 停止
```
$ service mysqld stop
# 7.0
$ systemctl stop mysqld.service
$ service mysql stop
# 7.0
$ systemctl stop mysql.service
```

# # 其它
## ## 查看第一次安装的root密码
```
$ grep "password" /var/log/mysqld.log
```
## ## 查看MySQL错误日志
```
$ cat /var/log/mysqld.log
```

## ## 修改mysql密码
```
$ mysqladmin -u root password
```
或者
```
$ /usr/bin/mysql_secure_installation
```
## ## 查看运行状态
```
/etc/rc.d/init.d/mysqld status
```
## ## 忘记 Root 密码

1. 跳过权限检查方式启动
```
$ mysqld_safe --skip-grant-tables
# 或只允许本地访问
$ mysqld_safe --skip-grant-tables --skip-networking
```
2. 登录mysql
	> 此时不检查密码
	
	```
$ mysql -u root
	```
3. 修改root密码
```
mysql> UPDATE `mysql`.`user` SET `Password`=PASSWORD('new-password') WHERE `User`='root';
```
4. 刷新权限
```
mysql> flush privileges;
```
5. 退出
```
mysql> quit
```
6. 退出mysql进程
```
$ mysqladmin -u qualquer_coisa shutdown
$ service mysqld start
```

# # 客户端
## ## phpMyAdmin
### 安装phpMyAdmin
```
$ wget -c "https://files.phpmyadmin.net/phpMyAdmin/4.6.5.1/phpMyAdmin-4.6.5.1-all-languages.zip" -O "phpMyAdmin.zip"
$ unzip -o -d /www/website/ phpMyAdmin.zip
$ mv /www/website/phpMyAdmin-* /www/website/phpMyAdmin
```
### 配置
1. 访问 http://127.0.0.1/phpMyAdmin/setup/
2. 新建服务器并配置相应参数
3. 复制配置到：/www/website/phpMyAdmin/config.inc.php
4. http://127.0.0.1/phpMyAdmin Done!

## ## Navicate Premium
> 下载地址：http://www.navicat.com/download

# # 一些常见的错误
## ## MySQL在升级之后，遇到Cannot load from mysql.proc. The table is probably corrupted
```
mysql_upgrade -u root -p
```
或
```
ALTER TABLE `mysql`.`proc` MODIFY COLUMN `comment`  text CHARACTER SET utf8 COLLATE utf8_bin NOT NULL AFTER `sql_mode`;
```
## ## [Warning] Can't create test file xxx.lower-test
在改变了数据库路径后，无数次地试图service mysql start，或者 /etc/init.d/mysql start，以及mysql_install_db，都报错
### 关闭SELinux
```
# 临时关闭
$ setenforce 0
# 永久关闭
$ vim /etc/selinux/config
SELINUX=disabled
```
### apparmor
它也对mysql所能使用的目录权限做了限制
在 /etc/apparmor.d/usr.sbin.mysqld 这个文件中，有这两行，规定了mysql使用的数据文件路径权限
```
/var/lib/mysql/ r,
/var/lib/mysql/** rwk,
```
```/var/lib/mysql/``` 就是之前mysql安装的数据文件默认路径，apparmor控制这里mysqld可以使用的目录的权限
照上面那两条，增加下面这两条就可以了
```
/www/datatbase/mysql/ r,
/www/datatbase/mysql/** rwk,
```
重启apparmor
```
$ /etc/inid.d/apparmor restart
```