# 密码证书

## ## 正常证书
### ### RSA私钥
```
$ openssl genrsa -out rsa_private.key 2048
```

### ### RSA公钥
```
$ openssl rsa -in rsa_private.key -pubout -out rsa_public.key
```

## ## AES256加密

### ### AES加密的私钥

> 如果下文去掉 `-passout pass:你的密码`，则终端会提示输入密码

```
$ openssl genrsa -aes256 -passout pass:你的密码 -out rsa_aes_private.key 2048
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

> 如果下文去掉 `-passin pass:你的密码`，则终端会提示输入密码

```
$ openssl rsa -in rsa_aes_private.key -passin pass:你的密码 -pubout -out rsa_public.key
```




