```
- input_type: log
  paths:
    - /var/log/nginx/access*.log
  document_type: nginx-access
- input_type: log
  paths:
    - /var/log/nginx/error*.log
  document_type: nginx-error
```