# # Memcache

本文以2台服务器为例，一台是缓存服务器，一台是PHP服务，如果只有一台，则在该台服务器上均执行即可

## 安装

缓存服务器上安装
```
# 如果可能，执行下面语句安装memcached，基于rpmforge库
$ yum --enablerepo=rpmforge install libevent memcached
```

PHP 服务器上需安装
```
$ yum --enablerepo=remi-php74,remi install php-pecl-memcache php-pecl-memcached
```

验证是否安装成功
```
$ memcached -h
```

## 操作

### 开机自动运行
```
$ chkconfig --level 2345 memcached on
# 7.0
$ systemctl enable memcached.service
```

### 启动
```
$ service memcached start
# 7.0
$ systemctl start memcache.service
```

### 关闭
```
$ service memcached stop
# 7.0
$ systemctl stop memcached.service
```

### 卸载

缓存服务器
```
$ yum remove memcached
```

PHP服务器
```
$ yum remove php-pecl-memcache php-pecl-memcached
```