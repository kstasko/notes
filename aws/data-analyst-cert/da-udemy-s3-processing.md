# "You will need to know this..."  
## Course Material  
[Udemy Class Link](https://www.udemy.com/course/aws-data-analytics/)  
[Sundog Course Material](https://sundog-education.com/aws-certified-big-data-course-materials/)

---  
## AWS Lambda
A way to run code snippets in the cloud - serverless, continuous scaling
- often used to process data as it's moved around

### Lambda Integration
**Why not just run a server**
- server management (patches, monitoring, hardware failures, etc.)  
- servers can be cheap, but scaling gets expensive really fast  
- `you do not pay for processing time you do not use`  
- easier to split up development between front-end and back-end  

`Supported Languages`
( Node.js, Python, Java, C#, Go, Powershell, ruby )

**Lambda & redshift**
- `Best practice for loading data into redshift is the COPY command`
- Lambda can batch up new data and load them with COPY

**Lambda & Kinesis**
> You need to realize that if your batch size for streaming data is too large, that could cause your Lambda service to time out.

`kinesis stream should be in the same account as your lambda`

- Lambda code receives an event with a batch of streams records
    - you specify a batch size when setting up the trigger (up to 10k records)
    - `too large a btach size can cause timeouts!`
    - batches may also be split beyond Lambda's payload limit (6MB)
- Lambda will retry the batch until it succeeds or the data expires
    - this can stall the shard if you do not handle errors properly
    - use more shards to ensure processing is not totally help up by errors
- Lambda processes shard data synchronously

### Costs, Promises and Anti-Patterns
"Pay for what you use"
- generous free tier (1M requests/month, 400k GB-seconds compute time)
- $.20 / million requests

**Other promises**
- High avaiilability
    - no scheduled downtime
    - retries failed code 3 times
- unlimited scalability
    - safety throttle of 1k concurrent executions per region
- high performance
    - new functions callable in seconds
    - events process in milliseconds
    - code is cached automatically
    - 15 minute max timeout

**anti-patterns**
- long-running applications
    - use EC2 instead, or chain functions
- dynamic websites
    - although lambda can be used to develop 'serverless' apps that rely on client-side AJAX
- stateful applications
    - but you can work in dynamodb or s3 to keep track of state


---
## AWS Glue
Serverless discovery and definition of table definitions and schema
- S3 data lakes
- RDS
- Redshift
- most other SQL databases  

**Custom ETL jobs**
- trigger-driven, on a schedule, or on demand
- fully-managed

**Glue crawler / data catalog**
- glue crawler scans data in s3, creates schema
- can run periodically
- populates the glue data catalog
    - stores only table definition
    - original data stays in s3
- once cataloged, you can tread your unstructured data like it's structured
    - redshift spetrum
    - athena
    - EMR
    - quicksight  

**Glue and S3 Partitions**
- Glue crawler will extract partitions based on how your s3 data is oranized
- think up front about how you will be querying your data lake in s3


### Glue, Hive, and ETL
**Glue & Hive**
- Hive lets you run SQL-like queries from EMR
- The Glue Data Catalog can serve as a Hive 'metastore'
- you can also import a Hive metastore into Glue

`MUST KNOW ALL OF GLUE ETL`  
**Glue ETL**
- automatic code generation
- Scala or Python
- Ecryption
    - Server-side (at-rest)
    - SSL (in transit)
- can be event-driven
- can provision additional "DPU's" (data processing units) to increase performance of underlying Spark jobs
    - enabling job metrics can help you understand the max capacity in DPU's you need
- Errors reported to CloudWatch
    - Could tie into SNS for notification

> Glue ETL is a system that lets you automatically process and transform your data. You can do this by a graphical interface that lets you define how you want that transformation to work and it will actually do that using Apcache Spark under the hood using Scala or Python codde, fully encryptd both at rest and in transit. Can be event-driven if you want it to be or it can run on a schedule.

- Transform data, Clean Data, Enrich Data (before doing analysis)
    - generate ETL code in Python or Scala, you can modify the code
    - can provide your own Spark or PySpark scripts
    - Target can be S3, JDBC (RDS, Redshift) or in Glue Data Catalog
- fully managed, cost effective, pay only for the resources consumed
- jobs are run on a serverless spark platform
- glue scheduler to schedule the jobs
- glue triggers to automate job runs based on events

**The Dynamic Frame**
A dataframe is a collection of DynamicRecords
- DynamicRecords are self-describing, have a schema
- very much like a Spark DataFrame, but with more ETL stuff
- Scala and Python APIs

**Glue - Transforamtions**
Bundled Transformations
- DropFields, DropNullFields - remove NULL fields
- Filter - specify a function to filter records
- Join - to enrich data
- Map - add fields, delete fields, perform external lookups  

`Machine Learning Transformations`
- **FindMatches ML** - identify duplicate or matching records in your dataset, even when the reccords do not have a common unique identifier and no fields match exactly

Format conversions: CSV, JSON, Avro, Parquet, ORC, XML
Apache Spark transformations (example: K-means)

**ResolveChoice**
- deals with ambiguities in a DynamicFrame and returns a new one
    - for example, two frames with the same name
- make_cols: creates a new column for each type
- cast: casts all values to specified type
- make_struct_ : creates a structure that contains each data type
- project : projects every type to a given type


### Modifying the Glue Data Catalogue
- ETL scripts can update your schema and partitions if necessary
- adding new partitions
    - re-run the crawler, or
    - have the script use enableUpdateCatalog and partitionKeys options
- updating table schema
    - re-run the crawler, or
    - use enableUpdateCatalog / updateBehavior from script
- creating new tables
    - enableUpdateCatalog / updateBehavior with setCatalogInfo
- Restrictions
    - S3 only
    - JSON, CSV, Avro, Parquet, only
        - Parquer requires special code
    - nested scheams are not supported


### Development Endpoints, Running ETL Jobs with Bookmarks
**Development Endpoints**
- Develop ETL scrpts using a notebook
    - then create an ETL job that runs your script
- Endpoint is in a VPC controlled by security groups, connect via:
    - Apache Zeppelin on your local machine
    - Zeppelin notebook server on EC2 (via Glue console)
    - SageMaker notebook
    - Terminal window
    - PyCharm professional edition
    - Use Elastic IP's to access a private endpoint address

**Running Glue Jobs**
- Time-based schedules (cron style)
- `Job Bookmarks`
    - Persists state from the job run
    - prevents reprocessing of old data
    - allows you to process new data only when re-running on a chedule
    - works with S3 sources in a variety of formats
    - works with relational databases via JDBC (if PK's are in sequential order)
        - `only handles new rows, not updated rows`
- CloudWatch Events
    - Fire off a Lambda function or SNS notification when ETL succeeds or fails
    - Invoke EC2 run, send event to Kinesis, activate a Step Function

### Glue Cost, & Anti-Pattern
**Glue Cost Model**
- Billed by the minute for crawler and ETL jobs
- First million objects stored and accesses are free for the Glue Data Catalog
- Development endpoints for developing ETL code charged by the minute

**Glue Anti-patterns**
- multiple ETL engines
    - Glue ETL is based on Spark
    - If you want to use other engines (Hive, Pig, etc) Data Pipeline EMR would be a better fit

**No longer an Anti-Pattern: Streaming**
- Glue ETL supports serverless streaming ETL
    - consumes from Knesis or Kafka 
    - Clean & transform in-flight
    - Store results into S3 or other data stores  
- Runs on Apache Spark Structured Streaming


### Glue Studio
`For the Exam you really only need to now what it is and what is for`
- visual interface for ETL workflows
- visual job editor
    - create DAG's for complex workflows
    - sources include S3, Kinesis, Kafka, JDBC
    - Transform / sample / join data
    - Target to S3 or Glue Data Catalog
    - Support Partitioning
- Visual Job dashboard
    - overviews, status, run times


### Glue DataBrew

### Glue Elastic Views

### Lake Formation