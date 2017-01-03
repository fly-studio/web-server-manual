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
$ yum --enablerepo=remi-php71,remi install php-pear php-devel
```