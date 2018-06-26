## ## 临时证书

如果觉得颁发CA 以及然后办法客户端证书比较繁琐，可以使用下面指令一步到位

也就是说，crt文件需要当做根证书导入到系统，无法无效

```
openssl req -x509 -out nginx.crt -keyout nginx.key \
  -newkey rsa:2048 -nodes -sha256 \
  -subj '/CN=www.domain.com' -extensions EXT -config <( \
   printf "[dn]\nCN=www.domain.com\n[req]\ndistinguished_name = dn\n[EXT]\nsubjectAltName=DNS:www.domain.com\nkeyUsage=digitalSignature\nextendedKeyUsage=serverAuth")
```

如何导入系统请参考上文


