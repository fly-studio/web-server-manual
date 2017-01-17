# 日志类型

## ## 默认日志配置
适合没有修改过的nginx配置
```
log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                     '$status $body_bytes_sent "$http_referer" '
                     '"$http_user_agent" "$http_x_forwarded_for" ';
```
新建 patterns
```bash
$ vim /etc/logstash/patterns.d/nginx-access
```
```
MAINNGINXLOG %{COMBINEDAPACHELOG} "%{GREEDYDATA:forwarded_for}"
```
> 适配格式：

> 59.172.199.68 - - [17/Jan/2017:19:46:57 +0800]  "GET /img/icon-5.png HTTP/1.1" 200 4456 "https://xxx.com/xxxx" "Mozilla/5.0 (iPhone; CPU iPhone OS 10_0_2 like Mac OS X) AppleWebKit/602.1.50 (KHTML, like Gecko) Mobile/14A456 MicroMessenger/6.5.3 NetType/WIFI Language/zh_CN" "-"

## ## 自定义日志格式
```
log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                     '$status $body_bytes_sent "$http_referer" '
                     '"$http_user_agent" "$http_x_forwarded_for"'
                     '"$gzip_ratio" $request_time $bytes_sent $request_length';
```
新建 patterns
```bash
$ vim /etc/logstash/patterns.d/nginx-diy-access
```
```
DIYNGINXLOG %{COMBINEDAPACHELOG} %{QS:forwarded_for} %{QS:gzip_ratio} %{NUMBER:request_time:float} %{NUMBER:bytes_sent} %{NUMBER:request_length}
```

## ## JSON 日志
```bash
$ vim /etc/nginx/nginx.conf
```
在 http 中添加
```
    log_format logstash_json '{ "@timestamp": "$time_iso8601", '
                         '"host":"$server_addr",'
                         '"http_host":"$host",'
                         '"client": "$remote_addr",'
                         '"url":"$uri",'
                         '"referer":"$http_referer",'
                         '"status": "$status", '
                         '"request": "$request", '
                         '"body_bytes_size":$body_bytes_sent,'
                         '"bytes_size":$bytes_sent,'
                         '"upstream_time":"$upstream_response_time",'
                         '"upstream_host":"$upstream_addr",'
                         '"remote_user": "$remote_user", '
                         '"request_time": "$request_time", '
                         '"request_method": "$request_method", '
                         '"gzip_ratio":"$gzip_ratio", '
                         '"x_forwarded_for": "$http_x_forwarded_for", '
                         '"user_agent": "$http_user_agent" }';

    access_log /var/log/nginx/logstash.log logstash_json;
```

# Logstash 配置

```
$ vim /etc/logstash/conf.d/apache.conf
```
```
input {
  file {
    # nginx的日志地址
    path => "/var/log/nginx/*.log"
    start_position => "beginning"
  }
}
filter {
  if [path] =~ "access" {
    mutate { replace => { "type" => "nginx_access" } }
    grok {
      patterns_dir => [ "/etc/logstash/patterns.d/" ]
      match => [
        "message" , "%{MAINNGINXLOG}",
        "message" , "%{DIYNGINXLOG}",
        "message" , "%{COMBINEDAPACHELOG}+%{GREEDYDATA:extra_fields}",
        "message", "%{GREEDYDATA:access_message}"
      ]
    }
    mutate {
      remove_field => [ "message" ]
      add_field => ["timestamp_submitted", "%{@timestamp}"]
    }
    date {
      match => [ "timestamp" , "dd/MMM/yyyy:HH:mm:ss Z" ]
      remove_field => [ "timestamp" ]
      target => '@timestamp'
    }
    geoip {
      source => "clientip"
    }
  } else if [path] =~ "error" {
    mutate { replace => { "type" => "nginx_access" } }
    grok {
        match => [
          "message" , "(?<datetime>\d\d\d\d/\d\d/\d\d \d\d:\d\d:\d\d) \[(?<errtype>\w+)\] \S+: \*\d+ (?<errmsg>[^,]+), (?<errinfo>.*)$",
          "message", "%{GREEDYDATA:error_message}"
      ]
    }
    mutate {
        rename => [ "host", "fromhost" ]
        gsub => [ "errmsg", "too large body: \d+ bytes", "too large body" ]
    }
    if [errinfo]
    {
        ruby {
            code => "
                new_event = LogStash::Event.new(Hash[event.get('errinfo').split(', ').map{|l| l.split(': ')}])
                new_event.remove('@timestamp')
                event.append(new_event)
            "
        }
    }
    grok {
      patterns_dir => ["/etc/logstash/patterns.d/"]
      match => { "request" => '"%{WORD:verb} %{URIPATH:urlpath}(?:\?%{URIPARAM:urlparam})?(?: HTTP/%{NUMBER:httpversion})"' }
       remove_field => [ "message", "errinfo", "request" ]
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



