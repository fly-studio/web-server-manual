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