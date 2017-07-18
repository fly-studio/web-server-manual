## ## Lua(选装)
先安装luaJit
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
再下载nginx的lua module
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
```
$ cd ~/nginx
$ git clone https://github.com/FRiCKLE/ngx_coolkit.git
```



```
--with-http_auth_request_module \
--add-module=../stream-lua-nginx-module \
--add-module=../lua-nginx-module \



```