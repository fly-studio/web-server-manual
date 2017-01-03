> 安装之前请确认安装了必须的 [Repo库](chapter-started/repo-仓库.md "Repo库")

# # 安装nginX

## ## yum安装方式
配置官方源
```
$ vim /etc/yum.repos.d/nginx.repo
```
内容
```
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=0
enabled=1
```
安装
```
$ yum install nginx
```

## ## 编译安装方式
> https://imququ.com/post/my-nginx-conf.html

### ### openSSL 1.1.0
如果条件不允许，请参照下文openSSL 1.0.2

1.1.0c [下载地址](https://www.openssl.org/source/openssl-1.1.0-latest.tar.gz "下载地址")
```
$ wget https://www.openssl.org/source/openssl-1.1.0-latest.tar.gz
$ tar -zxf openssl-*-latest.tar.gz
$ cd openssl-*
# 安装
$ ./config
$ make
$ make test
$ make install
$ mv /usr/bin/openssl /usr/bin/openssl-v1.0.1
$ ln -s /usr/local/lib64/libssl.so.1.1 /usr/lib64/libssl.so.1.1
$ ln -s /usr/local/lib64/libcrypto.so.1.1 /usr/lib64/libcrypto.so.1.1
$ ln -s /usr/local/bin/openssl /usr/bin/openssl
```
### ### 安装openSSL 1.0.2()/
- 1.0.2需要打ChaCha20/Poly1305的补丁，对手机支持友好  [cloudflare补丁](https://github.com/travislee8964/sslconfig/tree/master/patches "cloudflare补丁")
- 由于默认 gcc 版本为 4.4.7，在添加 Chacha 20 补丁之后，编译会报错：[#11](https://github.com/cloudflare/sslconfig/issues/11 "#11")，需要将 gcc 升级到 4.8 以上版本：

```
$ wget https://www.openssl.org/source/openssl-1.0.2-latest.tar.gz
$ tar -zxf openssl-*-latest.tar.gz
$ cd openssl-*
# openssl-1.0.2j 打补丁
$ wget https://raw.githubusercontent.com/travislee8964/sslconfig/master/patches/openssl__chacha20_poly1305_draft_and_rfc_ossl102i.patch
$ patch -p1 < ./openssl__chacha20_poly1305_draft_and_rfc_ossl102i.patch 
# 安装
$ ./config
$ make
$ make test
$ make install
$ mv /usr/bin/openssl /usr/bin/openssl-v1.0.1
$ ln -s /usr/local/lib64/libssl.so.1.1 /usr/lib64/libssl.so.1.1
$ ln -s /usr/local/lib64/libcrypto.so.1.1 /usr/lib64/libcrypto.so.1.1
$ ln -s /usr/local/bin/openssl /usr/bin/openssl
```

### ### brotli插件
> accept-encoding:gzip, deflate, sdch, br
> br 是一个加密模块

编译libbrotli
```
$ cd ~
$ yum install libtool
$ git clone https://github.com/bagder/libbrotli.git
$ cd libbrotli
$ ./autogen.sh
$ ./configure
$ make
$ make install
```
下载 ngx_brotli
```
$ cd ~
$ git clone https://github.com/google/ngx_brotli.git
$ vim ngx_brotli/config
```
顶部加一行
```
have=NGX_HTTP_HEADERS . auto/have
```
### ### nginx-ct（需要 openssl 1.1.0）
> 支持多个证书
下载 nginx-ct

```
$ cd ~
$ wget -O nginx-ct.zip -c https://github.com/grahamedgecombe/nginx-ct/archive/v1.3.1.zip
$ unzip nginx-ct.zip
```

### ### ngx_cache_purge
> 清除proxy_cache

```
$ cd ~
$ git clone https://github.com/FRiCKLE/ngx_cache_purge.git
```

### ### nginx-http-concat
> js/css文件合并成一个请求

```
$ cd ~
git clone git://github.com/alibaba/nginx-http-concat.git
```
### ### echo-nginx-module
调试最方便了
```
git clone https://github.com/openresty/echo-nginx-module.git
```

### ### 下载源代码
```
$ cd ~
$ wget http://nginx.org/download/nginx-1.11.6.tar.gz
$ tar -zxf nginx-*.tar.gz
$ cd nginx-*/
```

### ### 必要的运行库
```
$ yum install zlib-devel gd gd-devel libxml2 libxml2-devel libxslt-devel perl-devel perl-ExtUtils-Embed pcre pcre-devel GeoIP GeoIP-devel GeoIP-data 
```
### ### 添加用户
```
$ groupadd nginx
$ useradd -g nginx -s /sbin/nologin -d /dev/null nginx
```
### ### 修改openssl 路径
```
$ vim auto/lib/openssl/conf
```
```
# CORE_INCS="$CORE_INCS $OPENSSL/.openssl/include"  
# CORE_DEPS="$CORE_DEPS $OPENSSL/.openssl/include/openssl/ssl.h"  
# CORE_LIBS="$CORE_LIBS $OPENSSL/.openssl/lib/libssl.a"  
# CORE_LIBS="$CORE_LIBS $OPENSSL/.openssl/lib/libcrypto.a"  
# 修改成下面样子  
CORE_INCS="$CORE_INCS $OPENSSL/include"  
CORE_DEPS="$CORE_DEPS $OPENSSL/include/openssl/ssl.h"  
CORE_LIBS="$CORE_LIBS $OPENSSL/libssl.a"  
CORE_LIBS="$CORE_LIBS $OPENSSL/libcrypto.a"  
```
### ### 官方安装配置
> --add-module=./nginx-ct-1.3.1 需要 openssl 1.1.0，可以不用nginx-ct

```
$ ./configure --prefix=/etc/nginx \
--sbin-path=/usr/sbin/nginx \
--modules-path=/usr/lib64/nginx/modules \
--conf-path=/etc/nginx/nginx.conf \
--error-log-path=/var/log/nginx/error.log \
--http-log-path=/var/log/nginx/access.log \
--http-client-body-temp-path=/var/lib/nginx/tmp/client_body \
--http-proxy-temp-path=/var/lib/nginx/tmp/proxy \
--http-fastcgi-temp-path=/var/lib/nginx/tmp/fastcgi \
--http-uwsgi-temp-path=/var/lib/nginx/tmp/uwsgi \
--http-scgi-temp-path=/var/lib/nginx/tmp/scgi \
--pid-path=/var/run/nginx.pid \
--lock-path=/var/lock/subsys/nginx \
--user=nginx \
--group=nginx \
--with-file-aio \
--with-http_ssl_module \
--with-http_v2_module \
--with-http_realip_module \
--with-http_addition_module \
--with-http_xslt_module=dynamic \
--with-http_image_filter_module=dynamic \
--with-http_geoip_module=dynamic \
--with-http_sub_module \
--with-http_dav_module \
--with-http_flv_module \
--with-http_mp4_module \
--with-http_gunzip_module \
--with-http_gzip_static_module \
--with-http_random_index_module \
--with-http_secure_link_module \
--with-http_degradation_module \
--with-http_auth_request_module \
--with-http_slice_module \
--with-http_stub_status_module \
--with-http_perl_module=dynamic \
--with-mail=dynamic \
--with-mail_ssl_module \
--with-pcre \
--with-pcre-jit \
--with-threads \
--with-stream=dynamic \
--with-stream_ssl_module \
--with-debug \
--with-cc-opt='-O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector --param=ssp-buffer-size=4 -m64 -mtune=generic -Wno-deprecated-declarations' \
--with-ld-opt=' -Wl,-E' \
--with-openssl=../openssl-1.1.0c \
--add-module=../nginx-ct-1.3.1 \
--add-module=../ngx_cache_purge \
--add-module=../nginx-http-concat \
--add-module=../echo-nginx-module \
--add-module=../ngx_brotli


make && make install
```
> -Wno-deprecated-declarations 是为了让ngx_brotli编译不报错，是暂时性的配置，因为该软件中压缩相关 API 正在修改。

### 安装服务(仅CentOS 6)
因为编译安装的nginX是没有安装到服务，安装这个就可以使用服务了
```
$ cd ~
$ git clone https://github.com/Fleshgrinder/nginx-sysvinit-script.git
$ cd nginx-sysvinit-script
$ make
```
支持如下命令
```
service nginx force-reload
service nginx reload
service nginx restart
service nginx try-restart
service nginx start
service nginx status
service nginx stop
```
如果nginx-sysvinit-script无法安装
> https://www.nginx.com/resources/wiki/start/topics/examples/redhatnginxinit/

```
$ vim /etc/init.d/nginx
```
```
#!/bin/sh
#
# nginx - this script starts and stops the nginx daemon
#
# chkconfig:   - 85 15
# description:  NGINX is an HTTP(S) server, HTTP(S) reverse \
#               proxy and IMAP/POP3 proxy server
# processname: nginx
# config:      /etc/nginx/nginx.conf
# config:      /etc/sysconfig/nginx
# pidfile:     /var/run/nginx.pid

# Source function library.
. /etc/rc.d/init.d/functions

# Source networking configuration.
. /etc/sysconfig/network

# Check that networking is up.
[ "$NETWORKING" = "no" ] && exit 0

nginx="/usr/sbin/nginx"
prog=$(basename $nginx)

NGINX_CONF_FILE="/etc/nginx/nginx.conf"

[ -f /etc/sysconfig/nginx ] && . /etc/sysconfig/nginx

lockfile=/var/lock/subsys/nginx

make_dirs() {
   # make required directories
   user=`$nginx -V 2>&1 | grep "configure arguments:.*--user=" | sed 's/[^*]*--user=\([^ ]*\).*/\1/g' -`
   if [ -n "$user" ]; then
      if [ -z "`grep $user /etc/passwd`" ]; then
         useradd -M -s /bin/nologin $user
      fi
      options=`$nginx -V 2>&1 | grep 'configure arguments:'`
      for opt in $options; do
          if [ `echo $opt | grep '.*-temp-path'` ]; then
              value=`echo $opt | cut -d "=" -f 2`
              if [ ! -d "$value" ]; then
                  # echo "creating" $value
                  mkdir -p $value && chown -R $user $value
              fi
          fi
       done
    fi
}

start() {
    [ -x $nginx ] || exit 5
    [ -f $NGINX_CONF_FILE ] || exit 6
    make_dirs
    echo -n $"Starting $prog: "
    daemon $nginx -c $NGINX_CONF_FILE
    retval=$?
    echo
    [ $retval -eq 0 ] && touch $lockfile
    return $retval
}

stop() {
    echo -n $"Stopping $prog: "
    killproc $prog -QUIT
    retval=$?
    echo
    [ $retval -eq 0 ] && rm -f $lockfile
    return $retval
}

restart() {
    configtest || return $?
    stop
    sleep 1
    start
}

reload() {
    configtest || return $?
    echo -n $"Reloading $prog: "
    killproc $nginx -HUP
    RETVAL=$?
    echo
}

force_reload() {
    restart
}

configtest() {
  $nginx -t -c $NGINX_CONF_FILE
}

rh_status() {
    status $prog
}

rh_status_q() {
    rh_status >/dev/null 2>&1
}

case "$1" in
    start)
        rh_status_q && exit 0
        $1
        ;;
    stop)
        rh_status_q || exit 0
        $1
        ;;
    restart|configtest)
        $1
        ;;
    reload)
        rh_status_q || exit 7
        $1
        ;;
    force-reload)
        force_reload
        ;;
    status)
        rh_status
        ;;
    condrestart|try-restart)
        rh_status_q || exit 0
            ;;
    *)
        echo $"Usage: $0 {start|stop|status|restart|condrestart|try-restart|reload|force-reload|configtest}"
        exit 2
esac
```
### 安装服务(仅CentOS 7)
```
$ vim /usr/lib/systemd/system/nginx.service
```
```
[Unit]
Description=nginx - high performance web server
Documentation=http://nginx.org/en/docs/
After=network.target remote-fs.target nss-lookup.target
  
[Service]
Type=forking
PIDFile=/run/nginx.pid
ExecStartPre=/usr/sbin/nginx -t -c /etc/nginx/nginx.conf
ExecStart=/usr/sbin/nginx -c /etc/nginx/nginx.conf
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s QUIT $MAINPID
PrivateTmp=true
  
[Install]
WantedBy=multi-user.target
```
重启systemctl
```
$ chmod +x /usr/lib/systemd/system/nginx.service
$ systemctl daemon-reload
$ systemctl daemon-reexec
```

# # 配置
## 1. 创建服务(开机运行)
```
$ chkconfig --level 2345 nginx on
# 7.0
$ systemctl enable nginx.service
```
## 2. 建立工作文件夹
```
＄ mkdir -m 777 -p /www/website
＄ chown -R nobody:nobody /www/website
```
## 3. 默认全局配置
```
$ vim /etc/nginx/nginx.conf
```
#### CentOS 6.5
```
# For more information on configuration, see:
#   * Official English Documentation: http://nginx.org/en/docs/
#   * Official Russian Documentation: http://nginx.org/ru/docs/

user              nginx;
worker_processes  10;

error_log  /var/log/nginx/error.log;
#error_log  /var/log/nginx/error.log  notice;
#error_log  /var/log/nginx/error.log  info;

pid        /var/run/nginx.pid;

events {
    use epoll;
    worker_connections  65535;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for" '
                      '"$gzip_ratio" $request_time $bytes_sent $request_length';

    access_log  /var/log/nginx/access.log  main;

    fastcgi_intercept_errors on;

    ssi             on;
    sendfile        on;
    tcp_nopush      on;
    tcp_nodelay     on;

    keepalive_timeout  65;

    gzip  on;
    gzip_vary          on;
    gzip_types   text/plain application/xml text/javascript application/x-javascript text/css;
    gzip_disable "MSIE [1-6]\.(?!.*SV1)";

    client_max_body_size  2048m;

    # 以下三行配置不得当，会导致响应非常缓慢
    #proxy_buffer_size 64k;
    #proxy_buffers 4 64k;
    #proxy_busy_buffers_size 128k;

    # Load config files from the /etc/nginx/conf.d directory
    # The default server is in conf.d/default.conf
    include /etc/nginx/conf.d/*.conf;
}
```

```
$ vim /etc/nginx/conf.d/default.conf
```
```
#修改如下配置
...
root /www/website/;
index  index.html index.htm index.php;

...
# 注释如下配置
...
#error_page  404              /404.html;
#location = /404.html {
#	root   /usr/share/nginx/html;
#}
#error_page   500 502 503 504  /50x.html;
#location = /50x.html {
#	root   /usr/share/nginx/html;
#}

...
```
#### CentOS 7
```
# For more information on configuration, see:
#   * Official English Documentation: http://nginx.org/en/docs/
#   * Official Russian Documentation: http://nginx.org/ru/docs/

user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;

events {
    use epoll;
    worker_connections 65535;
}

http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for" '
                      '"$gzip_ratio" $request_time $bytes_sent $request_length';

    access_log  /var/log/nginx/access.log  main;

    ssi                 on;
    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    types_hash_max_size 2048;

    gzip  on;
    gzip_types   text/plain application/xml text/javascript application/x-javascript text/css;
    gzip_disable "MSIE [1-6]\.(?!.*SV1)";

    client_max_body_size  2048m;

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;

    # Load modular configuration files from the /etc/nginx/conf.d directory.
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    # for more information.
    include /etc/nginx/conf.d/*.conf;

    server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  _;
        root         /www/website;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        location / {
            index index.html index.htm index.php;
        }

       #error_page 404 /404.html;
       #     location = /40x.html {
       #}

       #error_page 500 502 503 504 /50x.html;
       #    location = /50x.html {
       #}
    }
}
```
CentOS 7将配置分为了两块
```
/etc/nginx/conf.d 放置 vHosts的内容
/etc/nginx/default.d 放置 location 之类的信息
```

## 4. 网站配置
进入默认配置文件夹
```
$ cd /etc/nginx/default.d
```
新建php.conf
```
$ vim php.conf
```
```
# 普通PHP
location ~ \.php$ {
	root           /www/website;
	fastcgi_pass   127.0.0.1:9000;
	fastcgi_index  index.php;
	fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
	fastcgi_param HTTP_PROXY "";
	include        fastcgi_params;
}
# RESTful的php，比如/index.php/path/to
location ~ .+\.php($|/) {
	set $script $uri;
	set $path_info "/";
	if ($uri ~ "^(.+\.php)(/.+)") {
		set $script	$1;
		set $path_info	$2;
	}

	fastcgi_pass 127.0.0.1:9000;
	fastcgi_index index.php?IF_REWRITE=1;
	include fastcgi_params;
	fastcgi_param PATH_INFO $path_info;
	fastcgi_param SCRIPT_FILENAME $document_root/$script;
	fastcgi_param SCRIPT_NAME $script;
	fastcgi_param HTTP_PROXY "";
}
```

新建status.conf (可选)
```
$ vim status.conf
```
```
location /status
{
	stub_status on;
	access_log off;

	#allow 127.0.0.1;
	#deny all
}
```

#### 屏蔽文件夹
如果使用L+ 
CentOS 6在```/etc/nginx/conf.d/default.conf```中的 location / {...} 之前添加
CentOS 7新建一个 ```/etc/nginx/default.d/deny-l+.conf```
```
location ~ /((config/|resources/).*?\.(php|tpl)|artisan|\.htaccess|\.env)$ {
      return 405;
}
location ~ ^/(kohana|laravel)/ {
      return 405;
}
```
## 5. vHOST的建立方式
比如：新建www.domain.com的vHOST
```
$ vim /etc/nginx/conf.d/www.domain.com.conf
```
```
server {
	# 端口 80
	listen	80;
	# 匹配哪些域名，支持泛域名
	server_name domain.com www.domain.com *.domain.com;
	# 根目录
	root /www/website/path/to/project_name/;
	# 如果使用L+ 需要禁止这些文件的访问（不使用则不用）
	location ~ /((config/|resources/).*?\.(php|tpl)|artisan|\.htaccess|\.env)$ {
		return 405;
	}
	#访问配置
	location / {
		index index.php;
		rewrite ^/plugins/(.*) /static/plugins/$1 last;
		if (!-e $request_filename) {
			# 如果使用L+ 需要重指向公用静态文件
			rewrite  ^/static/(?!common/)(.*)$  /static/common/$1  last;
			# RESTful 必须
			rewrite ^/(.*)$ /index.php/$1 last;
			break;
		}
	}
	
	# 解析PHP
	location ~ .+\.php($|/) {
		set $script $uri;
		set $path_info "/";
		if ($uri ~ "^(.+\.php)(/.+)") {
			set $script	$1;
			set $path_info	$2;
		}

		fastcgi_pass 127.0.0.1:9000;
		fastcgi_index index.php?IF_REWRITE=1;
		include fastcgi_params;
		fastcgi_param PATH_INFO $path_info;
		fastcgi_param SCRIPT_FILENAME $document_root/$script;
		fastcgi_param SCRIPT_NAME $script;
		fastcgi_param HTTP_PROXY "";
	}
}
```

## 6. 子目录支持RESTful
比如： http://127.0.0.1/dir_name/

> L+ 框架中，已有.htaccess文件，这个文件支持apache的重写URL，但是nginX中需要做如下配置，如果不加入这个配置，则只能通过 **http://127.0.0.1/dir_name/index.php/controller/action** ，这种类似的方式访问页面

修改主配置文件加入子目录配置
```
$ vim /etc/nginx/conf.d/default.conf
```
```
server {
	...
	...
	# 末尾加入
	include /etc/nginx/conf.d/alias/alias.conf;
}
```
新建子目录配置
```
$ mkdir -m 777 -p /etc/nginx/alias.d
$ vim /etc/nginx/default.d/alias.conf
```
```
# pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
location ~ .+\.php($|/) {
	root   /www/website/;
	#set $uri $fastcgi_script_name;
	set $script    $uri;
	set $path_info  "/";
	if ($uri ~ "^(.+\.php)(/.+)") {
		set $script     $1;
		set $path_info  $2;
	}
	fastcgi_pass 127.0.0.1:9000;
	fastcgi_index  index.php?IF_REWRITE=1;
	include fastcgi_params;
	fastcgi_param PATH_INFO $path_info;
	fastcgi_param SCRIPT_FILENAME  $document_root$script;
	fastcgi_param SCRIPT_NAME $script;
	fastcgi_param HTTP_PROXY "";
}
include /etc/nginx/alias.d/*.conf;
```
比如：新建一个dir_name的子目录配置
```
$ vim /etc/nginx/alias.d/dir_name.conf
```
```
location /dir_name/ {
	root   /www/website/;
	index  index.html index.htm index.php;
	
	rewrite ^/dir_name/plugins/(.*) /dir_name/static/plugins/$1 last;
	if (!-e $request_filename) {
		# 本条同上，L+ 必须
		rewrite  ^/dir_name/static/(?!common/)(.*)$  /dir_name/static/common/$1  last;
		# RESTful
		rewrite  ^/dir_name/(.*)$ /dir_name/index.php/$1  last;
		break;
	}
}
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


# # 操作
## ## 启动
```
$ service nginx start
# 7.0
$ systemctl start nginx.service
```
## ## 重启
```
$ service nginx restart
# 7.0
$ systemctl restart nginx.service
```
## ## 停止
```
$ service nginx stop
# 7.0
$ systemctl stop nginx.service
```
## ## 重新加载配置
```
$ service nginx reload
# 7.0
$ systemctl reload nginx.service
```
## ## 卸载
```
$ yum remove nginx
$ rm -rf /etc/nginx/
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