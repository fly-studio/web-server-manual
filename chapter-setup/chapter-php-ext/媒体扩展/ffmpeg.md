# # FFMpeg

## 安装程序

> 这是php-ffmpeg扩展的必要程序
> 可以通过命令行调取本程序，下文中的PHP类便是使用命令行调取程序得到的结果

CentOS 6 
 
```
$ wget http://li.nux.ro/download/nux/dextop/el6/x86_64/nux-dextop-release-0-2.el6.nux.noarch.rpm
$ rpm --import http://li.nux.ro/download/nux/RPM-GPG-KEY-nux.ro
$ rpm -Uvh nux-dextop-release-*.rpm
```

CentOS 7 

```
$ wget http://li.nux.ro/download/nux/dextop/el7/x86_64/nux-dextop-release-0-5.el7.nux.noarch.rpm
$ rpm --import http://li.nux.ro/download/nux/RPM-GPG-KEY-nux.ro
$ rpm -Uvh nux-dextop-release-*.rpm

```

安装

```
$ yum install ffmpeg ffmpeg-devel
```

## PHP 中使用

> 推荐使用PHP代码实现的功能包，以下两个package基本涵盖了常用的ffmpeg的功能，比如：获取视频属性、截图、截取gif、转换格式、合并、缩放分辨率、压缩码率、按照时间剪辑、截取部分画面

- https://github.com/PHP-FFMpeg/PHP-FFMpeg  这个项目作者**实测**，非常方便
- https://github.com/CodeScaleInc/ffmpeg-php 函数名和下面so扩展一致


## PHP扩展 (不推荐)

### 如果安装的ffmpeg为v0.6，则可安装下面的扩展，否则参照上面！

```
$ wget http://downloads.sourceforge.net/project/ffmpeg-php/ffmpeg-php/0.6.0/ffmpeg-php-0.6.0.tbz2
$ tar -xjf ffmpeg-php-0.6.0.tbz2
$ cd ffmpeg-php-0.6.0
$ phpize
$ ./configure
```

### PHP 5.4编译会报错

```
//修改row 311
list_entry *le; → zend_rsrc_list_entry *le;
//修改 row 346
list_entry new_le; → zend_rsrc_list_entry new_le;
//修改 row 360
hashkey_length+1, (void *)&new_le, sizeof(list_entry), → hashkey_length+1, (void *)&new_le,sizeof(zend_rsrc_list_entry),
```

### 报错：ffmpeg_frame.c:421 : error: 'PIX_FMT_RGBA32' undeclared (first use in this function)

```
# 批量替换
$ sed -i 's/PIX_FMT_RGBA32/PIX_FMT_RGB32/g' ./ffmpeg_frame.c
```

### 编译、配置
```
$ make
$ cp modules/ffmpeg.so /usr/lib64/php/modules
$ echo -e "extension=ffmpeg.so\n" > /etc/php.d/ffmpeg.ini
```

## 卸载
```
$ rm -rf /usr/lib64/php/modules/ffmpeg.so
$ rm -rf /etc/php.d/ffmpeg.ini
```
