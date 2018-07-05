# 证书基本操作

- 常见后缀： key、prv；pem；csr；pub、crt。后缀仅仅作为参考，具体以其内容为准。
- 私钥：不对外公布，RSA的私钥，一般后缀为`key`。
- 公钥：RSA的公钥，由私钥析出。广义上公钥就是指证书，常见后缀`crt`，可以在`Windows`上面双击查看其信息。
- CA：根证书，其实CA也是普通的证书的一种，比如大型免费证书发布商 `Let's Encrypt`，只是因为它们已经预装在系统、浏览器中，比如：`Chrome`、`Windows`、`Mac`、`Linux`、`Firefox`。所以浏览器中可以直接访问由这些`CA根证书`签发的证书而不会报错。不然就需要导入自己颁发的根证书，见下文。
- 普通证书：普通证书需要和`CA公钥`合并签发最终证书，也就是形成一个证书链，系统会验证其链上的所有证书，如果遇到不受信任的，将会报错。
- 如何制作证书请参考下级分类。


## 导入根证书到系统

### 导入到Linux系统
```
$ cp /etc/pki/tls/certs/ca-bundle.crt{,.bak}    备份以防出错
$ cat ~/ca/cacert.pem >> /etc/pki/tls/certs/ca-bundle.crt
```
### 导入到Windows系统
将`cacert.pem`复制并改名为`cacert.crt`，双击，并安装到受信任的根证书颁发机构

![](/assets/ie_certificate_pathpng.png)


## nginx配置示例
```
ssl on;
ssl_certificate ~/certs/nginx.crt;
ssl_certificate_key ~/certs/nginx.key;
```

# 其它

## 查看证书

```
$ openssl x509 -text -noout -in cacert.crt
```

```
$ openssl rsa -noout -text -in cacert.crt
```

## 错误

### 错误1
文件不存在
```
$ touch /etc/pki/CA/index.txt
$ echo '1000' > /etc/pki/CA/serial
```

### 错误2

```
The mandatory stateOrProvinceName field was missing
```

`/etc/pki/tls/openssl.cnf`将 `match` 改为 `optional`

```
# For the CA policy
[ policy_match ]
countryName        = optional 
stateOrProvinceName    = optional
organizationName    = optional
organizationalUnitName    = optional
commonName        = supplied
emailAddress        = optional
```

### 错误3

如下错误请升级openssl

```
ERROR:Serial number 01 has already been issued,
      check the database/serial_file for corruption
```
### 错误4

如下错误请升级openssl

```
failed to update database
TXT_DB error number 2
```


## 浏览器

### Chrome下，可以启用本地location证书

```
chrome://flags/#allow-insecure-localhost
```