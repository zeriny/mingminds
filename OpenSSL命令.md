## OpenSSL命令

```sh
openssl s_client -showcerts -connect 1.119.0.9:443

openssl x509 -inform der -in DigiCertGlobalCAG2.crt #将证书文件转成Pem格式

openssl verify xxx.der #验证证书，xxx.der里需要是证书链

openssl x509 -text -in test.pem

openssl s_client -connect 202.112.51.112:443 -servername "test.zeriny.com"| openssl x509 -noout -text

openssl s_client -connect www.yhd.com:443 | openssl x509 -noout -text
```





生成SSL证书：https://www.jianshu.com/p/9523d888cf77

