# # APC
> xCache 与 APC 任选其一

## 安装
```
$ pecl install apc
$ echo "extension=apc.so" > /etc/php.d/apc.ini
```
安装中的选项
> Enable internal debugging in APC [no] : <kbd>Enter</kbd>
>
> Enable per request file info about files used from the APC cache [no] : <kbd>Enter</kbd>
>
> Enable spin locks (EXPERIMENTAL) [no] : <kbd>yes</kbd> <kbd>Enter</kbd>
>
> Enable memory protection (EXPERIMENTAL) [no] : <kbd>yes</kbd> <kbd>Enter</kbd>
>
> Enable pthread mutexes (default) [no] : <kbd>yes</kbd> <kbd>Enter</kbd>
>
> Enable pthread read/write locks (EXPERIMENTAL) [yes] : <kbd>yes</kbd> <kbd>Enter</kbd>

## 安装成功的提示(其它pecl安装的雷同)
```
Build process completed successfully
Installing '/usr/lib64/php/modules/apc.so'
Installing '/usr/include/php/ext/apc/apc_serializer.h'
install ok: channel://pecl.php.net/APC-3.1.13
configuration option "php_ini" is not set to php.ini location
You should add "extension=apc.so" to php.ini
```
## ini的配置示例
> 详细请查看 (http://cn.php.net/manual/en/apc.configuration.php)

```
$ vim /etc/php.d/apc.ini
```
```
extension=apc.so
[APC]
apc.cache_by_default = On
apc.enabled = On
apc.max_file_size = 10M
apc.shm_segments = 3
apc.shm_size = 50M
apc.stat = on
apc.write_lock = On
```

## 卸载
```
$ pecl uninstall apc
$ rm -rf /etc/php.d/apc.ini
```

# # xCache
> xCache 是国产软件，据测试其它加速软件的结果，xCache 加速最优
> xCache 同时也是缓存软件 (类似 Memcache)
> xCache 与 APC 任选其一

## 安装
```
$ yum --enablerepo=remi-php71,remi install php-xcache xcache-admin
```
## 修改配置
> XCache自 3.0 版本开始不再支持使用 zend_extension 加载 xCache

```
$ vim /etc/php.d/xcache.ini
```
```
extension=xcache.so
[xcache]
xcache.shm_scheme = "mmap"
xcache.size  = 128M
xcache.count = 4 ; 与php-fpm进程数相同
xcache.slots = 64K
xcache.ttl = 3600 ; 过期时间(秒)
xcache.gc_interval = 300 ; 回收内存空间的间隔
xcache.readonly_protection = Off
xcache.mmap_path = "/dev/zero"
xcache.cacher = On
xcache.stat = On ; 自动发现检查脚本更新
```

## 卸载
```
$ yum remove xcache
$ rm -rf /etc/php.d/xcache.ini
```
## Web管理配置(如果你需要)
```
$ cp -a /usr/share/xcache/ /www/website/xcache
```
并在xcache.ini中添加如下信息即可
```
[xcache.admin]
xcache.admin.enable_auth = On
xcache.admin.user = "admin"
xcache.admin.pass = "your password's md5"
```
## 启用缓存服务
如果想使用这个代替memcache，在[xcache]下追加如下配置
```
xcache.var_size = 256M
xcache.var_count = 4 ;同上
xcache.var_slots = 64K
xcache.var_ttl = 86400 
xcache.var_gc_interval = 300
xcache.var_maxttl = 604800 ; 程序无法指定超过这个最大值的
```

