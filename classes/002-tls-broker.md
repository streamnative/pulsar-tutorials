## 1. Planning

- Broker
- Proxy

## 2. TLS Encryption

### 2.1. Certificate authority

```
mkdir broker-ca
```
```
cd broker-ca
```
```
wget https://raw.githubusercontent.com/apache/pulsar/master/site2/website/static/examples/openssl.cnf
```

Create dirs for certs and keys.

```
mkdir certs crl newcerts private
```
```
chmod 700 private/
```
```
touch index.txt
```
```
echo 1000 > serial
```
```
openssl genrsa -aes256 -out private/ca.key.pem 4096
```
```
chmod 400 private/ca.key.pem
```
```
openssl req -config openssl.cnf -key private/ca.key.pem \
      -new -x509 -days 7300 -sha256 -extensions v3_ca \
      -out certs/ca.cert.pem
```
```
```

Use common name: `example.local`


After you answer the question prompts, CA-related files are stored in the `./broker-ca` directory. Within that directory:

- `certs/ca.cert.pem` is the public certificate. This public certificates is meant to be distributed to all parties involved.
- `private/ca.key.pem` is the private key. You only need it when you are signing a new certificate for either broker or clients and you must safely guard this private key.

### 2.2. Broker certificate

Generate the broker key.

```
openssl genrsa -out broker.key.pem 2048
```

Convert to PKCS8 format.

```
openssl pkcs8 -topk8 -inform PEM -outform PEM \
      -in broker.key.pem -out broker.key-pk8.pem -nocrypt
```

Generate the certificate request. Use common name `broker.example.local`.

```
openssl req -config openssl.cnf \
      -key broker.key.pem -new -sha256 -out broker.csr.pem
```

Sign it with the certificate authority.

```
openssl ca -config openssl.cnf -extensions server_cert \
      -days 1000 -notext -md sha256 \
      -in broker.csr.pem -out broker.cert.pem
```

### 2.3. Proxy certificate

Generate the proxy key.

```
openssl genrsa -out proxy.key.pem 2048
```

Convert to PKCS8 format.

```
openssl pkcs8 -topk8 -inform PEM -outform PEM \
      -in proxy.key.pem -out proxy.key-pk8.pem -nocrypt
```

Generate the certificate request. Use common name `proxy.example.local`.

```
openssl req -config openssl.cnf \
      -key proxy.key.pem -new -sha256 -out proxy.csr.pem
```

Sign it with the certificate authority.

```
openssl ca -config openssl.cnf -extensions server_cert \
      -days 1000 -notext -md sha256 \
      -in proxy.csr.pem -out proxy.cert.pem
```