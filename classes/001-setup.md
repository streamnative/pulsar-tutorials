## 0. Create network

```bash
docker network create "pulsar"
```

```bash
export PULSAR_NETWORK=pulsar
```

List the networks

```bash
docker network ls
```

## 1. Setup zookeeper

```bash
mkdir -p data/zookeeper0
```

```bash
docker run -it --rm -d \
    -v $(pwd)/conf/cluster0:/pulsar/conf \
    -v $(pwd)/data/zookeeper0:/pulsar/data/zookeeper \
    --network ${PULSAR_NETWORK} \
    --name zookeeper0 \
    --hostname zookeeper0 \
    -p 2181:2181 \
    apachepulsar/pulsar-all:2.5.0 \
    bin/pulsar zookeeper
```

### 1.1. Health Check

check the log to see if zookeeper is running

```
docker logs -f zookeeper0
```

health check

```
telnet localhost 2181
```

send `ruok` command to zookeeper and you should see `imok` is returned.

### 2. Initialize Cluster Metadata

Connect to the zookeeper cluster.

```
docker exec -it zookeeper0 bash
```

Initialize the pulsar cluster metadata.

```
export PULSAR_CLUSTER="cluster0"
```

```
bin/pulsar initialize-cluster-metadata \
    --cluster ${PULSAR_CLUSTER} \
    --zookeeper zookeeper0:2181 \
    --configuration-store zookeeper0:2181 \
    --web-service-url http://${PULSAR_CLUSTER}-broker0:8080 \
    --web-service-url-tls https://${PULSAR_CLUSTER}-broker0:8443 \
    --broker-service-url pulsar://${PULSAR_CLUSTER}-broker0:6650 \
    --broker-service-url-tls pulsar+ssl://${PULSAR_CLUSTER}-broker0:6651
```

Connect to zookeeper shell to see what is created on zookeeper.

```
bin/pulsar zookeeper-shell
```

### 3. Start bookies

#### 3.1 Start first bookie

```bash
mkdir -p data/cluster0-bookkeeper0
mkdir -p logs/cluster0-bookkeeper0
```

```bash
docker run -it --rm -d \
    -v $(pwd)/conf/cluster0/bookkeeper0:/pulsar/conf \
    -v $(pwd)/data/cluster0-bookkeeper0:/pulsar/data/bookkeeper \
    -v $(pwd)/logs/cluster0-bookkeeper0:/pulsar/logs \
    --network ${PULSAR_NETWORK} \
    --name cluster0-bookkeeper0 \
    --hostname cluster0-bookkeeper0 \
    -p 3181:3181 \
    -p 8001:8000 \
    apachepulsar/pulsar-all:2.5.0 \
    bin/pulsar bookie
```

On zookeeper node, check if the bookie is registered.

On bookkeeper0 node, do a `simpletest` to verify the bookie is working as expected.

```bash
bin/bookkeeper shell simpletest -e 1 -w 1 -a 1 10
```

#### 3.2 Start second bookie

```bash
mkdir -p data/cluster0-bookkeeper1
mkdir -p logs/cluster0-bookkeeper1
```

```bash
docker run -it --rm -d \
    -v $(pwd)/conf/cluster0/bookkeeper1:/pulsar/conf \
    -v $(pwd)/data/cluster0-bookkeeper1:/pulsar/data/bookkeeper \
    -v $(pwd)/logs/cluster0-bookkeeper1:/pulsar/logs \
    --network ${PULSAR_NETWORK} \
    --name cluster0-bookkeeper1 \
    --hostname cluster0-bookkeeper1 \
    -p 3182:3181 \
    -p 8002:8000 \
    apachepulsar/pulsar-all:2.5.0 \
    bin/pulsar bookie
```

##### Health check

- check docker log
- check http endpoint
    ```bash
    curl localhost:8002/metrics
    ```
    ```bash
    curl localhost:8002/api/v1/config/server_config
    ```

#### 3.3 Start third bookie

```bash
mkdir -p data/cluster0-bookkeeper2
mkdir -p logs/cluster0-bookkeeper2
```

```bash
docker run -it --rm -d \
    -v $(pwd)/conf/cluster0/bookkeeper2:/pulsar/conf \
    -v $(pwd)/data/cluster0-bookkeeper2:/pulsar/data/bookkeeper \
    -v $(pwd)/logs/cluster0-bookkeeper2:/pulsar/logs \
    --network ${PULSAR_NETWORK} \
    --name cluster0-bookkeeper2 \
    --hostname cluster0-bookkeeper2 \
    -p 3183:3181 \
    -p 8003:8000 \
    apachepulsar/pulsar-all:2.5.0 \
    bin/pulsar bookie
```

##### Health check

- check docker log
- check http endpoint
    ```bash
    curl localhost:8003/metrics
    ```
    ```bash
    curl localhost:8003/api/v1/config/server_config
    ```

### 4. BookKeeper Cluster Health Check

After all 3 bookies are up running, you can run the following commands to verify if the cluster is running as expected.

```
bin/bookkeeper shell simpletest
```

### 5. Start brokers


```