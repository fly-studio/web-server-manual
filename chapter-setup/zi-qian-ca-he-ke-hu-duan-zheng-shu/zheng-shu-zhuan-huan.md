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