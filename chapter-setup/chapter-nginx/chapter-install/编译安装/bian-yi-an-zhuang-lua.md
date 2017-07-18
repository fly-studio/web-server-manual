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

## nginx的lua module
```
$ cd ~/nginx
$ git clone https://github.com/openresty/lua-nginx-module.git
$ git clone https://github.com/openresty/stream-lua-nginx-module.git

```

## ## xss-nginx-module
防XSS注入
```
$ cd ~/nginx
$ git clone https://github.com/openresty/xss-nginx-module.git
```

## ## 

## ## ngx_coolkit
这个需要如下扩展
- ngx_http_auth_request_module,
- ngx_postgres (PostgreSQL) or ngx_drizzle (MySQL, Drizzle, SQLite),
- ngx_set_misc. 

```
$ cd ~/nginx
$ git clone https://github.com/FRiCKLE/ngx_coolkit.git
```



在nginx的编译配置中加
```
--with-http_auth_request_module \
--add-module=../stream-lua-nginx-module \
--add-module=../lua-nginx-module \



```