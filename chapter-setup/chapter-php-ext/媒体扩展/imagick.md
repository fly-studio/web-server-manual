# # Imagick

> Imagick是一款优于GD的图片处理扩展

## 安装程序

> 这是php-imagick扩展的必要程序

```
$ yum --enablerepo=remi install ImageMagick ImageMagick-devel
```
## 安装扩展
```
$ pecl install imagick
$ echo "extension=imagick.so" > /etc/php.d/imagick.ini
```

安装中的选项
> Please provide the prefix of Imagemagick installation [autodetect] : <kbd>Enter</kbd>

## （或）源代码安装
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

## 卸载
```
$ yum remove ImageMagick ImageMagick-devel
$ pecl uninstall imagick
$ rm -rf /etc/php.d/imagick.ini
```
