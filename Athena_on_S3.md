# Query using Athena from parquet files on S3

## Step 1: Configure query result location
Point to S3 to save the Athena query results

## Step 2: Create database and table from S3 bucket data
Create AWS Glue Database and Table along with HIVE compatible partitions (e.g. year=YYYY/month=MM/day=DD)
```
CREATE EXTERNAL TABLE IF NOT EXISTS `aws_service_logs`.`cf_access_optimized` (
  `time` timestamp,
  `location` string,
  `bytes` bigint,
  `requestip` string,
  `method` string,
  `host` string,
  `uri` string,
  `status` int,
  `referrer` string,
  `useragent` string,
  `querystring` string,
  `cookie` string,
  `resulttype` string,
  `requestid` string,
  `hostheader` string,
  `requestprotocol` string,
  `requestbytes` bigint,
  `timetaken` double,
  `xforwardedfor` string,
  `sslprotocol` string,
  `sslcipher` string,
  `responseresulttype` string,
  `httpversion` string
)
COMMENT "Table generated from CloudFront log stored as Parquet files on S3"
PARTITIONED BY (
  `year` string COMMENT 'YYYY',
  `month` string COMMENT 'MM',
  `day` string COMMENT 'DD'
)
ROW FORMAT SERDE 'org.apache.hadoop.hive.ql.io.parquet.serde.ParquetHiveSerDe'
STORED AS INPUTFORMAT 'org.apache.hadoop.hive.ql.io.parquet.MapredParquetInputFormat' OUTPUTFORMAT 'org.apache.hadoop.hive.ql.io.parquet.MapredParquetOutputFormat'
LOCATION 's3://cfst-2680-697c7e0b037723458f6ce5792c51c6-s3bucket-elskllmk80wd/'
TBLPROPERTIES ('classification' = 'parquet');
```

## Step 3: Add partition metadata
This is important step otherwise the _count(*)_ would be zero
```
MSCK REPAIR TABLE aws_service_logs.cf_access_optimized
```

## Step 4: Query using Athena
### Counts
```
SELECT count(*) AS rowcount FROM aws_service_logs.cf_access_optimized
```
### Top 10 
```
SELECT * FROM aws_service_logs.cf_access_optimized order by time desc LIMIT 10
```
### Exploratory Analysis using time column
```
SELECT * FROM aws_service_logs.cf_access_optimized WHERE time BETWEEN TIMESTAMP '2018-11-02' AND TIMESTAMP '2018-11-03' LIMIT 10
```
### Aggregation query
```
SELECT SUM(bytes) AS total_bytes
FROM aws_service_logs.cf_access_optimized
WHERE time BETWEEN TIMESTAMP '2018-11-02' AND TIMESTAMP '2018-11-03'
```
