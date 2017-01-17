# 默认日志

# JSON 日志
```bash
$ vim /etc/nginx/nginx.conf
```
在 http 段 的 `log_format main` 下面添加
```
log_format logstash_json '{ "@timestamp": "$time_iso8601", '
                         '"host":"$server_addr",'
                         '"client": "$remote_addr",'
                         '"size":$body_bytes_sent,'
                         '"upstream_time":"$upstream_response_time",'
                         '"upstream_host":"$upstream_addr",'
                         '"http_host":"$host",'
                         '"url":"$uri",'
                         '"remote_user": "$remote_user",'
                         '"request_time": "$request_time",'
                         '"status": "$status",'
                         '"request": "$request",'
                         '"request_method": "$request_method",'
                         '"http_referrer": "$http_referer",'
                         '"body_bytes_sent":"$body_bytes_sent",'
                         '"http_x_forwarded_for": "$http_x_forwarded_for",'
                         '"http_user_agent": "$http_user_agent" }';
access_log /var/log/nginx/logstash.log logstash_json;
```


