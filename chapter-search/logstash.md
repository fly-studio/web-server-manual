# # 安装
如果你有安装过 elasticsearch.repo，那么直接使用yum安装即可
```bash
$ yum install logstash
```
# # 配置
## ## JAVA的环境变量
```
$ vim /etc/profile
```
```bash
JAVA_HOME=/usr/lib/jvm/jre-1.8.0-openjdk
JRE_HOME=/usr/lib/jvm/jre-1.8.0-openjdk-1.8.0.111-1.b15.el7_2.x86_64
PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
export JAVA_HOME JRE_HOME PATH
```
```
$ source /etc/profile
```
## ## 数据文件夹
```bash
$ mkdir /www/database/logstash/
$ chown -R logstash:logstash /www/database/logstash/
```
## ## 主配置文件
```bash
$ vim /etc/logstash/logstash.yml
```
```
path.data: /www/database/logstash/
```
## ## 运行配置
Logstash的运行依赖运行配置文件，所有的日志、通道都是通过此文件配置
```bash
$ vim /etc/logstash/conf.d/l+.conf
```

以下是一个配置示例
```
input {
  file {
    path => "/www/website/base/storage/logs/logstash-*.log"
    type => "base"
    codec => json {
    }
  }
  # 此处可以添加更多来源
}

filter {

}
# 下文均省略output，请自行加上
output {
  elasticsearch { hosts => ["127.0.0.1:9200"] }
  # 在服务器运行时，下面的可以不用
  stdout { codec => rubydebug }
}
```

## ## Apache 日志
Apache日志分为 access/error 日志两种
但是Logstash没有error的解析日志，所以添加一个错误日志的解析正则

错误日志解析正则表达式
```
$ mkdir /etc/logstash/patterns.d/
$ vim /etc/logstash/patterns.d/apache-error
```
```
APACHE_ERROR_LOG \[(?<timestamp>%{DAY:day} %{MONTH:month} %{MONTHDAY} %{TIME} %{YEAR})\] \[.*:%{LOGLEVEL:loglevel}\] \[pid %{NUMBER:pid}:tid %{NUMBER:tid}\]( \[client %{IP:clientip}:.*\])? %{GREEDYDATA:errormsg}
```

> 可匹配：
> [Fri Nov 11 10:15:47.509138 2016] [:error] [pid 7632:tid 1928] [client 127.0.0.1:61179] PHP  20. Illuminate\\Routing\\Pipeline->Illuminate\\Routing\\{closure}() /laravel/vendor/addons/core/src


Logstash 配置如下
```
$ vim /etc/logstash/conf.d/apache.conf
```
```
input {
  file {
    # apache的access日志地址
    path => "/opt/xampp/apache/logs/*.log"
    start_position => "beginning"
  }
}
filter {
  if [path] =~ "access" {
    mutate { replace => { "type" => "apache_access" } }
    grok {
      match => { "message" => "%{COMBINEDAPACHELOG}" }
    }
    mutate {
      remove_field => [ "message" ]
      add_field =>  ["timestamp_submitted", "%{@timestamp}"]
    }
    date {
      match => [ "timestamp" , "dd/MMM/yyyy:HH:mm:ss Z" ]
      remove_field => [ "timestamp" ]
      target => '@timestamp'
    }
  } else if [path] =~ "error" {
    mutate { replace => { type => "apache_error" } }
    grok {
      patterns_dir => [ "/etc/logstash/patterns.d/" ]
      match => { "message" => "%{APACHE_ERROR_LOG}" }
    }
    mutate {
      remove_field => [ "message" ]
      add_field =>  ["timestamp_submitted", "%{@timestamp}"]
    }
    if !("_grokparsefailure" in [tags]) {
      date {
        # Try to pull the timestamp from the 'timestamp' field (parsed above with grok). The apache time format looks like: "18/Aug/2011:05:44:34 -0700"
        #                        Sat Feb 08 06:31:09.123456 2014
        match => [ "timestamp", "EEE MMM dd HH:mm:ss.SSSSSS yyyy" ]
        target => '@timestamp'
        remove_field => [ "timestamp" ]
      }
    }
  }
  geoip {
    source => "clientip"
  }

}
```
## ## nginX



## ## MySQL

## ## php-fpm

## ## crontab

## ## supervisor

## ## PHP
如果你使用的 L+项目，请查看下文的配置，因为laravel的错误日志很详细

## ## L+ 项目开启Logstash
在L+项目，已经集成了logstash方案，开启方式也很简单
```
$ vim /www/website/base/config/elasticsearch.php
```
```
'logstashDriver' => 'file', //file redis elasticsearch
```
这个`logstashDriver`可以设置为如下值
#### - file
如果 L+ 项目有日志输出，会将日志写入到`storage/logs/logstatsh-YYYY-MM-DD.log`
（不影响原日志的写入`storage/logs/laravel-YYYY-MM-DD.log`）。

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
#### - redis
如果laravel项目有日志输出，会在 redis 上插入一条日志
- redis 的服务配置在 `config/database.php`的`redis - default`
- **KEY** 的名称是`logstash-INDEX`
- **INDEX** 是 `config/elasticsearch.php`的`connections - default - index`
- 也就是说 `KEY = 'logstash-'.config('elasticsearch.connections.default.index')`

LogStash 需要如下配置
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

#### elasticsearch
此处将laravel的日志直接写到elastic服务器端，Logstash只需要去读取elastic即可得到日志


# # 启动
## ## 创建服务
### ### CentOS 7
如果已经安装过`ruby`，以及`gem install pleaserun`
```
$ pleaserun -p systemd -v default --install /usr/share/logstash/bin/logstash -f /etc/logstash/conf.d/ --config.reload.automatic
```
如果没有请ruby，参照
```
$ vim /usr/lib/systemd/system/logstash.service
```
内容查看
> https://github.com/guardian/machine-images/blob/master/packer/resources/features/elk-stack/systemd-logstash.service

### ### CentOS 6
```
$ vim /etc/init.d/logstash
```
> 参照 https://gist.githubusercontent.com/piavlo/4538157/raw/0f4c05bfa7c5fece96065b60a19c063d99058cb2/etc%2520init.d%2520logstash
