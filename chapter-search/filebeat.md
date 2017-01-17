# 配置
```
$ vim /etc/filebeat/filebeat.yml
```
```
filebeat.prospectors:
- input_type: log
  paths:
    - /www/website/base/storage/log/logstash-*.log
  document_type: base

- input_type: log
  paths:
    - /var/log/nginx/access*.log
  document_type: nginx-access
- input_type: log
  paths:
    - /var/log/nginx/error*.log
  document_type: nginx-error

- input_type: log
  paths:
    - /var/log/httpd/access_log*
  document_type: apache-access
- input_type: log
  paths:
    - /var/log/httpd/error_log*
  document_type: apache-error

- input_type: log
  paths:
    - /var/log/redis/*.log
  document_type: redis

- input_type: log
  paths:
    - /var/log/php-fpm/error.log
  document_type: php-fpm
- input_type: log
  paths:
    - /var/log/php-fpm/www-slow.log
  document_type: php-fpm-slow

- input_type: log
  paths:
    - /var/log/mysqld.log
  document_type: mysql
- input_type: log
  paths:
    - /var/log/mysql-sql.log
  document_type: mysql-sql

- input_type: log
  paths:
    - /var/log/cron
  document_type: cron
  
output.logstash:
  hosts: ["elastic's ip:9601"]

```

# Logstash 接收端
```
$ vim /etc/logstash/conf.d/beat.conf
```
```
input {
  beats {
    port => 9601
  }
}
filter {
  if [type] == "nginx-access" {
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
  } else if [type] == "nginx-error" {
    grok {
        match => { "message" => "(?<datetime>\d\d\d\d/\d\d/\d\d \d\d:\d\d:\d\d) \[(?<errtype>\w+)\] \S+: \*\d+ (?<errmsg>[^,]+), (?<errinfo>.*)$" }
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
        match => { "request" => '"%{WORD:verb} %{URIPATH:urlpath}(?:\?%{URIPARAM:urlparam})?(?: HTTP/%{NUMBER:httpversion})"' }
        patterns_dir => ["/etc/logstash/patterns.d/"]
        remove_field => [ "message", "errinfo", "request" ]
    }
  } else if [type] == "apache-access" {
    grok {
      match => [
        "message", "%{COMBINEDAPACHELOG}",
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
  } else if [type] == "apache-error" {
    grok {
      patterns_dir => [ "/etc/logstash/patterns.d/" ]
      match => [
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
}
output {
  elasticsearch {
    hosts => ["10.13.115.90:9200"]
    index => "filebeat-%{+YYYY.MM.dd}"
    workers => 1
    flush_size => 10
    idle_flush_time => 10
    template_overwrite => true
  }
}
```