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
- a visual data prep tool
    - UI for pre-processing large data sets
- Over 250 ready-made transformations
- You create 'recipes' of transformations taht can be saved as jobs within a larger project
- Security
    - integrates with KMS
    - SSL in transit
    - IAM can restrict who can do what
    - Cloudwatch & CloudTrail

### Lake Formation
> Makes it easy to set up a secure data lake in days

- loading data & monitoring data flows
- setting up partitions
- encryption & managing keys
- defining transformation jobs & monitoring them
- access control
- auditing
- built on top of AWS Glue

> Anything we have talked about Glue doing, Lake Formation can do it too.

No cost for Lake Formation itself, only for the services underneath used

The Finer Points
- cross-account Lake FOrmation permission
    - recipient must be set up as a data lake admin
    - can use AWS Resource Access Manager for acounts external to your org
    - IAM permissions for cross-account access
- Lake FOrmation does not support manifests in Athena or Redshift queries
- IAM permissions on the KMS encryption key are needed for encrypted data catalogs in Lake Formation
- IAM permissions needed to create blueprints and workflows

## Elastic MapReduce (EMR)
--
### EMR Architecture
- Managed Hadoop framework on EC2 instances
- includes Spark, HBase, Presto, Flink, Hive & more
- EMR notebooks
- Several integration points with AWS

> Instead of having this opaque system like you did with Glue ETL, where you do not see the servers. 
> If you want to get down and dirty and actually have low-level access to Apache Spark and want to 
> be able to have explicit control over the resources that it has, EMR might be for you

EMR Cluster
> An EMR cluster fundamentally is just a collection of EC2 instances that are running Hadoop. Each one of these instances is called a node.

**Master Node** - manages the cluster
- tracks status of tasks, monitors cluster health
- single EC2 instance (it can be a single node cluster even)

**Core Node** - Hosts Hadoop Distributed File System (HDFS) data and runs tasks
- can be scaled up & down, but with some risk
- multi-node clusters have at least one

**Task Node** - runs tasks, does not host data
- optional
- no risk of data loss when removing
- good use of spot instances

`A good way to expand your cluster dynamically is by using a spot instance on a task node`

### EMR Usage
Transient vs Long-Running Clusters
- Transient clubsters terminate once all steps are complete
    - Loading data, processing, storing - the shut down
    - Saves money
- Long-running clusters must be manually terminated
    - Basically a data warehouse with periodic process on large datasets
    - Can spin up task nodes using Spot instances for temporary capacity
    - Can use reserved instances on long-running clusters to save $
    - Termination protection on by default, auto-termination off

- Frameowrks and applications are specified at cluster launch
- Connect directly to master to run jobs directly
- Or, submit ordered steps via the console
    - Process data in S3 or HDFS
    - Output data to S3 or somewhere
    - Once defined, steps can be invoked via the console

### EMR AWS Integration
- EC2 for the instances that comprise the nodes in the cluster
- VPC to configure the virtual network in which you launch your instances
- S3 to store input and output data
- CloudWatch to monitor cluster performance and configure alamrs
- IAM to configure permissions
- CloudTrail to audit requests made to the service
- Data Pipeline to schedule and start your clusters

### EMR Storage
Hadoop Distributed File System (HDFS)
- Multiple copies stored across cluster instances for redundnacy
- Files stored as blocks (128 MB default size)
- `Ephemeral - HDFS data is lost when cluster is terminated`
    - useful for caching intermediate results or workloads with significant random I/O
- Hadoop tries to process data where it is stored on HDFS

EMRFS: access S3 as if it were HDFS
- allows persistent storage after cluster termination
**EMRFS Consistent View** - Optional for S3 consistency
    - Uses DynamoDB to track consistency
    - May need to tinker with read/write capacity on DynamoDB
- Strongly Consistent

Local File system
- suitable only for temporary data (buffer, caches, etc)

Elastic Block Store for HDFS
- allows use of EMR on EBS-only types (M4, C4)
- Deleted when cluster is terminated
- EBS volumes can only be attached when launching a cluster
- If you manually detach an EBS volume, EMR treats that as a failure and replaces it

> If you do want persistent storage that's going to last after your cluster is terminated, you have to use the EMRFS with S3

### EMR Promises; Intro to Hadoop
- EMR charges by the hour + EC2 charges
- Provisions new nodes if a core node fails
- Can add and remove tasks nodes on the fly
    - Increase processing capacity, but not HDFS capacity
- Can resize a running cluter's core nodes
    - Increase both processing and HDFS capacity
- Core nodes can also be added or removed
    - But removing risks data loss

> generally speaking if you need a temporary increase in processing capcity adding tasks nodes is a good way to do tha.
> If you need to add a storage capcity as well on HDFS you can resize a core node.
> You can also add and remove core nodes on the fly just like you can with task nodes.

Managed Scaling
- EMR Automatic Scaling
    - the wold way of doing it
    - Custom scaling rules based on CloutWatch metrics
    - Supports instance groups only
- EMR Managed Scaling
    - Support instance groups and instance fleets
    - Scales spot, on-demand, and instances in a Savings Plan within the same cluster
    - Available for Spark, Hive, YARN workloads
- Scale-up Strategy
    - First adds core nodes, then task nodes, up to max units specified
- Scale-down Strategy
    - First remove task nodes, then core nodes, no further than min constraints
    - Spot nodes always removed before on-demand instances

`Hadoop common / core = MapReduce, YARN, HDFS`

Maybe come back to this slide

### Apache Spark Intro
- distributed processing framework for big data
- in-memory caching, optimized query execution
- supports Java, Scala, Python, & R
- Supports code reuses across
    - Batch processing
    - Interactive Queries
        - Spark SQL
    - Real-time Analytics
    - Machine Learning
        - MLLib
    - Graph Processing
- Spark Streaming
    - Integrated with Kinesis, Kafka, on EMR
- Spark is NOT meant for OLTP

### Hive on EMR
> The important thing to know is that Hive basically exposes SQL interfaces to your underlying data stored on your EMR cluster.

Why Hive
- uses familiar SQL syntax (HiveQL)
- Scalable - works with 'big data' (really most appropriate for data warehouse applications)
- Easy OLAP queries

The Hive Metastore
- Hive mainains a 'metastore' that imparts a structure you define on the unstructured data that is stored on HDFS 
External Hive Metastores
- Metastore is stored in MySQL on the master node by default
- External metastore offer better resiliency / integration (Glue Data Catalog, RDS)

### Pig on EMR

### HBASE on EMR

### Presto on EMR

### Zeppelin and EMR Notebooks

### Hue, Splunk & Flume