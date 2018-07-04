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


## ## PKCS1 -> PKCS8

```
$ openssl pkcs8 -topk8 -inform PEM -in cakey.pem -outform pem -nocrypt -out pkcs8.pem
```

如果 cakey.pem 有密码，则在上面末尾加上 `-passout pass:密码`



## ## PKCS8 -> PKCS1

```
openssl pkcs8 -in pkcs8.pem -nocrypt -out pri_key.pem
```