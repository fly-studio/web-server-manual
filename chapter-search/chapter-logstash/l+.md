如果你使用的 L+项目，请查看下文的配置，因为laravel的错误日志很详细

# # 输出日志到文件
在L+项目，已经集成了logstash方案，开启方式也很简单
```
$ vim /www/website/base/config/elasticsearch.php
```
```
'logstashDriver' => 'file', //file redis elasticsearch
```


**文件路径**

如果 L+ 项目有日志输出，会将日志写入到`storage/logs/logstatsh-YYYY-MM-DD.log`
（不影响原日志的写入`storage/logs/laravel-YYYY-MM-DD.log`）。



## ## Filebeat

```
- input_type: log
  paths:
    - /www/website/base/storage/log/logstash-*.log
  document_type: base

```

## ## Logstash

Logstash需要如下配置
```
$ vim /etc/logstash/conf.d/l+log.conf
```
```
input {
  file {
    path => "/www/website/base/storage/logs/logstash-*.log"
    # elastic的type，可以和 l+ 项目名称相同
    type => "base"
    # 类型为 json
    codec => json {

    }
  }
}
```
# 日志输出到 redis
```
$ vim /www/website/base/config/elasticsearch.php
```
```
'logstashDriver' => 'redis', //file redis elasticsearch
```

如果laravel项目有日志输出，会在 redis 上插入一条日志
- redis 的服务配置在 `config/database.php`的`redis - default`
- **KEY** 的名称是`logstash-INDEX`
- **INDEX** 是 `config/elasticsearch.php`的`connections - default - index`
- 也就是说 `KEY = 'logstash-'.config('elasticsearch.connections.default.index')`

## ## Filebeat

filebeat 不支持redis的日志收集


## ## LogStash
```
$ vim /etc/logstash/conf.d/l+redis.conf
```
```
input {
  # redis读取方式
  redis {
    # redis 服务端
    host => "127.0.0.1"
    key => "logstash-base"
    data_type => ["list"]
  }
}
```

