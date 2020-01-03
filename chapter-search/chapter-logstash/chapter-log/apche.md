# # Logstash

Apache日志分为 access/error 日志两种

但是Logstash没有error的解析日志，所以添加一个错误日志的解析正则

## ## 错误日志解析正则表达式

### Apache 2.2
```bash
$ vim /etc/logstash/patterns.d/apache22-error
```
```
APACHE22_ERROR_LOG \[(?<timestamp>%{DAY:day} %{MONTH:month} %{MONTHDAY} %{TIME} %{YEAR})\] \[%{LOGLEVEL:loglevel}\]( \[client %{IP:clientip}:.*\])? %{GREEDYDATA:errormsg}
APACHE22_LOG_404 %{IPORHOST:clientip} %{HTTPDUSER:ident} (%{HTTPDUSER:auth}|"") \[%{HTTPDATE:timestamp}\] "(?:%{WORD:verb} %{NOTSPACE:request}(?: HTTP/%{NUMBER:httpversion})?|%{DATA:rawrequest})" %{NUMBER:response} (?:%{NUMBER:bytes}|-)
```
> 可匹配：

> [Sun Jan 01 03:29:45 2017] [error] [client 91.213.126.147] File does not exist: /data/www/Discuz/upload/discuz, referer: 

### Apache 2.4
```bash

$ vim /etc/logstash/patterns.d/apache24-error
```
```
APACHE24_ERROR_LOG \[(?<timestamp>%{DAY:day} %{MONTH:month} %{MONTHDAY} %{TIME} %{YEAR})\] \[.*:%{LOGLEVEL:loglevel}\] \[pid %{NUMBER:pid}:tid %{NUMBER:tid}\]( \[client %{IP:clientip}:.*\])? %{GREEDYDATA:errormsg}
```

> 可匹配：

> [Fri Nov 11 10:15:47.509138 2016] [:error] [pid 7632:tid 1928] [client 127.0.0.1:61179] PHP  20. Illuminate\\Routing\\Pipeline->Illuminate\\Routing\\{closure}() /laravel/vendor/addons/core/src


## ## Logstash 配置如下
```
$ vim /etc/logstash/conf.d/apache.conf
```
```
input {
  file {
    # apache的access日志地址
    path => [
      "/var/log/httpd/access_log"
      "/var/log/httpd/error_log"
    ]
    start_position => "beginning"
  }
}
filter {
  if [path] =~ "access" {
    mutate { replace => { "type" => "apache_access" } }
    grok {
      patterns_dir => ["/etc/logstash/patterns.d/"]
      match => [
        "message", "%{COMBINEDAPACHELOG}",
        "message", "%{APACHE22_LOG_404}",
        "message", "%{GREEDYDATA:access_message}"
      ]
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
      # 修改下文 APACHE22_ERROR_LOG APACHE24_ERROR_LOG 
      match => [
        "message", "%{HTTPD_ERRORLOG}",
        "message", "%{APACHE24_ERROR_LOG}",
        "message", "%{APACHE22_ERROR_LOG}",
        "message", "%{GREEDYDATA:error_message}"
      ]
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
output {
  elasticsearch { hosts => ["127.0.0.1:9200"] }
}

```


