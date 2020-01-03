# Lua
如果需要安装Lua到Nginx，可以按照如下步骤
## ## LuaJIT
先安装并导出luaJit
```
$ cd ~
$ wget http://luajit.org/download/LuaJIT-2.0.5.tar.gz
$ tar zxvf LuaJIT-*.tar.gz
$ cd LuaJIT-*
$ make PREFIX=/usr
$ make install PREFIX=/usr
$ export LUAJIT_LIB=/usr/lib
$ export LUAJIT_INC=/usr/include/luajit-2.0
```

## lua module
```
$ cd ~/nginx
$ git clone https://github.com/openresty/lua-nginx-module.git
$ git clone https://github.com/openresty/stream-lua-nginx-module.git
$ git clone https://github.com/openresty/lua-upstream-nginx-module.git
```

## ## ngx_devel_kit
Nginx Development Kit
```
$ cd ~/nginx
$ git clone https://github.com/simpl/ngx_devel_kit.git
```

## ## xss-nginx-module
防XSS注入
```
$ cd ~/nginx
$ git clone https://github.com/openresty/xss-nginx-module.git
```

## ## form-input-nginx-module
```
$ cd ~/nginx
$ git clone https://github.com/calio/form-input-nginx-module.git
```

## ## encrypted-session-nginx-module
```
$ cd ~/nginx
$ git clone https://github.com/openresty/encrypted-session-nginx-module.git
```

## ## srcache-nginx-module
```
$ cd ~/nginx
$ git clone https://github.com/FRiCKLE/ngx_coolkit.git
```

## ## headers-more-nginx-module
```
$ cd ~/nginx
$ git clone https://github.com/openresty/headers-more-nginx-module.git
```

## ## array-var-nginx-module
```
$ cd ~/nginx
$ git clone https://github.com/openresty/array-var-nginx-module.git
```

## ## memc-nginx-module
```
$ cd ~/nginx
$ git clone https://github.com/openresty/memc-nginx-module.git
```

## ## redis2-nginx-module
```
$ cd ~/nginx
$ git clone https://github.com/openresty/redis2-nginx-module.git
```

## ## redis-nginx-module (可选)
```
$ cd ~/nginx
$ wget https://people.freebsd.org/~osa/ngx_http_redis-0.3.8.tar.gz
$ tar zxvf ngx_http_redis-*.tar.gz
```

## ## srcache-nginx-module
```
$ cd ~/nginx
$ git clone https://github.com/openresty/srcache-nginx-module.git
```

## ## set-misc-nginx-module
```
$ cd ~/nginx
$ git clone https://github.com/openresty/set-misc-nginx-module.git
```

## ## ngx_coolkit
这个需要如下扩展
- ngx_http_auth_request_module,
- ngx_postgres (PostgreSQL) or ngx_drizzle (MySQL, Drizzle, SQLite),
- ngx_set_misc. 

```
$ cd ~/nginx
$ git clone https://github.com/FRiCKLE/ngx_coolkit.git
```


## 在nginx的编译配置中加
```
--with-http_auth_request_module \
--add-module=../ngx_devel_kit \
--add-module=../stream-lua-nginx-module \
--add-module=../lua-nginx-module \
--add-module=../lua-upstream-nginx-module \
--add-module=../xss-nginx-module \
--add-module=../ngx_coolkit \
--add-module=../set-misc-nginx-module \
--add-module=../memc-nginx-module \
--add-module=../form-input-nginx-module \
--add-module=../encrypted-session-nginx-module \
--add-module=../headers-more-nginx-module \
--add-module=../array-var-nginx-module \
--add-module=../redis2-nginx-module \
--add-module=../srcache-nginx-module \
```

## 编译中需要注意
1. `--with-stream` 不能是动态
2. `--with-http_perl_module` 与 `stream-lua-nginx-module` 有冲突，需要去掉
3. `openssl` 需要 v1.0.2
4. `LD`，`CC`的参数如下
```
--with-cc-opt='-O2 -I/usr/include -I/usr/include/openssl' \
--with-ld-opt='-Wl,-rpath,/usr/lib'
```
5. 如果nginx的版本高于1.10，需要修改
`stream-lua-nginx-module/src/ngx_stream_lua_util.c`
```
将
ngx_stream_close_connection(s->connection); 
替换为
ngx_stream_finalize_session(s, NGX_STREAM_INTERNAL_SERVER_ERROR);
```

完整编译命令行是
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
--with-mail=dynamic \
--with-mail_ssl_module \
--with-pcre \
--with-pcre-jit \
--with-threads \
--with-stream \
--with-stream_ssl_module \
--with-debug \
--with-cc-opt='-O2 -I/usr/include -I/usr/include/openssl' \
--with-ld-opt='-Wl,-rpath,/usr/lib' \
--with-openssl=~/openssl-1.0.2l \
--add-module=../ngx_devel_kit \
--add-module=../nginx-ct-1.3.2 \
--add-module=../ngx_cache_purge \
--add-module=../nginx-http-concat \
--add-module=../echo-nginx-module \
--add-module=../xss-nginx-module \
--add-module=../ngx_coolkit \
--add-module=../set-misc-nginx-module \
--add-module=../memc-nginx-module \
--add-module=../form-input-nginx-module \
--add-module=../encrypted-session-nginx-module \
--add-module=../headers-more-nginx-module \
--add-module=../array-var-nginx-module \
--add-module=../redis2-nginx-module \
--add-module=../srcache-nginx-module \
--add-module=../lua-nginx-module \
--add-module=../lua-upstream-nginx-module \
--add-module=../stream-lua-nginx-module 
```

