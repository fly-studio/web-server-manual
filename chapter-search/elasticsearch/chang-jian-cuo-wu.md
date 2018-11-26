# 常见错误

## bootstrap.memory_lock 导致无法启动的问题

```
$ vim /etc/sysconfig/elasticsearch
```
取消注释 或者加上此行
```
MAX_LOCKED_MEMORY=unlimited
```

```
$ vim /etc/security/limits.conf
```
结尾添加
```
elasticsearch soft memlock unlimited
elasticsearch hard memlock unlimited
```

```
$ vim /usr/lib/systemd/system/elasticsearch.service
```

在`[Install]`上面加上
```
LimitMEMLOCK=infinity
```

然后在`/etc/elasticsearch/elasticsearch.yml`中开启`bootstrap.memory_lock: true`即可