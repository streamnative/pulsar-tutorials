```
mkdir bookie-ca
```

Generate TLS key and certificate.

```
keytool -keystore bookie.keystore.jks -alias localhost -validity 1000 -genkey
```

Create CA.

```
openssl req -new -x509 -keyout ca-key -out ca-cert -days 365
```

Add generated CA to truststore.

```
keytool -keystore bookie.truststore.jks -alias CARoot -import -file ca-cert
```

```
keytool -keystore client.truststore.jks -alias CARoot -import -file ca-cert
```

Sing all certificates in the keystore.

```
keytool -keystore bookie.keystore.jks -alias localhost -certreq -file cert-file
```

Sign it with CA

```
openssl x509 -req -CA ca-cert -CAkey ca-key -in cert-file -out cert-signed -days 1000 -CAcreateserial -passin pass:{ca-password}
```

Import both the certificate of the CA and the signed certificate into the keystore.

```
keytool -keystore bookie.keystore.jks -alias CARoot -import -file ca-cert
```
```
keytool -keystore bookie.keystore.jks -alias localhost -import -file cert-signed
```

The definitions of the parameters are the following:

- keystore: the location of the keystore
- ca-cert: the certificate of the CA
- ca-key: the private key of the CA
- ca-password: the passphrase of the CA
- cert-file: the exported, unsigned certificate of the bookie
- cert-signed: the signed certificate of the bookie