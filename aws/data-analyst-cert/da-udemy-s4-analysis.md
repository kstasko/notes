### Course Material
[Udemy Class Link](https://www.udemy.com/course/aws-data-analytics/)  
[Sundog Course Material](https://sundog-education.com/aws-certified-big-data-course-materials/)

## Kinesis Analytics
---  
### *Overview*
> another system for querying streams of data continuously, very similar in spirit to `Spark Streaming`

**Inputs**
- `Kinesis Data Streams` or 
- `Kinesis Data Firehose`
- Optionally can store `reference table/data` in `S3`

`Reference Tables`
- inexpensive way to 'join' data for quick lookups

**Real-time Analytics**
- Operates on fixed time periods or windows or time

**Output**
- `Kinesis Data Firehose` or 
- `Kinesis Data Streams`

**KDA & `Lambda`**
- AWS Lambda can be a destination
- Allows lots of flexibility for post-processing
    - Aggregating rows
    - Translating to different formats
    - Transforming and enriching data
    - Encryption
- Opens up access to other services & destinations (S3, DynamoDB, Aurora, Redshift, SNS, SQS, Cloudwatch)

**KDA & `Apache Flink`**
> just know it's an open-source framework for processing data streams

- Formerly Kindesis Data Analytics for Java
    - KDA always used Flink under the hood
    - Now supports Scala as well as Java

- KDA integrates Flink with AWS
    - Instead of SQL, you can develop your own Flink application from scratch and load it into KDA via S3

**Common Use-cases**
- Streaming ETL
- Continuous metric generation
- Responsive analytics

### *Cost*
- Pay only for resources consumed (not cheap)
- Serverless; scales automatically
- Use IAM permissions to access streaming source and destination(s)
- Schema discovery (how your column names are found)

### *RANDOM_CUT_FOREST*
- SQL function used for anomaly detection on numeric columns in a stream
- They are especially proud of this (they wrote a whitepaper about it)
- It is a novel way to identify outliers in a data set 

> This is important because if you ever see a question about trying to detect anomalies or outliers in a stream of data,
> random cut forest with `Kinesis Analytics` is very likely a good answer for that.`

>  What is the Default capacity of the processing application in terms of memory?
> IDA provisions capacity in the form of Kinesis Processing Units (KPU).
> A single KPU provides you with the memory (4 GB) and corresponding computing and networking. 
> The default limit for KPUS for an application is 8.

## Elasticsearch / OpenSearch
---  
### *Overview*

### *Index Management*

### *Designing for Stability*

### *Performance*


---
