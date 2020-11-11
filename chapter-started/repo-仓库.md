# 软件仓库

手册中所有软件仓库都基于以下仓库

## Base 仓库

```
# 备份原仓库，记得先安装wget
$ mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
```

```
# 6.x
$ wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-6.repo

# 7.x
$ wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo
```

非阿里云ESC用户需要替换如下网址

```
$ sed -i -e '/mirrors.cloud.aliyuncs.com/d' -e '/mirrors.aliyuncs.com/d' /etc/yum.repos.d/CentOS-Base.repo
```

## epel 仓库

重要仓库

```
$ yum install epel-release
```

```
# 6.x
$ wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-6.repo

# 7.x
$ wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo
```

## remi软件仓库

PHP、Redis等软件的新版本仓库

```
# 6.x
$ wget https://mirrors.aliyun.com/remi/enterprise/remi-release-6.rpm

# 7.x
$ wget https://mirrors.aliyun.com/remi/enterprise/remi-release-7.rpm
```

```
$ rpm -Uvh remi-release-*.rpm
```

替换成aliyun的源网址

```
$ sed -i -e 's/\#baseurl\=http:\/\/rpms.remirepo.net\//baseurl\=https:\/\/mirrors.aliyun.com\/remi\//g' /etc/yum.repos.d/remi-*.repo
```

`remi` 软件库包含`PHP 5.4-5.6、7.0-7.4、8.0`，以及其对应的扩展  
并且默认情况下`remi`库是未启用状态，需要使用类似`enablerepo=remi-php74`启用指定版本

## RepoForge 仓库

```
$ vim /etc/yum.repos.d/rpmforge.repo
```

```
[rpmforge]
name = RHEL $releasever - RPMforge.net - dag
baseurl = https://mirrors.aliyun.com/repoforge/redhat/el$releasever/en/$basearch/rpmforge
mirrorlist = http://mirrorlist.repoforge.org/el$releasever/mirrors-rpmforge
enabled = 0
protect = 0
gpgkey = file:///etc/pki/rpm-gpg/RPM-GPG-KEY-rpmforge-dag
gpgcheck = 1
[rpmforge-extras]
name = RHEL $releasever - RPMforge.net - extras
baseurl = https://mirrors.aliyun.com/repoforge/redhat/el$releasever/en/$basearch/extras
mirrorlist = http://mirrorlist.repoforge.org/el$releasever/mirrors-rpmforge-extras
enabled = 0
protect = 0
gpgkey = file:///etc/pki/rpm-gpg/RPM-GPG-KEY-rpmforge-dag
gpgcheck = 1
[rpmforge-testing]
name = RHEL $releasever - RPMforge.net - testing
baseurl = https://mirrors.aliyun.com/repoforge/redhat/el$releasever/en/$basearch/testing
mirrorlist = http://mirrorlist.repoforge.org/el$releasever/mirrors-rpmforge-testing
enabled = 0
protect = 0
gpgkey = file:///etc/pki/rpm-gpg/RPM-GPG-KEY-rpmforge-dag
gpgcheck = 1
EOF
```

## Sclo-RH 仓库

这个仅仅在需要升级GCC的时候需要，按需安装

```
$ vim /etc/yum.repos.d/CentOS-Sclo-RH.repo
```

```
# CentOS-SCLo-rh.repo
#
# Please see http://wiki.centos.org/SpecialInterestGroup/SCLo for more
# information

[centos-sclo-rh]
name=CentOS-$releasever - SCLo rh
baseurl=http://mirror.centos.org/centos/$releasever/sclo/$basearch/rh/
gpgcheck=0
enabled=1
#gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-SIG-SCLo

[centos-sclo-rh-testing]
name=CentOS-$releasever - SCLo rh Testing
baseurl=http://buildlogs.centos.org/centos/$releasever/sclo/$basearch/rh/
gpgcheck=0
enabled=0
#gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-SIG-SCLo

[centos-sclo-rh-source]
name=CentOS-6 - SCLo rh Sources
baseurl=http://vault.centos.org/centos/$releasever/sclo/Source/rh/
gpgcheck=0
enabled=0
#gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-SIG-SCLo

[centos-sclo-rh-debuginfo]
name=CentOS-$releasever - SCLo rh Debuginfo
baseurl=http://debuginfo.centos.org/centos/$releasever/sclo/$basearch/
gpgcheck=0
enabled=0
#gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-SIG-SCLo
```

