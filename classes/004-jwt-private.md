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

Create a public/private key.

```
${PULSAR_HOME}/bin/pulsar tokens create-key-pair --output-private-key $(pwd)/my-private.key --output-public-key $(pwd)/my-public.key
```

Generate token for `admin`

```
${PULSAR_HOME}/bin/pulsar tokens create --private-key file://$(pwd)/my-private.key \
            --subject admin > $(pwd)/admin.private.token
```

Generate token for `batman`

```
${PULSAR_HOME}/bin/pulsar tokens create --private-key file://$(pwd)/my-private.key \
            --subject batman > $(pwd)/batman.private.token
```

Generate token for `ironman`

```
${PULSAR_HOME}/bin/pulsar tokens create --private-key file://$(pwd)/my-private.key \
            --subject ironman > $(pwd)/ironman.private.token
```
