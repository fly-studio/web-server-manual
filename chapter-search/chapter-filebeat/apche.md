```
- input_type: log
  paths:
    - /var/log/httpd/access_log*
  document_type: apache-access
- input_type: log
  paths:
    - /var/log/httpd/error_log*
  document_type: apache-error

```

