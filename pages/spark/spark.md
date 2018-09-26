# Read and write from/to CSV

If Spark Version is older than 2.0, this dependency is needed in SBT;
```
"com.databricks"%"spark-csv_2.10"%"1.5.0"
```

Then load the CSV into a DataFrame:
```
# Need to provide a Schame and a File Path
val valdf=sqlContext.read
.format("com.databricks.spark.csv")
.schema(schema)
.option("delimiter",",")
.option("nullValue","")
.option("header","true")
.option("treatEmptyValuesAsNulls","true")
.load(csv_path)
```

To write the DataFrame to a SINGLE CSV file:
```
// Write all to one partition
df.coalesce(1).write.format("com.databricks.spark.csv")
.option("header","true")
.save("mydata.csv")
} 
```

# Writing a JSON DataFrame to Kafka

Writing a RDD or DataFrame to Kafka requires the Producers to be created in each RDD partition

```
  private def writeJSONToKafka(df : DataFrame, topic: String): Unit =
  {
    val props = new util.HashMap[String, Object]()
    props.put("bootstrap.servers","localhost:9092")
    props.put("key.serializer",  "org.apache.kafka.common.serialization.StringSerializer")
    props.put("value.serializer",  "org.apache.kafka.common.serialization.StringSerializer")

    println("Writing JSON to Kafka")

    df.toJSON.foreachPartition((partisions: Iterator[String]) => {
      val producer: KafkaProducer[String, String] = new KafkaProducer[String, String](props)
      partisions.foreach((line: String) => {
          producer.send(new ProducerRecord[String, String](topic,null,line))
      })
    })
  }

```

# Spark Streaming setup

SBT dependencies to be set:

```
libraryDependencies ++= {

  val sparkVer = "2.0.0"
  Seq(
  	"org.apache.spark" %% "spark-streaming" % sparkVer % "provided",
    "org.apache.spark" %% "spark-streaming-kafka-0-8" % sparkVer % "provided",
    "org.apache.spark" %% "spark-core" % sparkVer % "provided" withSources(),
    "org.apache.spark" %% "spark-hive" % sparkVer % "provided"
  )
}
```

Stream set up and processing:

```
   // Initialize spark context
    val conf = new SparkConf().setAppName("My Streaming Processor")
    val ssc = new StreamingContext(conf, Seconds(30))
    ssc.sparkContext.setLogLevel("ERROR")

    // Kafka config
    val kafkaConf = Map(
    // TODO Maybe this one is all we need
    "metadata.broker.list" -> "localhost:9092",
    )

    // Create a stream of Array[Byte] as key and as payload
    val stream = KafkaUtils.createDirectStream[Array[Byte], Array[Byte], DefaultDecoder, DefaultDecoder](
    ssc,
    kafkaConf,
    Set("myTopic")
    )

    // Process each batch of data in another function
    stream.foreachRDD { rdd => processRdd(rdd,config)}

    ssc.start()
    ssc.awaitTermination()
```


# Write a compressed DataFrame

```
  val spark = SparkSession.builder.config(rdd.sparkContext.getConf).getOrCreate()
  spark.sqlContext.setConf("spark.sql.parquet.compression.codec", "snappy")

  // Create the DF and add the partition columns
  val targetDF = spark.sqlContext.createDataFrame(rowData, addPartitions(myTableSchema,partitionsArray))

  // Write to Parquet
  val parquetPath = "/user/victor/mytable"
  targetDF.write.mode("append").partitionBy(partitions : _*).parquet(parquetPath)

```

# Add dynamic partitions to a DataFrame

Adds dynamic partitions to an existing schema, so they can be given by configuration

```
  def addPartitions(schema: StructType, partitions: Array[String]) : StructType = {
    var array = new ArrayBuffer[StructField]()

    // Add previous fields
    schema.foreach(f => array += f)

    // Add new partition fields
    partitions.foreach(p => array += new StructField(p,StringType))

    new StructType(array.toArray)
  }

  val myTableSchema = StructType(Array(StructField("mytimestamp", StringType, true),
    StructField("key", StringType, true),
    StructField("value", DoubleType, true)))   

```

# Compare two rows within two Dataframes/two Parquet Files


```
    // Read the files
    val df1 = spark.read.parquet(path1)
    val df2 = spark.read.parquet(path2)

    // Clean the metadata from the schema
    val schema_df1 = df1.schema.map(_.copy(metadata = Metadata.empty))
    val schema_df2 = df2.schema.map(_.copy(metadata = Metadata.empty))
    println(s"Do schemas match?  ${schema_df1 == schema_df2}")

    // Get a new dataframe with the rows in the second that dont exist in the first
    val diff = df1.except(df2)
    println(s"${diff.count()} rows found to be different")
```