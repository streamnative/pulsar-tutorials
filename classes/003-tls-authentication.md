## 1. Planning

- super-user: admin
- users:
    - batman
    - ironman

## 2. Create `admin` super-user certificate

Generate key.

```
openssl genrsa -out admin.key.pem 2048
```

Covert to PKCS8 format.

```
openssl pkcs8 -topk8 -inform PEM -outform PEM \
      -in admin.key.pem -out admin.key-pk8.pem -nocrypt
```

Generate client certificate request. Use `admin` as common name. Because common name is used as the `role` for TLS authentication.

```
openssl req -config openssl.cnf \
      -key admin.key.pem -new -sha256 -out admin.csr.pem
```

Sign the request.

```
openssl ca -config openssl.cnf -extensions usr_cert \
      -days 1000 -notext -md sha256 \
      -in admin.csr.pem -out admin.cert.pem
```

## 2. Create `batman` user certificate

Generate key.

```
openssl genrsa -out batman.key.pem 2048
```

Covert to PKCS8 format.

```
openssl pkcs8 -topk8 -inform PEM -outform PEM \
      -in batman.key.pem -out batman.key-pk8.pem -nocrypt
```

Generate client certificate request. Use `batman` as common name. Because common name is used as the `role` for TLS authentication.

```
openssl req -config openssl.cnf \
      -key batman.key.pem -new -sha256 -out batman.csr.pem
```

Sign the request.

```
openssl ca -config openssl.cnf -extensions usr_cert \
      -days 1000 -notext -md sha256 \
      -in batman.csr.pem -out batman.cert.pem
```

## 3. Create `ironman` user certificate

Generate key.

```
openssl genrsa -out ironman.key.pem 2048
```

Covert to PKCS8 format.

```
openssl pkcs8 -topk8 -inform PEM -outform PEM \
      -in ironman.key.pem -out ironman.key-pk8.pem -nocrypt
```

Generate client certificate request. Use `ironman` as common name. Because common name is used as the `role` for TLS authentication.

```
openssl req -config openssl.cnf \
      -key ironman.key.pem -new -sha256 -out ironman.csr.pem
```

Sign the request.

```
openssl ca -config openssl.cnf -extensions usr_cert \
      -days 1000 -notext -md sha256 \
      -in ironman.csr.pem -out ironman.cert.pem
```