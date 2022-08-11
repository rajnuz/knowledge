# Kafka 命令

[toc]

## 查看全部topic

```shell
kafka-topics.sh --bootstrap-server localhost:9092 --list
```

## 描述某个topic 可以看到partion的情况

```shell
kafka-topics.sh --describe --bootstrap-server localhost:9092 --topic ${topic name}
```

## 查看所有的consumer group

```shell
kafka-consumer-groups.sh --bootstrap-server localhost:9092 --list
```

## 描述某个consumer，可以查看到每个topic的消费状态

```shell
kafka-consumer-groups.sh  --bootstrap-server localhost:9092 --group ${groupId} --describe
```

## 删除topic

```shell
kafka-topics.sh --delete --bootstrap-server localhost:9092 --topic ${topic name}
```