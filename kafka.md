# Kafka

## CommitFailedException

### defination

```java
Commit cannot be completed since the group has already rebalanced and assigned the partitions to another member. This means that the time between subsequent calls to poll() was longer than the configured max.poll.interval.ms, which typically implies that the poll loop is spending too much time message processing. You can address this either by increasing max.poll.interval.ms or by reducing the maximum size of batches returned in poll() with max.poll.records.
```

### interpretation

所谓 CommitFailedException，顾名思义就是 Consumer 客户端在提交位移时出现了错误或异常，而且还是那种不可恢复的严重异常. if recoverable, the api call will try with failure in ***commitSync***.

The consumer instance takes longer than the expected (max.poll.interval.ms param) between two ***poll*** methods call. This indicates normally the consumer instance spending too much time process the message.

### general solution

1. increase the value of *max.poll.interval.ms* param.
2. reduce the number of messages fetched per poll request by a smaller param value of *max.poll.records*

### root cause

mostly happened when user explicitly commit manually

1. directly make processing message too long than the max.poll.interval.ms

   to avoid:

   - reduce time of single message process
   - increase the max.poll.interval.ms value (0.10.1.0 + version support)
   - reduce value of max.poll.records (*recommended*)
   - use multi-thread to accelerate message consumption (*complex*)

2. there is another special "standalone consumer (group)". If standalone consumer is using a group id (which other consumer group is using). 



## multi-threading message consumption

### background

Kafka java consumer is designed single-threaded

main thread

heart-beat thread

KafkaConsumer class is not thread-safe.

1. consumer app have multiple threads and each maintain one KafkaConsumer
2. or isolate the message consumption and process functions. Leave message processing to specific thread pool

![img](https://static001.geekbang.org/resource/image/84/0c/84dc0edb73f203b55808b33ca004670c.jpg)

## TCP connection on Consumer side

### Time to create TCP connection

KafkaConsumer class

no connection created (different from producer) when kafkaconsumer class initiated

TCP connection created when poll method is called:

1. FindCoordinnator

   Coordinator驻留在 Broker 端的内存中，负责消费者组的组成员管理和各个消费者的位移提交管理。当消费者程序首次启动调用 poll 方法时，它需要向 Kafka 集群发送一个名为 FindCoordinator 的请求，希望 Kafka 集群告诉它哪个 Broker 是管理它的协调者

2. Connect to Coordinnator

   Broker 处理完上一步发送的 FindCoordinator 请求之后，会返还对应的响应结果（Response），显式地告诉消费者哪个 Broker 是真正的协调者，因此在这一步，消费者知晓了真正的协调者后，会创建连向该 Broker 的 Socket 连接。只有成功连入协调者，协调者才能开启正常的组协调操作，比如加入组、等待组分配方案、心跳请求处理、位移获取、位移提交等。

3. Consuming data

   消费者会为每个要消费的分区创建与该分区领导者副本所在 Broker 连接的 TCP。举个例子，假设消费者要消费 5 个分区的数据，这 5 个分区各自的领导者副本分布在 4 台 Broker 上，那么该消费者在消费时会创建与这 4 台 Broker 的 Socket 连接。

### lifecycle of TCP connection

#### actively close connection

KafkaConsumer.close or kill

#### auto close connection

connection.max.idle.ms param



## Kafka Consumer Monitoring

1. kafka-consumer-group
2. java consumer api
3. jms monitoring from kafka

## Kafka Replication Mechanism

<img src="https://static001.geekbang.org/resource/image/3b/77/3b5f28c6d19b2c6fe592b2b78d3ebc77.jpg" alt="img" style="zoom: 33%;" />

#### Follower Replica doesn't support messaging service.

1. support "Read-your-writes"
2. Monotonic Reads



#### ISR In Sync Replica

the maximum lag follower can fall behind the leader = replica.lag.time.max.ms



#### two kinds of request

1. data request
2. control request

## Rebalance

### Consumer States

#### Empty

#### Dead

#### Preparing Rebalance

#### CompletingRebalance

#### Stable

## Kafka主题管理

### 增删改查

- create: Kafka-topics scripts

  ```shell
  
  bin/kafka-topics.sh --bootstrap-server broker_host:port --create --topic my_topic_name  --partitions 1 --replication-factor 1
  ```

- delete: note it is async action

  ```shell
  
  bin/kafka-topics.sh --bootstrap-server broker_host:port --delete  --topic <topic_name>
  ```

  

- modify: multiple category

  - topic partitions: you can only increase the partition with ***kafka-topics*** scripts

    ```shell
    
    bin/kafka-topics.sh --bootstrap-server broker_host:port --alter --topic <topic_name> --partitions <新分区数>
    ```

    

  - topic param, 

    ```shell
    
    bin/kafka-configs.sh --zookeeper zookeeper_host:port --entity-type topics --entity-name <topic_name> --alter --add-config max.message.bytes=10485760
    ```

    

  - replica param

    use ***kafka-reassign-partitions***

  - bandwidth

  - partition location migration

- read

  ```shell
  bin/kafka-topics.sh --bootstrap-server broker_host:port --list
  ```

### internal topics

- _consumer_offsets
- _transaction_state

### common error

- delete failure
- _consumer_offsets taking too much space



## Dynamic Parameter

### background

the parameter changes in server.property file will not be effective until Broker is restarted, this is heavy cost.

### definition

the parameters that changing take effect without restarting brokers

### common use cases

- adjust size of threadpool on Broker to deal with volatile throughput
- adjust connection and security config
- adjust expiry info for ssl
- adjust performance of Compact Operation
- adjust JMS metric

### common used params

- log.retention.ms
- num.io.threads
- num.network.threads
- ssl.keystore.type/location/password
- ssl.key.password
- num.replica.fetchers



## reset consumer offset

<img src="https://static001.geekbang.org/resource/image/ac/6a/ac093597e8dbef3b1f832f24c125fc6a.jpg" alt="img" style="zoom:25%;" />



## Kafka Authentication

<img src="https://static001.geekbang.org/resource/image/4a/3d/4a52c2eb1ae631697b5ec3d298f7333d.jpg" alt="img" style="zoom:33%;" />



