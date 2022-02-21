# "You will need to know this..."  
## Course Material  
[Udemy Class Link](https://www.udemy.com/course/aws-data-analytics/)  
[Sundog Course Material](https://sundog-education.com/aws-certified-big-data-course-materials/)

## AWS Lambda
---  
### *Overview*
A way to run code snippets in the cloud - serverless, continuous scaling
- often used to process data as it's moved around

### *Integrations*
**Why not just run a server**
- server management (patches, monitoring, hardware failures, etc.)  
- servers can be cheap, but scaling gets expensive really fast  
- `you do not pay for processing time you do not use`  
- easier to split up development between front-end and back-end  

`Supported Languages`  
( Node.js, Python, Java, C#, Go, Powershell, Ruby )

#### *Lambda & `Redshift`*
- `Best practice for loading data into Redshift is the COPY command`
- Lambda can batch up new data and load them with COPY

#### *Lambda & `Kinesis`*
> If your batch size for streaming data is too large, that could cause your Lambda service to time out.

`kinesis stream should be in the same account as your lambda`

Lambda code receives an event with a batch of streams records
- you specify a batch size when setting up the trigger (up to 10k records)
- `too large a btach size can cause timeouts!`
- batches may also be split beyond Lambda's payload limit (6MB)  

Lambda will retry the batch until it succeeds or the data expires
- this can stall the shard if you do not handle errors properly
- use more shards to ensure processing is not totally help up by errors
- Lambda processes shard data synchronously

### Costs
Pay for what you use
- generous free tier (1M requests/month, 400k GB-seconds compute time)
- $.20 / million requests

### *Promises*
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

### *Anti-Patterns*
**long-running applications** (use EC2 instead, or chain functions)  
**dynamic websites** (although lambda can be used to develop 'serverless' apps that rely on client-side AJAX)  
**stateful applications** (you can work in dynamodb or s3 to keep track of state)

## AWS Glue
---
### *Overview*
Serverless discovery and definition of table definitions and schema
- S3 data lakes
- RDS
- Redshift
- most other SQL databases  

**Custom ETL jobs**
- trigger-driven, on a schedule, or on demand
- fully-managed

**`Glue Crawler` / `Data Catalog`**
- `Glue Crawler` scans data in `S3`, creates schema
- can run periodically
- populates the `Glue Data Catalog`
    - stores only table definition
    - original data stays in `S3`
- once cataloged, you can tread your unstructured data like it's structured
    - redshift spetrum
    - athena
    - EMR
    - quicksight  

**Glue & S3 Partitions**
- Glue crawler will extract partitions based on how your s3 data is oranized
- think up front about how you will be querying your data lake in s3

### *Glue & Hive*
**Glue & Hive**
- Hive lets you run SQL-like queries from EMR
- The Glue Data Catalog can serve as a Hive 'metastore'
- you can also import a Hive metastore into Glue

### *Glue & ETL - MUST KNOW*
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

> Glue ETL is a system that lets you automatically process and transform your data.
> You can do this by a graphical interface 
> that lets you define how you want that transformation to work 
> and it will actually do that using Apcache Spark under the hood 
> using Scala or Python codde, fully encryptd both at rest and in transit.
> Can be event-driven if you want it to be or it can run on a schedule.

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
- Develop ETL scripts using a notebook
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
- Transient clusters terminate once all steps are complete
    - Loading data, processing, storing - the shut down
    - Saves money
- Long-running clusters must be manually terminated
    - Basically a data warehouse with periodic process on large datasets
    - Can spin up task nodes using Spot instances for temporary capacity
    - Can use reserved instances on long-running clusters to save $
    - Termination protection on by default, auto-termination off

- Frameworks and applications are specified at cluster launch
- Connect directly to master to run jobs directly
- Or, submit ordered steps via the console
    - Process data in S3 or HDFS
    - Output data to S3 or somewhere
    - Once defined, steps can be invoked via the console

### EMR AWS Integration
- EC2 - for the instances that comprise the nodes in the cluster
- VPC - to configure the virtual network in which you launch your instances
- S3 - to store input and output data
- CloudWatch - to monitor cluster performance and configure alamrs
- IAM - to configure permissions
- CloudTrail - to audit requests made to the service
- Data Pipeline - to schedule and start your clusters

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

> generally speaking if you need a temporary increase in processing capacity adding tasks nodes is a good way to do that.
> If you need to add a storage capacity as well on HDFS you can resize a core node.
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
- supports Java, Scala, Python & R
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
- uses familiar SQL syntax ( HiveQL )
- Scalable - works with 'big data' ( really most appropriate for data warehouse applications )
- Easy OLAP queries

The Hive Metastore
- Hive mainains a 'metastore' that imparts a structure you define on the unstructured data that is stored on HDFS 
External Hive Metastores
- Metastore is stored in MySQL on the master node by default
- External metastore offer better resiliency / integration (Glue Data Catalog, RDS)

### Pig on EMR
- writing mappers and reducers by hand takes a long time
- Pig introduces *Pig Latin* a scripting language that lets you use SQL-like syntax to define your map and reduce steps
- highly extensible with user-defined functionality

#### *AWS Integration*
- ability to use multiple file systems (not just HDFS)
- load JAR's and scripts from S3

> basically a higher level alternative to writing MapReduce code not quite SQL

### HBASE on EMR
- non-relational, petabyte-scale database
- based on Google's BigTable, on top of HDFS
- in-memory
- hive integration

*Sounds a lot like DynamoDB*  
- both are NoSQL datbases intended for the same sort of things
- but if you're all-in with AWS anyhow, DynamoDB has advantages
    - fully managed
    - more integrations with other AWS services
    - Glue integration
- HBase has some advantages though:
    - efficient storage of sparse data
    - appropriate for high frequency counters (consistent reads & writes)
    high write & update throughput
    - more integration with Hadoop

*Hbase / AWS integration*
- can store data on S3 via EMRFS
- can back up to S3

### Presto on EMR
#### *Overview*
- can connect to many different 'big data' databases and data stores at once and query across them
- **interactive** queries at **petabyte scale**
- SQL style
- optimized for OLAP 
- developed and still partially maintained by Facebook
- this is what Athena uses under the hood
- expoes JDBC, Command-Line and Tableau interfaces

#### *Connectors*
HDFS , S3 , Cassandra , MongoDB , HBase , SQL , Redshift , Teradata

> It's very fast and easy to get Presto up and running with EMR now in
> Presto all the processing is done in memory and pipelined across the network in between stages.
> This avoids any unecessary IO overhead and that is why it is so fast.

### Zeppelin and EMR Notebooks
- It's a hosted notebook on your cluster that allows you to interactively run Python scripts and code against your data that is stored on your cluster
- it can interleave with nicely formatted notes
- allows you to share these notebooks with other people that are using your cluster.

Spark, Python, JDBC, HBase, Elasticsearch + more

**Zeppelin + Spark**
- can run Spark code interactively
    - this speeds up your development cycle
    - allows easy experimentation and exploration
- can execute SQL queries directly against SparkSQL
- Query results may be visualized in charts and graphs
- Makes Spark feel more like a data science tool

**EMR Notebook**
- Similar concept to Zeppelin with more AWS Integration
- Notebooks backed up to S3
- `Provision clusters from the notebook`
- Hosted inside a VPC
- Accessed only via AWS console

### Hue, Splunk & Flume
> The important things here are what each technology does and how it integrates with AWS.

**Hue**
- Hadoop User Experience
- Graphical front-end for your applications on your EMR cluster
- IAM integration
- S3: can browse & move data between HDFS and S3

`Remember Hue is a management tool, front-end console for you entire EMR console`

**Splunk**
- Splunk / Hunk / 'makes machine data acessible, usable and valuable to everyone'
- `operational tool - can be used to visualize EMR and S3 data using your EMR Hadoop cluster

**Flume**
- another way to stream data into your cluster
- made from the start with Hadoop in mind
    - built-in sinks for HDFS and HBase
- orignally made to handle log aggregation

> a distributed reliable and available service that allows you to efficiently collect aggregate and move large amounts of log data in particular

> a way of streaming raw data into a cluster

**MXNet**
- like Tensorflow, a library for building accelerating neural networks
- included on EMR

> MXNet is a framework that is used to build depp learning applications

### S3DistCP & other Services
**S3DistCP**
- tool for copying large amounts of data
    - from S3 into HDFS or vice versa
- uses MapReduce to copy in a distributed manner
- suitable for parallel copying of large numbers of objects
    - across buckets, across accounts

### EMR Security and Instance Types
**Security**
- IAM policies
    - grant or deny permissions
    - allow user actions
    - combine with tagging to control access per cluster
- Kerberos
    - secure user authentication
- SSH
    - Secure connection to command line
    - Tunneling for web interfaces
    - can use Kerberos or EC2 key pairs
- IAM roles
    - control access to EMRFS data based on user, group, location of data
    - each cluster must have a service role and a role for the EC2 instance profile
    - IAM policies attached to roles
    - auto-scaling role
    - service-linked roles

Block public access
- easy way to prevent public access to data stored on your EMR cluster
- can set at the account level before creating the cluster

**Instance Types**  
`master node`:
- m5.xlarge if < 50 nodes, larger if > 50 nodes  

`nore & task nodes`:
- m5.xlarge is usually good
- if cluster waits a lot on external dependencies, t2.medium
- improved performance: m4.xlarge
- computation-intensive applications: high CPU instances
- database, memory-caching applications: high memory instances
- network / CPU-intensive (NLP, ML) - cluster computer instances

`spot instances`
- good choice for task nodes
- only use on core * master if you are testing or very cost-sensitive; you are risking partial data loss

## Data Pipeline
---  
**Overview**
> basically lets you schedule tasks for processing your big data
a web service that helps you relibaly process and move data between different AWS compute and storage services at specified intervals.

- destinations include S3, RDS, DynamoDB, Redshift and EMR
- manages task dependencies
- retried and notifies on failures
- cross-region pipelines
- precondition checks
- data sources may be on-premises
- highly available

**Activities**
> There are several different activities that a data pipeline can do and an activity is an an action that AWS data pipeline initiates on your behalf as part of a pipeline.

- EMR
- Hive
- Copy
- SQL
- Scripts

## Step Functions
---
### *Overview*
> It's purpose in life is to let you design workflows throughout AWS

- design workflows
- easy visualizations
- advanced error handling and retry mechanism outside the code
- audit of the history of workflows
- ability to 'Wait' for an arbitrary amount of time
- Max execution time of a State Machin is 1 year


