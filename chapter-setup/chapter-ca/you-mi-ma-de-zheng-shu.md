# 密码证书

## ## 正常证书

上一章之后有大量的介绍，这里不在赘述

### ### RSA私钥
```
$ openssl genrsa -out rsa_private.key 2048
```

### ### RSA公钥
```
$ openssl rsa -in rsa_private.key -pubout -out rsa_public.key
```

## ## 加密证书

只有私钥才能设置密码

- `--passout` 表示给输出文件设置密码
- `--passint` 表示输入的文件有密码

### ### AES加密的私钥

> 如果下文去掉 `-passout pass:密码`，则终端会提示输入密码

```
$ openssl genrsa -aes256 -passout pass:密码 -out rsa_aes_private.key 2048
```

生成的文件内容会添加类似如下内容

```
-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: AES-256-CBC,5584D000DDDD53DD5B12AE935F05A007
Base64 Encoded Data
-----END RSA PRIVATE KEY-----
```

### ### AES 加密公钥

此时必须提供密码才能生成公钥

> 如果下文去掉 `-passin pass:密码`，则终端会提示输入密码

```
$ openssl rsa -in rsa_aes_private.key -passin pass:密码 -pubout -out rsa_public.key
```
## ## 转换

### ### 无密码 -> 密码

```
$ openssl rsa -in rsa_private.key -aes256 -passout pass:密码 -out rsa_aes_private.key
```

### ### 密码 -> 无密码

```
$ openssl rsa -in rsa_aes_private.key -passin pass:密码 -out rsa_private.key
```



