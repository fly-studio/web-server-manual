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

通过SQL设置密码

```
# 当前用户 （空密码）
SET PASSWORD = PASSWORD('');
# 指定用户
SET PASSWORD FOR 'root'@'localhost' = PASSWORD('123456');
```

## ## 降低密码复杂度

> 如果使用上面的方法配置密码出现 
> `Your password does not satisfy the current policy requirements`
> 表示设置的密码过于简单可以使用以下方法降低密码的复杂度。当然了，简单密码一般用于开发环境。


使用原密码`mysql -u root -p`登录进去

```
# 设置policy为LOW
set global validate_password_policy=0;
# 设置大小写、数字、特殊字符的数量
set global validate_password_mixed_case_count=0;
set global validate_password_number_count=0;
set global validate_password_special_char_count=0;
# 下面的值是上面3个的总数。
set global validate_password_length=0;
```

## ## 密码永不过期

比如`root@localhost`用户

```
ALTER USER 'root'@'localhost' PASSWORD EXPIRE NEVER;
flush privileges;
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

授予全部权限, 包含`grant option`

```
grant all privileges on *.* to 'root'@'%' with grant option;
```

## ## 刷新权限表

修改任意用户权限之后，需要刷新权限表

```
flush privileges;
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

