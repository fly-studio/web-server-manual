```
- input_type: log
  paths:
    - /var/log/mysqld.log
  document_type: mysql

- input_type: log
  paths:
    - /var/log/mysql-sql.log
  document_type: mysql-sql
```