# Structured Streaming Example

*Producer process used for testing*
```
netstat -c | grep tcp | kafka-console-producer --topic topic2345 --broker-list [KAFKA HOST]:9092
```

Libraries and command line hacks used:

```
export SPARK_KAFKA_VERSION=0.10
```
(see [this](https://community.cloudera.com/t5/Data-Ingestion-Integration/KafkaConsumer-subscribe-0-9-vs-0-10-in-Structured-streaming/td-p/60161))


SBT dependencies

libraryDependencies ++= {

  val sparkVer = "2.1.0"
  Seq(
    "org.apache.spark" %% "spark-core" % sparkVer % "provided" withSources(),
    "org.apache.spark" %% "spark-streaming" % sparkVer % "provided",
    "org.apache.spark" %% "spark-sql" % sparkVer % "provided",
    "org.apache.spark" % "spark-sql-kafka-0-10_2.11" % sparkVer,
    "org.apache.kafka" % "kafka-clients" % "0.10.0.1"
  )
}

Spark Submit command

```
spark2-submit  --class com.ericsson.streaming.structured.StructuredStreamingMain --packages org.apache.spark:spark-sql-kafka-0-10_2.11:2.1.0,org.apache.kafka:kafka-clients:0.10.0.1 sparkstructuredstreamingconsumer_2.11-0.1.jar topic2345 172.29.45.149:9092 WARN
```


