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
> 关于remi的解释，请查看「PHP 安装与配置 - 安装」


# # 目录
## ## 设置运行目录
```
$ mkdir -m 777 -p /www/database/mysql/
```
## ## 工作目录
需要等待安装之后才能设置
```
$ chown -R mysql:mysql /www/database/mysql
```