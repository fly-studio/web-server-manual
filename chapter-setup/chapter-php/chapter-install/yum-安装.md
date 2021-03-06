# Yum 安装

```
$ yum --enablerepo=remi-php74,remi install php pcre pcre-devel php-fpm gd php-gd libjpeg libjpeg-devel libpng libpng-devel freetype freetype-devel zlib zlib-devel  php-pecl-zip glibc glibc-devel glib2 glib2-devel bzip2 bzip2-devel curl curl-devel openssl openssl-devel openldap openldap-devel nss_ldap openldap-clients openldap-servers libmcrypt libmcrypt-devel mcrypt mhash php-pecl-mcrypt libidn libidn-devel php-mbstring libxml2 libxml2-devel php-xmlrpc php-xml php-bcmath php-gmp php-ssh2 php-pecl-swoole4

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
- **libmcrypt libmcrypt-devel mcrypt mhash php-pecl-mcrypt**：
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
- **php-pecl-swoole4**：
网络通讯组件

## 注意

PHP 7.3 以及之后无法安装这两个扩展

```
php-pecl-libsodium php-pthreads
```
