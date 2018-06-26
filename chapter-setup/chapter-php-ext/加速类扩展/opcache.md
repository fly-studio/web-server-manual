# # Opcache（PHP 5.5以上）
```
$ yum --enablerepo=remi-php71,remi install php-opcache
```
配置文件在：/etc/php.d/*-opcache.ini
```
[opcache]
zend_extension=opcache.so
opcache.enable=1
opcache.enable_cli=1
opcache.huge_code_pages=1
;opcache.file_cache=/tmp
```
还需要启用HugePage
```
# 分配512个预留的大页内存
$ sysctl vm.nr_hugepages=512
```
查看HugePage结果
```
cat /proc/meminfo  | grep Huge
```
```
AnonHugePages:    106496 kB
HugePages_Total:     512
HugePages_Free:      504
HugePages_Rsvd:       27
HugePages_Surp:        0
Hugepagesize:       2048 kB
```


