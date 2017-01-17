Laravel项目，PHP所有日志都在`storage/logs/laravel-*.log`文件，但这个日志不方便同步到logstash。

如果你使用的 L+项目，在`addons/elasticsearch`中有`logstash`的输出模块，

总共有3种`logstash`的输出方式


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
# # 日志输出到 redis
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

# #日志输出到elasticsearch
```
$ vim /www/website/base/config/elasticsearch.php
```
```
'logstashDriver' => 'elasticsearch', //file redis elasticsearch
```

此处将laravel的日志直接写到elastic服务器端，Logstash只需要去读取elastic即可得到日志




