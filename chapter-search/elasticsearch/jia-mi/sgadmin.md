# 管理

## 开启searchguard的前端管理接口（可选）

```
searchguard.restapi.roles_enabled: ["sg_all_access"]
```

使用接口参考：https://docs.search-guard.com/latest/rest-api-access-control

## sgadmin 

下文是一个通用命令行：
- 忽略了HostName的验证
- 忽略了集群名称

```
$ /usr/share/elasticsearch/plugins/search-guard-6/tools/sgadmin.sh \
  -cert /path/to/manager.crt \
  -key /path/to/manager_pkcs8.pem \
  -cacert /path/to/cacert.pem \
  --ignore-clustername --disable-host-name-verification --disable-resolve-hostname \
  --目标指令见下文
```

> **目标指令**就是下文介绍的各个指令，比如初始化的指令为，其它用法依此类推。

>  ```
  /usr/share/elasticsearch/plugins/search-guard-6/tools/sgadmin.sh \
    -cert /path/to/manager.crt \
    -key /path/to/manager_pkcs8.pem \
    -cacert /path/to/cacert.pem \
    --ignore-clustername --disable-host-name-verification --disable-resolve-hostname \
    -cd /usr/share/elasticsearch/plugins/search-guard-6/sgconfig/
  ```

## 初始化 --cd /folder/path/

在上一章中，已经使用过本指令
```
-cd /usr/share/elasticsearch/plugins/search-guard-6/sgconfig/
```