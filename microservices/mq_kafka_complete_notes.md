# Message Queue (MQ) and Kafka -- Complete Interview Notes

## 1. What is a Message Queue (MQ)?

A Message Queue enables asynchronous communication between services.

Flow: Producer → Queue → Consumer

The producer sends messages without waiting for the consumer to process
them.

------------------------------------------------------------------------

## 2. Why Use Message Queues?

-   Decouples services
-   Improves scalability
-   Handles traffic spikes
-   Enables asynchronous processing
-   Improves reliability

Example: Order Service → Queue → Email Service

------------------------------------------------------------------------

## 3. How MQ Works

1.  Producer sends a message
2.  Broker stores message in queue
3.  Consumer reads message
4.  Consumer acknowledges processing
5.  Message removed from queue

------------------------------------------------------------------------

## 4. Components of MQ

-   Producer
-   Queue
-   Broker
-   Consumer
-   Acknowledgment

------------------------------------------------------------------------

## 5. Java Example (Conceptual JMS)

Producer:

``` java
ConnectionFactory factory = new ActiveMQConnectionFactory("tcp://localhost:61616");
Connection connection = factory.createConnection();
Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
Queue queue = session.createQueue("orders");
MessageProducer producer = session.createProducer(queue);
TextMessage message = session.createTextMessage("Order Created");
producer.send(message);
```

Consumer:

``` java
MessageConsumer consumer = session.createConsumer(queue);
Message message = consumer.receive();
System.out.println(((TextMessage) message).getText());
```

------------------------------------------------------------------------

## 6. Problems with Traditional MQ

-   Limited scalability
-   Broker bottleneck
-   Messages deleted after consumption
-   Difficult replay
-   Lower throughput

------------------------------------------------------------------------

## 7. Why Move to Kafka?

Kafka provides: - High throughput - Horizontal scaling - Message
retention - Replay capability - Partitioning - Fault tolerance

------------------------------------------------------------------------

# Kafka Theory

## 8. What is Kafka?

Kafka is a distributed event streaming platform used for real-time
pipelines and microservices.

------------------------------------------------------------------------

## 9. Kafka Architecture

Components: - Producer - Topic - Partition - Broker - Consumer -
Consumer Group

Flow: Producer → Topic → Partition → Consumer

------------------------------------------------------------------------

## 10. Topic and Partition

Partitions allow parallel processing and scalability.

------------------------------------------------------------------------

## 11. Consumer Groups

Each partition is consumed by one consumer in a group. Different groups
can read the same data.

------------------------------------------------------------------------

## 12. Offsets

Each message has an offset to track consumption progress.

------------------------------------------------------------------------

## 13. Kafka vs MQ

  Feature       Traditional MQ   Kafka
  ------------- ---------------- -----------
  Throughput    Medium           Very High
  Scalability   Limited          High
  Retention     No               Yes
  Replay        No               Yes

------------------------------------------------------------------------

## 14. Kafka Producer Code

``` java
Properties props = new Properties();
props.put("bootstrap.servers", "localhost:9092");
props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");

KafkaProducer<String, String> producer = new KafkaProducer<>(props);
ProducerRecord<String, String> record =
        new ProducerRecord<>("orders", "Order Created");

producer.send(record);
producer.close();
```

------------------------------------------------------------------------

## 15. Kafka Consumer Code

``` java
Properties props = new Properties();
props.put("bootstrap.servers", "localhost:9092");
props.put("group.id", "order-group");
props.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");

KafkaConsumer<String, String> consumer = new KafkaConsumer<>(props);
consumer.subscribe(Arrays.asList("orders"));

while (true) {
    ConsumerRecords<String, String> records =
            consumer.poll(Duration.ofMillis(100));

    for (ConsumerRecord<String, String> record : records) {
        System.out.println(record.value());
    }
}
```

------------------------------------------------------------------------

## 16. Most Asked Interview Questions

1.  Difference between Kafka and RabbitMQ?
2.  What is partition?
3.  What is consumer group?
4.  What is offset?
5.  How Kafka ensures ordering?
6.  What happens when broker fails?
7.  What is replication factor?
8.  How Kafka scales?

------------------------------------------------------------------------

## 17. Tricky Interview Questions

1.  Can multiple consumers read same partition?\
    Answer: Only in different consumer groups.

2.  What happens if consumer is slow?\
    Answer: Consumer lag increases.

3.  Why Kafka uses pull model?\
    Answer: Consumers control rate of consumption.

------------------------------------------------------------------------

## 18. Interview Summary

Kafka is a distributed event streaming platform designed for high
throughput, scalability, and fault tolerance.
