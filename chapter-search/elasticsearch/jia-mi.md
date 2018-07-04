# 加密

X-Pack虽然开源，但是加密功能依旧收费，我们选择用[search-guard](https://search-guard.com/)的免费版本实现

## 下载

打开下载地址，点击 zip，文件名类似：`search-guard-6-6.3.0-compliance-2.zip`
```
http://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.floragunn%22%20AND%20a%3A%22search-guard-6%22
```

## 关闭碎片分配

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

## 离线安装

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

## 创建证书

关于如何创建证书：[普通CN证书](/chapter-setup/zi-qian-ca-he-ke-hu-duan-zheng-shu/pu-tong-zheng-shu.md)

`CA`正常创建，客户端证书需要将客户端的`私钥KEY`转换为`PKCS#8`的格式，参照 [证书转换](/chapter-setup/zi-qian-ca-he-ke-hu-duan-zheng-shu/zheng-shu-zhuan-huan.md)

将这些文件放到 `/etc/elasticsearch/` 下。

## 配置

编辑 `/etc/elasticsearch/elasticsearch.yaml`文件

> 参考模板 https://github.com/floragunncom/search-guard-ssl/blob/master/searchguard-ssl-config-template.yml


下文的路径必须是相对路径，因为证书需要放在`/etc/elasticsearch/` 下

```
# 关闭企业版
searchguard.enterprise_modules_enabled: false
# 不校验hostname
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

同理HTTPs的配置如下，

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
  


