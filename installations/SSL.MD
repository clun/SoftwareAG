
Install GnuWin32

Generate PEM

```shell
openssl genrsa -out key.pem 2048
openssl req -new -sha256 -key key.pem -out csr.csr
openssl req -x509 -sha256 -days 365 -key key.pem -in csr.csr -out certificate.pem
```

Generate DER

```shell
openssl x509 -outform der -in certificate.pem -out certificate.der
```

Generate KeyStore

```shell
keytool -J-Duser.language=en -import -alias webmethods -keystore webmethods.jks -file C:/tmp/webmethods.der
```

Lits Items in KeyStore

```shell
keytool.exe -list -v -J-Duser.language=en -keystore c:/tmp/webmethods-keystore.jks
```


Generate TrustStore 
```shell
keytool.exe -J-Duser.language=en -import -file C:/tmp/webmethods.pem -alias webmethodsT -keystore webmethods-trustStore.jks
```



