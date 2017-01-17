# 默认日志

# JSON 日志
```bash
$ vim /etc/nginx/nginx.conf
```
在 http 段 的 `log_format main` 上面添加
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


