# 使用本地镜像安装

> https://www.jianshu.com/p/7c91b799b12e

镜像源于：https://hub.docker.com/u/kolla/

## 添加hosts

在kolla机器上填写hosts

```
# 如果将docker搭建其它机器上，自行修改ip
127.0.0.1 registryserver
```

#### 启动容器

在`registryserver`上执行

```
$ docker run -d -p 5001:5000 --name registry registry
```

#### 得到所有镜像名

随便找一台机器，`git clone kolla-anible's github`

```
$ grep "docker_namespace }" kolla-ansible/ -R | while read line ; do line=${line##*\}-};line=${line%\"};  echo $line >> images ;done
```

查看images文件，如有有，则修改其中的内容

```
neutron-server{{ '-opendaylight' if enable_opendaylight | bool else '' }}
{{ rabbitmq_image_name }}
```

为

```
neutron-server
neutron-server-opendaylight
rabbitmq
```


### 拉取镜像，并上传到私有仓库

如果仓库非本地127.0.0.1，需要当前机器上的`/etc/docker/daemon.json`添加

```
{
  "insecure-registries":["registryserver:5001"]
}
```

```
$ vim sync_images.sh
```

```
#!/bin/bash
IMAGEFILE=images
DOCKER_NAMESPACE="kolla"
KOLLA_BASE_DISTRO="centos"
INSTALL_TYPE="binary"
TAG="train"
DES_REGISTRY="registryserver:5001"

images=`cat $IMAGEFILE`
count=`cat $IMAGEFILE |wc -l`

icount=1
for image in $images
do
  echo [$icount/$count]: $image
  
  docker pull $DOCKER_NAMESPACE/${KOLLA_BASE_DISTRO}-${INSTALL_TYPE}-$image:$TAG
  
  docker tag $DOCKER_NAMESPACE/${KOLLA_BASE_DISTRO}-${INSTALL_TYPE}-$image:$TAG $DES_REGISTRY/$DOCKER_NAMESPACE/${KOLLA_BASE_DISTRO}-${INSTALL_TYPE}-$image:$TAG

  docker push $DES_REGISTRY/$DOCKER_NAMESPACE/${KOLLA_BASE_DISTRO}-${INSTALL_TYPE}-$image:$TAG
  
  ((icount++))

done
```

```
$ chmod +x sync_images.sh
$ ./sync_images.sh
```

#### 打包镜像

在`registryserver`上执行

```
$ docker inspect registry
```