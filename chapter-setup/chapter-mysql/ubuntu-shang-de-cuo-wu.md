在Unbuntu上修改MySQL默认路径之后，要初始化数据库

```
mysqld --initialize --user=mysql --datadir=/www/database/mysql
```

即使给目录`/www/database/mysql`设置`chmod 0777` `chown mysql:mysql`都会报错
```
mysqld: Can't create directory '/www/database/mysql' (Errcode: 17 - File exists)
```

这是因为Ubuntu有个AppArmor，是一个Linux系统安全应用程序，类似于Selinux,
AppArmor默认安全策略定义个别应用程序可以访问系统资源和各自的特权，
如果不设置服务的执行程序，即使你改了属主属组并0777权限，也是对服务起不到作用。

```
vim /etc/apparmor.d/usr.sbin.mysqld
```

查找`/var/lib/mysql`修改为：

```
# Allow data dir access
  /www/database/mysql/ r,
  /www/database/mysql/** rwk,
```

重启`systemctl restart apparmor`再初始化即可