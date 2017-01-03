> :fa-cogs: 安装之前请确认安装了必须的 [Repo库](chapter-started/repo-仓库.md "Repo库")

:fa-warning: 请仔细确认你的PHP版本，并根据具体情况修改下文中的remi库
> --enablerepo=`remi` 表示安装PHP 5.4的扩展
>
> --enablerepo=`remi-php55,remi` 表示安装PHP 5.5的扩展
>
> --enablerepo=`remi-php56,remi` 表示安装PHP 5.6的扩展
>
> --enablerepo=`remi-php70,remi` 表示安装PHP 7.0的扩展
>
> 关于remi的解释，请查看「PHP 安装与配置」


**以下未声明安装位置，一律表示安装于PHP所在服务器**

# # 必要组件
## ## 安装phpize
> 需要编译的php扩展，需安装phpize来编译

```
$ yum --enablerepo=remi-php70,remi install php-pear php-devel
```
# # 加速类扩展

<div class="alert alert-info"> PHP 5.6 或以上，开启OpCache即可，详见[上一章](http://www.load-page.com/base/manual/25#h2--opcache-php-5-5--5-1 "上一章")</div>

## ## APC
> xCache 与 APC 任选其一

### 安装
```
$ pecl install apc
$ echo "extension=apc.so" > /etc/php.d/apc.ini
```
安装中的选项
> Enable internal debugging in APC [no] : <kbd>Enter</kbd>
> Enable per request file info about files used from the APC cache [no] : <kbd>Enter</kbd>
> Enable spin locks (EXPERIMENTAL) [no] : <kbd>yes</kbd> <kbd>Enter</kbd>
> Enable memory protection (EXPERIMENTAL) [no] : <kbd>yes</kbd> <kbd>Enter</kbd>
> Enable pthread mutexes (default) [no] : <kbd>yes</kbd> <kbd>Enter</kbd>
> Enable pthread read/write locks (EXPERIMENTAL) [yes] : <kbd>yes</kbd> <kbd>Enter</kbd>

### 安装成功的提示(其它pecl安装的雷同)
```
Build process completed successfully
Installing '/usr/lib64/php/modules/apc.so'
Installing '/usr/include/php/ext/apc/apc_serializer.h'
install ok: channel://pecl.php.net/APC-3.1.13
configuration option "php_ini" is not set to php.ini location
You should add "extension=apc.so" to php.ini
```
### ini的配置示例
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

### 卸载
```
$ pecl uninstall apc
$ rm -rf /etc/php.d/apc.ini
```

## ## xCache
> xCache 是国产软件，据测试其它加速软件的结果，xCache 加速最优
> xCache 同时也是缓存软件 (类似 Memcache)
> xCache 与 APC 任选其一

### 安装
```
$ yum --enablerepo=remi-php70,remi install php-xcache xcache-admin
```
### 修改配置
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

### 卸载
```
$ yum remove xcache
$ rm -rf /etc/php.d/xcache.ini
```
### Web管理配置(如果你需要)
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
### 启用缓存服务
如果想使用这个代替memcache，在[xcache]下追加如下配置
```
xcache.var_size = 256M
xcache.var_count = 4 ;同上
xcache.var_slots = 64K
xcache.var_ttl = 86400 
xcache.var_gc_interval = 300
xcache.var_maxttl = 604800 ; 程序无法指定超过这个最大值的
```

# # 缓存类扩展
## ## Memcache
> 关于Memcache的端口开放请参考「[服务器『必要组建』和『开放端口』](http://www.load-page.com/base/manual/23 "服务器『必要组建』和『开放端口』")」

### 安装
缓存服务器上安装
```
# 如果可能，执行下面语句安装memcached，基于rpmforge库
$ yum --enablerepo=rpmforge install libevent memcached
```
PHP 服务器上需安装
```
$ yum --enablerepo=remi-php70,remi install php-pecl-memcache php-pecl-memcached
```
验证是否安装成功
```
$ memcached -h
```
### 操作
#### 开机自动运行
```
$ chkconfig --level 2345 memcached on
# 7.0
$ systemctl enable memcached.service
```
#### 启动
```
$ service memcached start
# 7.0
$ systemctl start memcache.service
```
#### 关闭
```
$ service memcached stop
# 7.0
$ systemctl stop memcached.service
```
#### 卸载
缓存服务器
```
$ yum remove memcached
```
PHP服务器
```
$ yum remove php-pecl-memcache php-pecl-memcached
```
## ## Redis
> 关于Redis的端口开放请参考「[服务器『必要组建』和『开放端口』](http://www.load-page.com/base/manual/23 "服务器『必要组建』和『开放端口』")」

### 工作目录
```
$ mkdir -m 777 -p /www/database/redis/
$ chown -R redis:redis /www/database/redis/  #此句需要在安装完毕之后执行
```
### 安装
缓存服务器上安装
```
$ yum --enablerepo=remi install redis
```
PHP 服务器 需安装
```
$ yum --enablerepo=remi-php70,remi install php-redis
```
### 配置
```
$ vim /etc/redis.conf
```
```
...
daemonize yes # 后台运行模式
bind client1-lan-ip client2-lan-ip # 注释本行表示接受全部，也可以指定IP，用半角空格分隔
dir /www/database/redis/
appendonly yes
....
```
### 操作
#### 开机启动
```
$ chkconfig --level 2345 redis on
# 7.0
$ systemctl enable redis.service

```
#### 启动
```
$ service redis start
# 7.0
$ systemctl start redis.service
```
#### 停止
```
$ service redis stop
# 7.0
$ systemctl stop redis.service
```
#### 卸载
缓存服务器
```
$ yum remove redis
```
PHP 服务器
```
$ yum remove php-redis
```
# # 媒体扩展

## ## Imagick
> Imagick是一款优于GD的图片处理扩展

### 安装程序
> 这是php-imagick扩展的必要程序

```
$ yum --enablerepo=remi install ImageMagick ImageMagick-devel
```
### 安装扩展
```
$ pecl install imagick
$ echo "extension=imagick.so" > /etc/php.d/imagick.ini
```
安装中的选项
> Please provide the prefix of Imagemagick installation [autodetect] : <kbd>Enter</kbd>

### （或）源代码安装
```
$ wget https://pecl.php.net/get/imagick -O "imagick.tgz"
$ tar -xvf imagick.tgz
$ cd imagick-*
$ phpize
$ ./configure
$ make
$ make install
$ echo "extension=imagick.so" > /etc/php.d/imagick.ini
```
### 卸载
```
$ yum remove ImageMagick ImageMagick-devel
$ pecl uninstall imagick
$ rm -rf /etc/php.d/imagick.ini
```
## ## FFMpeg

### 安装程序
> 这是php-ffmpeg扩展的必要程序
> 可以通过命令行调取本程序，下文中的PHP类便是使用命令行调取程序得到的结果

### 安装扩展
> 此扩展不是必须的，下面两个项目都使用PHP实现了相关函数和方法
> https://github.com/PHP-FFMpeg/PHP-FFMpeg
> https://github.com/CodeScaleInc/ffmpeg-php
> 其中第二个项目，函数名称和本so扩展一致

##### 如果安装的ffmpeg为v0.6，则可执行下面的安装，否则参照上面！
```
$ wget http://downloads.sourceforge.net/project/ffmpeg-php/ffmpeg-php/0.6.0/ffmpeg-php-0.6.0.tbz2
$ tar -xjf ffmpeg-php-0.6.0.tbz2
$ cd ffmpeg-php-0.6.0
$ phpize
$ ./configure
```
##### PHP 5.4编译会报错
```
//修改row 311
list_entry *le; → zend_rsrc_list_entry *le;
//修改 row 346
list_entry new_le; → zend_rsrc_list_entry new_le;
//修改 row 360
hashkey_length+1, (void *)&new_le, sizeof(list_entry), → hashkey_length+1, (void *)&new_le,sizeof(zend_rsrc_list_entry),
```
##### 报错：ffmpeg_frame.c:421 : error: 'PIX_FMT_RGBA32' undeclared (first use in this function)
```
# 批量替换
$ sed -i 's/PIX_FMT_RGBA32/PIX_FMT_RGB32/g' ./ffmpeg_frame.c
```
##### 编译、配置
```
$ make
$ cp modules/ffmpeg.so /usr/lib64/php/modules
$ echo -e "extension=ffmpeg.so\n" > /etc/php.d/ffmpeg.ini
```
### 卸载
```
$ rm -rf /usr/lib64/php/modules/ffmpeg.so
$ rm -rf /etc/php.d/ffmpeg.ini
```

# # 运行、调试类扩展
## ## Taint
> Taint是一款能有效检测XSS、SQL注入的PHP扩展
> 作者是：鸟哥 Laruence(http://www.laruence.com/)

### 安装
```
$ wget -c "http://pecl.php.net/get/taint" -O "taint.tgz"
$ tar zxvf taint*
$ cd taint-*
$ phpize
$ ./configure
$ make
$ cp modules/taint.so /usr/lib64/php/modules
$ echo -e "extension=taint.so\n[taint]\ntaint.enable = On" > /etc/php.d/taint.ini
```
### 卸载
```
$ rm -rf /usr/lib64/php/modules/taint.so
$ rm -rf /etc/php.d/taint.ini
```

## ## runkit
> runkit是一款可以动态修改任意类和函数的定义的扩展
> 比如可以自定义 echo 函数

### 安装
```
$ git clone https://github.com/zenovich/runkit.git
$ cd runkit
$ pecl install package.xml
$ echo "extension=runkit.so" > /etc/php.d/runkit.ini
```
### 配置
> 详细配置请查看 http://php.net/manual/zh/runkit.configuration.php

```
extension=runkit.so
[runkit]
runkit.internal_override=1 ;是否可以修改/删除内置PHP函数,默认为0
runkit.superglobal='' ;设置全局变量，用英文逗号分隔
```
### 卸载
```
$ pecl uninstall runkit
$ rm -rf /etc/php.d/runkit.ini
```
## ## Xdebug
> Xdebug是一个PHP程序调试器，可以用来跟踪，调试和分析PHP程序的运行状况。

### 安装
```
$ git clone https://github.com/derickr/xdebug.git
$ cd xdebug
$ phpize
$ ./configure --enable-xdebug
$ cp modules/xdebug.so /usr/lib64/php/modules/
$ echo 'zend_extension="/usr/lib64/php/modules/xdebug.so"' > /etc/php.d/xdebug.ini
```
### 配置
> 详细配置请查看 http://www.xdebug.com/docs/all_settings

```
zend_extension="/usr/lib64/php/modules/xdebug.so"
[Xdebug]
xdebug.auto_trace=1 ;是否允许Xdebug跟踪函数调用，跟踪信息以文件形式存储，默认值为0
xdebug.collect_params=1 ;是否允许Xdebug跟踪函数参数，默认值为0
xdebug.collect_return=1 ;是否允许Xdebug跟踪函数返回值，默认值为0
xdebug.trace_output_dir="/www/website/xdebug/trace" ;函数调用跟踪信息输出文件目录，默认值为/tmp
xdebug.profiler_enable=1 ;打开xdebug的性能分析器，以文件形式存储，这项配置是不能以ini_set()函数配置的，默认值为0
xdebug.profiler_output_dir="/www/website/xdebug/profiler" ;性能分析文件的存放位置，默认值为/tmp
```

### 卸载
```
$ rm -rf /usr/lib64/php/modules/xdebug.so
$ rm -rf /etc/php.d/xdebug.ini
```