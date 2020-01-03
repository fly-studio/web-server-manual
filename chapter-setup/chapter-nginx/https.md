# # 证书
## ## 免费的个人证书
### 腾讯
赛门铁克DV证书，每个域名都需要单独申请证书
```
https://console.qcloud.com/ssl
```
### Let's Encrypt
需要自己生成，有效期三个月 支持多域名
> 参考 https://imququ.com/post/letsencrypt-certificate.html
> 或者
> https://github.com/kaienkira/acme-client  PHP
> https://github.com/diafygi/acme-tiny Python

下载acme生成客户端
```
$ mkdir /www/cert/
$ wget https://raw.githubusercontent.com/kaienkira/acme-client/master/acme-client.php
$ mkdir /www/cert/acme-challenge
```
生成账号 私钥
```
$ openssl genrsa -out account.key 4096
```
生成域名 私钥
```
$ openssl genrsa -out domain.key 4096
```
新建sh文件，可随时生成新的证书(记得```chmod +x```)
```
$ vim getcert.sh
```
比如绑定三个域名：domain.com www.domain.com xxx.domain.com
```
#!/bin/bash

openssl req -new -sha256 -key /www/cert/domain.key -subj "/" -reqexts SAN -config <(cat /usr/local/ssl/openssl.cnf <(printf "[SAN]\nsubjectAltName=DNS:domain.com,DNS:www.domain.com,DNS:xxx.domain.com")) -out /www/cert/domain.csr
php /www/cert/acme-client.php \
    -a /www/cert/account.key \
    -r /www/cert/domain.csr \
    -d "domain.com;www.domain.com;xxx.domain.com" \
    -c /www/cert/acme-challenge \
    -o /www/cert/domain.crt.new

if [ $? -ne 0 ]
then
    exit 1
fi

cp /www/cert/domain.crt.new \
   /www/cert/domain.crt

rm -f -- /www/cert/acme-challenge/*
service nginx reload
```
Let's Encrypt会请求 http://domain.com/.well-known/acme-challenge/ 下的文件确保你对域名有控制权，所以你需要在你nginx中加上
```
location ^~ /.well-known/acme-challenge/ {
    alias /www/cert/acme-challenge/;
    try_files $uri =404;
}
```
比如上例均会访问：所以，所对应的nginx的server均需要加上上面的语句
```
http://domain.com/.well-known/acme-challenge/
http://www.domain.com/.well-known/acme-challenge/
http://xxx.domain.com/.well-known/acme-challenge/
```
做一个定时任务，1个月生成一个新证书
```
$ crontab -e
```
```
0 0 * * 1 /bin/bash /www/cert/getcert.sh
```


### StartSSL
可一次注册10个域名，Chrome、Mozilla已经不再信任其颁发的DV证书
```
https://www.startssl.com/
```
### WoSign
Chrome已经不再信任其颁发的DV证书
```
http://freessl.wosign.com/freessl
```
## ## 其它便宜证书
详情查看
```
https://www.zhihu.com/question/19578422
```

# # nginX配置
## ## 腾讯DV证书
腾讯发布的证书文件，2个。上传到服务器的某个目录下，比如：```/www/cert/```
```
1_www.domain.com_cert.crt
2_www.domain.com.key
```
修改nginx
```
$ vim /etc/nginx/conf.d/default.conf
```
注意添加的东西，其它的内容不变
```
server {
    listen      443 default_server ssl; # 监听 SSL 端口
    server_name www.domain.com;
	# 加入SSL配置
    ssl on;
    ssl_certificate /www/cert/1_www.domain.com_cert.crt;
    ssl_certificate_key /www/cert/2_www.domain.com.key;
    ssl_session_timeout 5m;
    ssl_protocols TLSv1;
    ssl_ciphers  HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers   on;

    # 其它内容照旧
    location / {
		...
	}
	...
}
```

## ## 推荐配置
Mozilla SSL Configuration Generator
https://mozilla.github.io/server-side-tls/ssl-config-generator/

