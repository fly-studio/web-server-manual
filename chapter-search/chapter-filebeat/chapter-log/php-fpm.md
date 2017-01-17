```
- input_type: log
  paths:
    - /var/log/php-fpm/error.log
  document_type: php-fpm
- input_type: log
  paths:
    - /var/log/php-fpm/www-slow.log
  document_type: php-fpm-slow

```