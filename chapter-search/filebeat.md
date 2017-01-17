```
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
    - /var/log/php-fpm/www.log
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

```