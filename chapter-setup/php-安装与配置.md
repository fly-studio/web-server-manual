> :fa-cogs: 安装之前请确认安装了必须的 [Repo库](chapter-started/repo-仓库.md "Repo库")

# # PHP版本
## ## remi库
remi库包含这些PHP版本：5.4 5.5 5.6 7.0 7.1
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
$ yum --enablerepo=remi-php70,remi install xxx
```
以上命令表示分别启用特定版本PHP、PHP扩展的安装
比如<code>yum --enablerepo=remi-php70,remi install php</code>表示安装PHP 7.0

## ## 切换PHP版本
如果服务器已经安装了其它PHP版本，请先删除PHP、以及PHP的所有扩展，然后再安装新版的PHP
```bash
$ yum remove php php-* gd
# 以及卸载所有编译安装的扩展
$ rm -rf /usr/lib64/php/modules/*.so
$ rm -rf /etc/php.d/*.ini
```
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
# # 安装PHP
```
$ yum --enablerepo=remi-php70,remi install php php-cgi php-fastcgi pcre pcre-devel php-fpm gd php-gd libjpeg libjpeg-devel libpng libpng-devel freetype freetype-devel zlib zlib-devel  php-pecl-zip glibc glibc-devel glib2 glib2-devel bzip2 bzip2-devel curl curl-devel openssl openssl-devel openldap openldap-devel nss_ldap openldap-clients openldap-servers libmcrypt libmcrypt-devel mcrypt mhash php-mcrypt libidn libidn-devel php-mbstring libxml2 libxml2-devel php-xmlrpc php-xml php-bcmath php-gmp php-ssh2 php-pecl-libsodium  php-pthreads php-pecl-swoole 
```
- **php-cgi**：
apache执行php的中间件
- **php-fastcgi php-fpm**：
nginX执行php的代理中间件
- **pcre pcre-devel**：
PCRE正则表达式解析库
- **libjpeg libjpeg-devel libpng libpng-devel**：
JPEG、PNG操作运行库
- **gd php-gd**：
GD绘图组件
- **freetype freetype-devel**：
freetype字体组件
- **zlib zlib-devel php-pecl-zip**：
ZIP解压缩组件
- **glibc glibc-devel glib2 glib2-devel**：
GZip解压缩组件
- **bzip2 bzip2-devel**：
BZip解压缩组件
- **curl curl-devel**：
cURL HTTP下载上传组件
- **openssl openssl-devel**：
SSL组件
- **openldap openldap-devel nss_ldap openldap-clients openldap-servers**：
LDAP Windows域验证组件
- **libmcrypt libmcrypt-devel mcrypt mhash php-mcrypt php-pecl-libsodium**：
加解密、Hash组件
- **libidn libidn-devel**：
国际域名（IDN）实施字符串预处理库
- **libxml2 libxml2-devel php-xmlrpc php-xml**：
XML操作组件
- **php-mbstring**：
Unicode 多字符集操作组件
- **php-bcmath php-gmp**：
GMP BCMath 高精度大数计算组件
- **php-ssh2**：
SSH客户端组件
- **php-pthreads**：
多线程组件
- **php-pecl-swoole**：
网络通讯组件

# # 配置文件权限
> 如果不配置所有者和权限，会出现session读写错误，以及403执行权限失败

```
$ chown -R nobody:nobody /var/lib/php/session
$ chown -R nobody:nobody /www/website/
$ chmod -R +rw /var/lib/php/session
$ chmod -R +rw /www/website/
```

# # 配置 php-fpm
## ## 主配置文件
```
$ vim /etc/php-fpm.conf
```
## ## 网站参数配置文件
```
$ vim /etc/php-fpm.d/www.conf
```
```
# 修改用户、用户组
...
user = nobody
group = nobody
...
; 启动慢日志
request_slowlog_timeout = 3
...
```
## ## 操作
### 开机启动
```
$ chkconfig --level 2345 php-fpm on
# 7.0
$ systemctl enable php-fpm.service
```
### 启动
```
$ service php-fpm start
# 7.0
$ systemctl start php-fpm.service
```
### 重新读取php/php-fpm配置
```
$ service php-fpm reload
# 7.0
$ systemctl reload php-fpm.service
```
### 停止
```
$ service php-fpm stop
# 7.0
$ systemctl stop php-fpm.service
```
# # 配置PHP
## ## 主配置文件
```
$ vim /etc/php.ini
```
```ini
...
display_errors = On
...
memory_limit = 128M
...
post_max_size = 60M ;需要小于memory_limit
...
upload_max_filesize = 50M ;需要小于post_max_size
...
upload_tmp_dir = /tmp ;nobody有读写权限的目录
...
date.timezone = UTC
...
```
## ## 开启Opcache（PHP 5.5以上）
```
yum --enablerepo=remi-php70,remi install php-opcache
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
