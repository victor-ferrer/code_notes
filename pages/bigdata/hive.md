## Hive DDL Manual
https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

## Hive tables over Snappy Parquet

Useful script for creating partitioned, snappy-based, Hive tables.

```
drop table mytable;

CREATE EXTERNAL TABLE `mytable`(
	  `mytimestamp` string, 
	  `key` string, 	  
	  `value` double, 
	PARTITIONED BY ( 
	  `year` int, 
	  `month` int, 
      `day` int)
  STORED AS PARQUET TBLPROPERTIES ("parquet.compression"="SNAPPY");
  
alter table mytable set location 'hdfs://[HDFS_HOST]:8020/user/victor/mytable';
  
msck repair table mytable;
  
select * from mytable limit 100;
```

Notes

 - Data should be stored in Snappy Parquet files and respecting the defined partitioning
 - See [this example](../spark/spark.md#add-dynamic-partitions-to-a-dataframe), where a DataFrame writes compressed data.