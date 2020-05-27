# 配置

## 启动管理员网页

```
$ rabbitmq-plugins enable rabbitmq_management
```

查看所有支持的插件

```
$ rabbitmq-plugins list
```

## 端口

需要在`firewall-cmd`中开启这些端口

```
4369 -- erlang发现口

5672 --client端通信口

15672 -- 管理界面ui端口

25672 -- server间内部通信口
```

- 比如PHP等客户端连接 需要`5672`端口 `amqp://root:password@ip:5672/`
- 如果访问管理后台，则 `http://ip:15672`


## 用户

默认只有本地的guest用户

添加root用户

```
$ rabbitmqctl add_user root passwd
```

设置为超管

```
rabbitmqctl set_user_tags root administrator
```

设置默认vhost（"/"）访问权限

```
rabbitmqctl set_permissions -p "/" root "." "." ".*"
```