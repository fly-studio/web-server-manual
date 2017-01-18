```
- input_type: log
  paths:
    - /var/log/nginx/access*.log
    - /etc/nginx/logs/access*.log
  document_type: nginx-access
- input_type: log
  paths:
    - /var/log/nginx/error*.log
    - /etc/nginx/logs/error*.log
  document_type: nginx-error
```