# # 最优配置
## ## 1. 只允许使用当前可靠的SSL协议
```
ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
```
## ## 2. 使用安全的加密算法
上面的ssl_ciphers可以替换为下文，! 表示不信任
```
ssl_ciphers 'EECDH+CHACHA20:EECDH+CHACHA20-draft:EECDH+AES128:RSA+AES128:EECDH+AES256:RSA+AES256:EECDH+3DES:RSA+3DES:!MD5';
```
## ## 3. 优先使用服务器提供的加密算法
添加nginX配置
```
ssl_prefer_server_ciphers on;
```
## ## 4. 配置强度更大的DHE(Ephemeral Diffie-Hellman，一次性迪菲－赫尔曼密钥)参数
先生成一个
```
$ openssl dhparam -out dhparam2048.pem 2048
```
添加nginX配置
```
ssl_dhparam /path/to/dhparam2048.pem;
```
## ## 5. 生成session_ticket.key
先生成一个
```
$ openssl rand 48 > session_ticket.key
```
添加nginX配置
```
ssl_session_tickets        on;
ssl_session_ticket_key     /path/to/session_ticket.key;
```
> 配置相同的session ticket key，部署在多个服务器上，这样多个不同的服务器也能产生相同的 session ticket。session ticket的缺点是支持率不广，大概只有40%。而session id是client hello的标准内容，从SSL2.0开始就被全部客户支持。

## ## 6. 启用HSTS
也就是限制网站只能通过HTTPS访问，这是降低中间人攻击的主要办法。
这一点只适合支持全站使用HTTPS访问的站点。
在nginx中是通过添加下面的HTTPS报头来提示浏览器作出该动作：
```
add_header Strict-Transport-Security max-age=15768000;
```
同时 跳转http到https
```
server {
    listen 80;
    add_header Strict-Transport-Security max-age=15768000;
    return 301 https://$server_name$request_uri;
}
```
> 服务器端配置HSTS，减少302跳转，其实HSTS的最大作用是防止302 HTTP劫持。HSTS的缺点是浏览器支持率不高，另外配置HSTS后HTTPS很难实时降级成HTTP。

## ## 7. 更多加速
如果有条件的话可以启用
```
# tcp fast open;
listen 443 ssl fastopen=3 reuseport;
```
> kernel 3 才支持tcp fast open; 查看 https://kn007.net/topics/centos-6-supports-tcp-and-tcp-fastopen-so-reuseport/

## ## 8. 启用SPDY/HTTP2
SPDY是强制使用HTTPS的，协议比较复杂，需要单独的文章来分析。可以肯定的一点是使用SPDY的请求不仅明显提升了HTTPS速度，甚至比HTTP还要快。在无线WIFI环境下，SPDY比HTTP要快50ms左右，3G环境下比HTTP要快250ms。
```
listen 443 ssl spdy;
```
nginx 1.9.5以上版本替换spdy为http2
```
listen 443 ssl http2 fastopen=3 reuseport;
```

## ## SSL测试
通过 ssllabs.com 提供的服务器和客户端测试来检测网站的SSL安全性
A 为最高

## ## nginx跳转设置
```
# domain.com 跳转为 https://www.domain.com
server {
        listen       80;
        listen       443 ssl;
        server_name  "~^([^.]*\.[^.]*)$";
        return       302 https://www.$host$request_uri;
}
# http://www.domain.com 跳转为 https://www.domain.com
server {
        listen 80;
        server_name "~^(www\.).*";
        return       301 https://$host$request_uri;
}
```

# # 帮助
## ## PHP 中仍然无法检测到 https
> 比如Laravel对https的识别算法是```$_SERVER['HTTPS'] == 'on'```，但是通用的nginx配置，并没有传递```HTTPS```到```php-fpm```

**修改方法：**
所有php的地方，添加2个配置，比如：
```
location ~ \.php$ {
    root           /www/website;
    fastcgi_pass   127.0.0.1:9000;
    fastcgi_index  index.php;
    fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
    include        fastcgi_params;
    # 添加这2个配置
    fastcgi_param   HTTPS               on;
    fastcgi_param   HTTP_SCHEME         https;
}

```
## ## 开启http2仍然无效
yum安装的nignx使用的是```OpenSSL 1.0.1e-fips 11 Feb 2013```，而 OpenSSL 的这个版本不支持 ALPN，所以无法开启 HTTP/2。

> Note that accepting HTTP/2 connections over TLS requires the “Application-Layer Protocol Negotiation” (ALPN) TLS extension support, which is available only since OpenSSL version 1.0.2. Using the “Next Protocol Negotiation” (NPN) TLS extension for this purpose (available since OpenSSL version 1.0.1) is not guaranteed.

在CentOS下只能编译使用，参见：[「编译nginx」](/base/manual/24#h2---0-1 "「编译nginx」")

## ## Nginx开启SSL后上传大文件 413
笔者已经在 http 区块设置了 ```client_max_body_size  50M;```
但是在包含SSL的server下失效，变成了默认的`1m`
> http://stackoverflow.com/a/35794955/5289743

```
# 将client_max_body_size 放在 ssl 段之前
server {
	client_max_body_size  50M;
	....
	ssl on;
}
```