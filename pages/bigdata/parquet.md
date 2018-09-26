## Manual Parquet generation with Kite SDK

Installation:

```
curl http://central.maven.org/maven2/org/kitesdk/kite-tools/1.1.0/kite-tools-1.1.0-binary.jar -o kite-dataset
chmod +x kite-dataset
```

Create the files´ schema:

```
{
  "type" : "record",
  "name" : "GenericCell4gHdfs",
  "namespace" : "com.mydomain.mypojo",
  "doc" : "Sample object",
  "fields" : [ {
    "name" : "timestamp",
    "type" : "string"
  }, {
    "name" : "value",
    "type" : [ "null", "double" ],
    "default" : null
  }, {
    "name" : "longValue",
    "type" : "long"
  }, {
    "name" : "intValue",
    "type" : "int"
  } ]
}

```

*Create the partitions`definition:*
With this JSON:
```
[ {
  "name" : "year",
  "source" : "timestamp",
  "type" : "year"
}, {
  "name" : "month",
  "source" : "timestamp",
  "type" : "month"
}, {
  "name" : "day",
  "source" : "timestamp",
  "type" : "day"
} ]
```

And then invoke:

```
kite-dataset  partition-config timestamp:year timestamp:month timestamp:day -s schema.txt -o partitions.txt
```

*Create the dataset*

```
 kite-dataset create dataset:hdfs:/victor/mypojo --schema edos_schema.txt  --format parquet -p partitions.txt
```

*Finally, Import the data from a CSV*

```
kite-dataset csv-import bigdata.csv dataset:hdfs:/victor/mypojo --use-hdfs
```