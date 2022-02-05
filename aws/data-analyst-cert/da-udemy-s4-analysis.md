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

---  

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

## Athena
---
### *Overview*
- Interactive query service for `S3`
    - No need to load data, it stays in `S3`
- Presto under the hood
- Serverless
- `Supports many data formats`
    - CSV (human readable)
    - JSON (human readable)
    - ORC (columnar, splittable)
    - Parquet (columnar, splittable)
    - Avro (splittable)
- Unstructured, semi-structured, or structured

### *Athena & `Glue`*
> Anytime Athena sees something in your `Glue Data Catalog` in your account,
> it is going to make a table for that for you.

Cross-region concerns:
- `Athena` cannot query across regions on its own BUT a `Glue Crawler` can
    - you can query `S3` data across regions if you query a `Glue Data Catalog` in the same region as `Athena`
        - And the `Glue Crawler` that created the data catalog spanned multiple regions

`Workgroups`
- can organize users / teams / apps / workkloads into `Workgroups`
- can control query access and track costs by `Workgroup`
- integrates with `IAM`, `Cloudwatch`, `SNS`
- Each `Workgroup` can have its own:
    - Query history
    - Data limits ( you can limit how much data queries may scan by `workgroup`)
    - `IAM policies`
    - Encryption Settings

### *Costs*
Pay-as-you-go
- $5 per TB scanned
- Successful or cancelled queries count, `failed queries do not`
- No charge for DDL (CREATE/ALTER/DROP, etc)

`Save up to 30% - 90% by using columnar formats & get better performance`
- ORC, Parquet

`Glue` & `S3` have their own charges

### *Security*
Access Control
- IAM, ACLs, S3 bucket policies
- AmazonAthenaFullAccess / AWSQuicksightAthenaAccess

Encrypt results at rest in S3 staging directory
- Server-side encryption with S3-managed keys (`SSE-S3`)
- Server-side encryption with KMS key (`SSE-KMS`)
- Client-side encryption with KMS key (`CSE-KMS`)

Cross-account access in S3 bucket policy possible  
Transport Layer Security (TLS) encrypts in-transit (between `Athena` and `S3`)

### *Anti-Patterns*
- Highly formatted reports / visualizations (use `Quicksight`)
- ETL (use `Glue`)

### *Performance*
### *Optimizing Performance*
- Use columnar data (ORC, Parquet)
- Small number of large files performs better than large number of small files
- Use partitions
    - If adding partitions after the fact, use `MCSK REPAIR TABLE` command


## Redshift
---
### *Overview*

### *Spectrum*

### *Performance Testing*

### *Durability*

### *Scaling*

### *Distribution Styles*

### *Sort Keys*

### *Data Flows & the COPY Command*

### *Integration*

### *WLM*

### *Vacuum*

### *Anti-Patterns*

### *Resizing & new Features*

### *Security Concerns*
