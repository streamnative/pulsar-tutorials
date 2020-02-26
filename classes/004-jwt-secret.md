Export `PULSAR_HOME` to pulsar binary distribution

```
export PULSAR_HOME=/path/to/pulsar/home
```

Create jwt dir

```
mkdir -p jwt
```
```
cd jwt
```

Create a secret key.

```
${PULSAR_HOME}/bin/pulsar tokens create-secret-key --output $(pwd)/my-secret.key --base64
```

Generate token for `admin`

```
${PULSAR_HOME}/bin/pulsar tokens create --secret-key file://$(pwd)/my-secret.key \
            --subject admin > $(pwd)/admin.secret.token
```

Generate token for `batman`

```
${PULSAR_HOME}/bin/pulsar tokens create --secret-key file://$(pwd)/my-secret.key \
            --subject batman > $(pwd)/batman.secret.token
```

Generate token for `ironman`

```
${PULSAR_HOME}/bin/pulsar tokens create --secret-key file://$(pwd)/my-secret.key \
            --subject ironman > $(pwd)/ironman.secret.token
```
