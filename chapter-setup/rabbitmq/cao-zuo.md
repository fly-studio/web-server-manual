# 操作

## 开机启动/取消

```
# 6.x
$ chkconfig --level 2345 rabbitmq-server on/off

# 7.x
$ systemctl enable/disable rabbitmq-server
```

## 启动/停止/重启

```
# 6.x
$ service rabbitmq-server start/stop/restart

# 7.x
$ systemctl start/stop/restart rabbitmq-server
```

## 卸载


```
$ yum remove rabbitmq-server

```
