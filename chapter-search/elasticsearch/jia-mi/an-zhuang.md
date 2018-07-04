# 安装

英文原版：https://docs.search-guard.com/latest/search-guard-installation

## I 下载

打开下载地址，点击 zip，文件名类似：`search-guard-6-6.3.0-compliance-2.zip`
```
http://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.floragunn%22%20AND%20a%3A%22search-guard-6%22
```

## II 关闭碎片分配(可选)

此步骤是可选的，但特别推荐用于具有大量数据的大型群集。此步骤可确保在重新启动群集时不会移动分片，从而导致大量`I/O 消耗`。 在任意一台执行即可，可以`PUT请求`下面的`JSON`。
```
$ curl -Ss -XPUT 'https://localhost:9200/_cluster/settings?pretty' \ 
  -H 'Content-Type: application/json' -d'
{
  "persistent": {
    "cluster.routing.allocation.enable": "none"
  }
}
'
```

## III 离线安装

```
$ cd /usr/share/elasticsearch
$ bin/elasticsearch-plugin install -b file:///path/to/search-guard-6-<version>.zip
```

如果安装的时候要求权限，输入 `y`
```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@     WARNING: plugin requires additional permissions     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
```

## IV 创建Node传输证书

此证书是在集群之间通过9300端口传输数据的时候的证书，**强制要求**。

> 关于如何创建证书：参考本手册-[普通CN证书](/chapter-setup/zi-qian-ca-he-ke-hu-duan-zheng-shu/pu-tong-zheng-shu.md)
> 如果可能，也可以使用这些脚本生成： https://github.com/floragunncom/search-guard-ssl/tree/master/example-pki-scripts


- `CA`正常创建，客户端证书需要将客户端的`私钥KEY`转换为`PKCS#8`的格式，参照 [证书转换](/chapter-setup/zi-qian-ca-he-ke-hu-duan-zheng-shu/zheng-shu-zhuan-huan.md)

- 将这些文件放到 `/etc/elasticsearch/` 下。

## V 配置

编辑 `/etc/elasticsearch/elasticsearch.y:qml`文件

### 关闭企业版

```
searchguard.enterprise_modules_enabled: false
```

### 所有配置

> 参考模板 https://github.com/floragunncom/search-guard-ssl/blob/master/searchguard-ssl-config-template.yml

### 证书配置

> 证书配置参考: https://docs.search-guard.com/latest/configuring-tls

下文的路径必须是相对路径，因为证书需要放在`/etc/elasticsearch/` 下

#### Node通讯证书

```
# 不校验hostname，因为要签SAN
searchguard.ssl.transport.enforce_hostname_verification: false
# 允许如下证书的Common Name，支持通配符和正则
searchguard.nodes_dn:
  - 'CN=*.domain.com'
  - '/CN=.*regex/'
searchguard.ssl.transport.enabled_protocols:
  - "TLSv1.2"
searchguard.ssl.transport.pemcert_filepath: 客户端公钥证书（就是和CA连接最终证书，crt后缀）
searchguard.ssl.transport.pemkey_filepath: 客户端私钥KEY，PKCS#8格式
searchguard.ssl.transport.pemkey_password: 如果没有密码则删除本行
searchguard.ssl.transport.pemtrustedcas_filepath: CA的公钥证书，按照上例创建的将是cacert.key文件

# 关闭xpack的安全设置，不然会导致冲突
xpack.security.enabled: false
```

#### 前端HTTPs证书配置

可以不开启，因为这个证书会被浏览器/cURL验证，所以需要签受信任的CA证书签SAN才有效，比如使用`Let's Encrypt证书`

```
searchguard.ssl.http.enabled: true
searchguard.authcz.admin_dn:
  - CN=admin,OU=SSL,O=Test,L=Test,C=DE.
searchguard.ssl.http.pemkey_filepath: privkey.pem
searchguard.ssl.http.pemkey_password: "secret"
searchguard.ssl.http.pemcert_filepath: client.crt
searchguard.ssl.http.pemtrustedcas_filepath: cacert.pem
searchguard.ssl.http.enabled_ciphers:
  - "TLS_DHE_RSA_WITH_AES_256_CBC_SHA"
  - "TLS_DHE_DSS_WITH_AES_128_CBC_SHA256"
searchguard.ssl.http.enabled_protocols:
  - "TLSv1.2"
```


## VI 启动ElasticSearch

正常启动1台，注意，**先启动一台**, 遇到问题可以查看日志去解决

## VII 配置管理端

### 加入执行权限

```
$ cd /usr/share/elasticsearch/plugins/search-guard-6/tools
$ chmod +x sgadmin.sh
```

### 配置中加入可以管理的证书

为了安全，需要再签一套客户端证书，`Common Name`填写管理员的名称

> 比如：笔者直接使用上文node用的ca和客户端私钥，颁发这套证书

然后加入到主配置，标记只有使用这些证书才能操作`sgamin.sh`

```
earchguard.authcz.admin_dn:
  - 'CN=name-1'
  - 'CN=name-2'
```

## VII 打开碎片分配(可选)

如果之前关闭过，此时需要重新打开，但是此时仍然无法直接访问ES去打开，需要使用命令行

证书，就是上文中签发的管理证书

```
$ /usr/share/elasticsearch/plugins/search-guard-6/tools/sgadmin.sh \
  -cert /path/to/manager.crt \
  -key /path/to/manager_pkcs8.pem \
  -cacert /path/to/cacert.pem \
  --ignore-clustername --disable-host-name-verification --disable-resolve-hostname \
  --enable-shard-allocation
```

## VIII 初始化Search Guard

由于 Search Guard 默认情况下没有将任何配置加入到ES中，所以必须使用`sgadmin`进行初始化

```
$ /usr/share/elasticsearch/plugins/search-guard-6/tools/sgadmin.sh \
  -cert /path/to/manager.crt \
  -key /path/to/manager_pkcs8.pem \
  -cacert /path/to/cacert.pem \
  --ignore-clustername --disable-host-name-verification --disable-resolve-hostname --enable-shard-allocation \
  -cd /usr/share/elasticsearch/plugins/search-guard-6/sgconfig/
```

如果发生如下错误，表示当前ES的碎片分配未打开，请执行 VII 的步骤
```
UnavailableShardsException: [[searchguard][0] Primary shard is not active or isn't assigned is a known node. Timeout: [1m],
```

## IX 启动集群
初始化结束，打开集群中的其它ES


