## Reproducer startup up issue

This the standard MQTT quickstart.

With the difference that all beans are annotated with `@IfBuildProfile("foo")`.

### Running

Start mosquitto broker:

```shell
podman-compose up
```

Then, start the application:

```shell
mvn quarkus:dev
```

### Results

Quarkus starts and doesn't send messages. However, in the startup logs one can see:

```text
2022-12-13 16:01:39,953 WARN  [io.sma.rea.mes.provider] (Quarkus Main Thread) SRMSG00207: Some components are not connected to either downstream consumers or upstream producers:
        - IncomingConnector{channel:'prices', attribute:'mp.messaging.incoming.prices'} has no downstream
        - OutgoingConnector{channel:'topic-price', attribute:'mp.messaging.outgoing.topic-price'} has no upstream
```

Also, in the MQTT broker logs, one can find:

```text
1670943779: New connection from 10.89.2.9 on port 1883.
1670943779: New client connected from 10.89.2.9 as 42cac017-9a80-4b81-ba07-98f77c84027b (p2, c1, k30).
1670943779: No will message specified.
1670943779: Sending CONNACK to 42cac017-9a80-4b81-ba07-98f77c84027b (0, 0)
1670943779: Received SUBSCRIBE from 42cac017-9a80-4b81-ba07-98f77c84027b
1670943779:     prices (QoS 0)
1670943779: 42cac017-9a80-4b81-ba07-98f77c84027b 0 prices
1670943779: Sending SUBACK to 42cac017-9a80-4b81-ba07-98f77c84027b
```

So it looks like that beans is still somewhat active. Although they should have been disabled during build time.

### Expected

* The beans should not be active.
* No MQTT connection should be established.
* No warning about "has no downstream" should be shown.
