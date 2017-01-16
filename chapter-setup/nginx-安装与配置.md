> 安装之前请确认安装了必须的 [Repo库](chapter-started/repo-仓库.md "Repo库")


# # 一些配置
## ## domain.com 跳转到 www.domain.com
在 ```/etc/nginx/conf.d/default.conf``` 开头添加
```
server {
        listen       80;
        server_name  domain.com;
        return       301 http://www.domain.com$request_uri;
}
```
