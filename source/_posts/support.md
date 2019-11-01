---
layout: golang-kafka
title: Use kafka in Golang
date: 2019-11-01 10:48:20
categories: golang
---



# kafka version

kafka_2.11-2.3.1

Follow the guide to start kafka server, on this version 2.3.1,
kafa is using zookeeper service to maintain meta-data information.



## start zookeeper and kafka service in local

```
bin/zookeeper-server-start.sh config/zookeeper.properties

bin/kafka-server-start.sh config/server.properties

create a topic:s
bin/kafka-topics.sh --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic test

list topics
bin/kafka-topics.sh --list --bootstrap-server localhost:9092

bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test

bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning
```


we can produce messages

```
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test
```

## consumer groups

each group can consume same messages at the same time, this could be widely used in a lot of applications,


```
sales group for example,

./bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --group "sales-group1" --from-beginning

a finance group for example,

/bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --group "finance-group1"

a tech support group for example,
./bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --group "techsupport-group1" --from-beginning

```

if we want to reset offset of one consumer group, messages can be re-consumed again, for example,

```
./bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --topic test --group "sales-group1" --reset-offsets --to-earliest --execute
```


## About further understanding of kafka topic and partitions

references
```
https://dzone.com/articles/apache-kafka-topic-architecture-amp-partitions
```

# Kafka in Golang

opensources

	"github.com/Shopify/sarama"
	
	
	```
		cfg := sarama.NewConfig()

	switch os.Getenv("KAKFA_APIVERSION") {
	case "V0_11_0_0":
		cfg.Version = sarama.V0_11_0_0
	default:
		cfg.Version = sarama.V1_0_0_0
	}

	c, err := sarama.NewClient([]string{d.addr}, cfg)
	
	```
	
	
	```
		d.producerWithPartition.Input() <- &sarama.ProducerMessage{
		Topic:     l.Topic,
		Key:       sarama.StringEncoder(l.Key),
		Partition: int32(getPartition(l.Key, num)),
		Value:     sarama.StringEncoder(l.Data),
		Headers:   header,
	}
	```


the other good opensource is 
```
	"github.com/segmentio/kafka-go"
```

