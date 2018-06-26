# # Redis

本文以2台服务器为例，一台是缓存服务器，一台是PHP服务，如果只有一台，则在该台服务器上均执行即可

## 工作目录
```
$ mkdir -m 777 -p /www/database/redis/
$ chown -R redis:redis /www/database/redis/  #此句需要在安装完毕之后执行
```

## 安装

缓存服务器上安装
```
$ yum --enablerepo=remi install redis
```

PHP 服务器 需安装
```
$ yum --enablerepo=remi-php71,remi install php-redis
```

## 配置
```
$ vim /etc/redis.conf
```

```
...
daemonize yes # 后台运行模式
bind client1-lan-ip client2-lan-ip # 注释本行表示接受全部，也可以指定IP，用半角空格分隔
dir /www/database/redis/
appendonly yes
....
```

## 操作
### 开机启动
```
$ chkconfig --level 2345 redis on
# 7.0
$ systemctl enable redis.service

```

### 启动
```
$ service redis start
# 7.0
$ systemctl start redis.service
```

### 停止
```
$ service redis stop
# 7.0
$ systemctl stop redis.service
```

### 卸载

缓存服务器
```
$ yum remove redis
```

PHP 服务器
```
$ yum remove php-redis
```
