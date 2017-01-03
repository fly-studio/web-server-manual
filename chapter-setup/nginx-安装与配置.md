> 安装之前请确认安装了必须的 [Repo库](chapter-started/repo-仓库.md "Repo库")

#  建立工作文件夹
```
＄ mkdir -m 777 -p /www/website
＄ chown -R nobody:nobody /www/website
```






# # SELinux 阻止 php-fpm 访问PHP文件
比如访问任何PHP文件都抛出：
```
file not found.
```
可以关闭SELinux，或者如下执行
```

$ setenforce 0
$ grep php-fpm /var/log/audit/audit.log | audit2allow -m php-fpmlocal > php-fpm.te
$ grep php-fpm /var/log/audit/audit.log | audit2allow -M php-fpmlocal
$ semodule -i php-fpmlocal.pp
$ setenforce 1
```



# # 日志
## ## 访问日志
```
/var/log/nginx/access.log
```
## ## 错误日志
```
/var/log/nginx/error.log
```
# # 访问监控
## ## GoAccess
GoAccess (nginX日志分析工具)
```
$ yum install goaccess GeoIP-devel
```
分析nginx的日志
```
$ goaccess -f /var/log/nginx/access.log -d -H -M --date-format="%d/%b/%Y" --time-format="%H:%M:%S %z"  --log-format="%h \- %^ [%d:%t] \"%r\" %s %b \"%R\" \"%u\" %^ \"%^\" %L"

# 保存为HTML文件
$ goaccess -f /var/log/nginx/access.log -d -H -M --real-os --date-format="%d/%b/%Y" --time-format="%H:%M:%S" --log-format="%h %^[%d:%t] \"%r\" %s %b" --output-format=html > /www/website/access.html
```
处理压缩包
```
$ zcat /var/log/nginx/access.log-20150122.gz | goaccess -d -H -M --date-format="%d/%b/%Y" --log-format="%h %^[%d:%^] \"%r\" %s %b" 
```
分析目录下所有压缩包日志
```
$ zcat access.log* | goaccess -...
```
分析某天的日志
```
$ sed -n '/05/Dec/2014/,$ p' access.log | goaccess -...
```
分析从11月5号到12月5号一个月内的日志
```
sed -n '/5/Nov/2014/,/5/Dec/2014/ p' access.log | goaccess -...
```
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
## ## 启用br压缩算法
http段 添加
```
brotli               on;
brotli_comp_level    6;
brotli_buffers       16 8k;
brotli_min_length    20;
brotli_types         *;
```