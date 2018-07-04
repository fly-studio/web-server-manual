# 证书转换


## ## PKCS1

默认的`RSA证书` 就是 `PKCS1证书`，指令类似：`openssl genrsa -out private.key 1024`

内容类似

```
-----BEGIN RSA PRIVATE KEY-----

-----END RSA PRIVATE KEY-----
```

## ## PKCS8

内容类似

```
-----BEGIN PRIVATE KEY-----

-----END PRIVATE KEY-----
```

## ## 私钥 -> der

```
$ openssl rsa -in cakey.pem -outform der-out rsa_aes_private.der
```


## ## PKCS1 -> PKCS8

### 无密码
```
$ openssl pkcs8 -topk8 -inform PEM -in cakey.pem -outform pem -nocrypt -out pkcs8.pem
```

### 设置密码

pkcs8默认采用des3 加密算法

```
$ openssl pkcs8 -topk8 -inform PEM -in cakey.pem -outform pem -out pkcs8.pem -passout pass:密码
```
生成的文件类似
```
-----BEGIN ENCRYPTED PRIVATE KEY-----

-----END ENCRYPTED PRIVATE KEY-----
```




## ## PKCS8 -> PKCS1

```
openssl pkcs8 -in pkcs8.pem -nocrypt -out pri_key.pem
```