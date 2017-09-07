## ## 查看第一次安装的root密码
```
$ grep "password" /var/log/mysqld.log
```

## ## 修改mysql密码
```
$ mysqladmin -u root password -p
```
或者
```
$ /usr/bin/mysql_secure_installation
```

## ## 添加root用户
先使用`mysql -u root -p ` 在服务器上进入MYSQL控制台
```
CREATE USER 'root'@'%' IDENTIFIED BY '密码';
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
```

## ## 普通用户
- 不包含`DROP`的权限，有需要可以自己添加
- `%` 代表所有主机，`localhost`、`127.0.0.1`表示本地

```
CREATE USER '用户名'@'%' IDENTIFIED BY '密码';
GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, CREATE VIEW, SHOW VIEW ON *.* TO '用户名'@'%';
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
mysql> UPDATE `mysql`.`user` SET `Password`=PASSWORD('新密码') WHERE `User`='root';
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

