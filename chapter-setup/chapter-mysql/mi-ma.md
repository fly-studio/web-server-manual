# 密码

## MySQL 8 初始化密码修改

查看初始化密码

```
cat /var/log/mysql/error.log | grep 'temporary password'
```

初始化密码是不能进行任何操作的, 必须命令行进入mysql修改


使用初始密码进入控制台
```
mysql -uroot -p
```

执行
```
alter user user() identified by '修改的密码'
```