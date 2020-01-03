# # Xdebug

> Xdebug是一个PHP程序调试器，可以用来跟踪，调试和分析PHP程序的运行状况。

## 安装
```
$ git clone https://github.com/derickr/xdebug.git
$ cd xdebug
$ phpize
$ ./configure --enable-xdebug
$ make
$ cp modules/xdebug.so /usr/lib64/php/modules/
$ echo 'zend_extension="/usr/lib64/php/modules/xdebug.so"' > /etc/php.d/xdebug.ini
```

## 配置
> 详细配置请查看 http://www.xdebug.com/docs/all_settings

```
$ mkdir -p /www/website/xdebug
```

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

## 卸载
```
$ rm -rf /usr/lib64/php/modules/xdebug.so
$ rm -rf /etc/php.d/xdebug.ini
$ rm -rf /www/website/xdebug
```