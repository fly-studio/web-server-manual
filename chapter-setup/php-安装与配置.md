> :fa-cogs: 安装之前请确认安装了必须的 [Repo库](chapter-started/repo-仓库.md "Repo库")

# # PHP版本
## ## remi库
remi库包含这些PHP版本：5.4 5.5 5.6 7.0 7.1 7.2 7.3
#### - PHP 5.4
```
$ yum --enablerepo=remi install xxx
```
#### - PHP 5.5
```
$ yum --enablerepo=remi-php55,remi install xxx
```
#### - PHP 5.6
```
$ yum --enablerepo=remi-php56,remi install xxx
```
#### - PHP 7.0
```
$ yum --enablerepo=remi-php70,remi install xxx
```
#### - PHP 7.1
```
$ yum --enablerepo=remi-php71,remi install xxx
```
#### - PHP 7.2
```
$ yum --enablerepo=remi-php72,remi install xxx
```
#### - PHP 7.3
```
$ yum --enablerepo=remi-php73,remi install xxx
```


以上命令表示分别启用特定版本PHP、PHP扩展的安装
比如<code>yum --enablerepo=remi-php73,remi install php</code>表示安装PHP 7.3


# # 工作目录
## ## php配置
```
/etc/php.ini
```
## ## php扩展配置目录
```
/etc/php.d
```
## ## php扩展目录
```
/usr/lib64/php/modules
```
## ## session目录
```
/var/lib/php/session
```
## ## php-fpm日志
```
# 错误日志
/var/log/php-fpm/error.log
# 慢日志
/var/log/php-fpm/www-slow.log
```

