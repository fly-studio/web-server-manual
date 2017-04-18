# vHOST的建立方式
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
	# 如果使用L+ 需要禁止这些文件的访问
	location ~ /(\.|artisan$|config|resources|storage) {
	    deny all;
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

# 子目录支持RESTful
比如： http://127.0.0.1/dir_name/

> L+ 框架中，已有.htaccess文件，这个文件支持apache的重写URL，但是nginX中需要做如下配置，如果不加入这个配置，则只能通过 **http://127.0.0.1/dir_name/index.php/controller/action** 这种类似的方式访问页面

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
