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
- a fork of Elasicsearch & Kibana
- a search engine
- an analysis tool
- a visualization tool ( Dashboards = Kibana )
- a data pipeline - `Kinesis` replaces Beats & Logstash

> Fundamentally, `OpenSearch` is a scalable version of `Lucene` and
> is distributed horizontally across many nodes in a cluster

### *Opensearch Applications*
- full-text search
- log analytics
- application monitoring
- security analytics
- clickstream analytics

### *OpenSearch Concepts*
- `Documents` - things you are searching for. They can be more text -
any structured JSON data works. Every document has a unique ID and a type
- `Types` - defines the schema and mappng shared by documents that represent the same sort of thing. (being deprecated)
- `Indices` - an index powers search into all documents within a collection of types. THey contain inverted indices taht let you search across everything within them at once.

> `Types` are going to be a thing of the past soon.
> So you really want to think about `Documents` & `Indexes`. 
> But if you see something on the exam about `Elasticsearch 6` still,
> types still might be a thing in that context.  

### *An index is split into `shard`*  
Documents are `hashed` to a particular `shard`
- each `shard may be on a different `node` in a `cluster`
- every `shard` is a self-contained `Lucene` index of its own

### *Amazon Opensearch Service*
> So far we have talked about what `Opensearch` shares in common with `Elasticsearch`, their common fundamental architecture. 
> But let's talk about what is specific to the `Amazon Opensearch Service`.

- fully-managed service (but not serverless)
- scale up or down witout downtime - `NOT` automatic
- pay fo what you use ( instance-hours, storage, data transfer )
- network isolation

### *Amazon OpenSearch Options*
- dedicated master node(s) - choice of count and instance types
- `Domains` - collection of all the configuration for the cluster as a whole
- snapshots to `S3`
- zone awareness

### *Amazon Opensearch Security*
- resource-based policies
- identity-based policies
- IP-based policies
- request signing
- `VPC`
- Cognito

### *Anti-Patterns* - Whitepaper available
- OLTP - no transactions, RDS or DynamoDB is better
- ad-hoc data querying - `Athena` is better

### *Index Management*
**cold / warm/ ultrawarm / hot storage**
- standard data nodes use `hot` storage - instance stores or EBS volumes / fastest performance
- `ultrawarm` (or warm) storage uses `S3` & caching
    - best for indices with few writes ( like log data / immutable data )
    - slower performance but much lower cost
    - must have a dedicated master node
- `cold` storage
    - also uses `S3` and is even cheaper
    - for "periodic research or forensic analysis on older data"
    - must have dedicated master and have `ultrawarm` enabled too
    - not compatible with T2 or T3 instancee types on data nodes
    - if using fine-grained access control - must map users to cold_manager role in `OpenSearch` Dashboards
- data may be migated between different storage types  

**Index Rollups**
- periodically roll up old data into summarized indices
- saves storage costs
- new index may have fewer fields, coarser time buckets

**Index Transforms**
- like rollups, but purpose is to create a different view to analyze data differently
- groupings and aggregations

**Cross-cluster Replication**
- replicates indices / mappings / metadata across domains
- ensures high availability in an outage
- replicated data geographically for better latency
- `Follower` index pulls data from `leader` index
- requires fine-grained access control and node-to-node encryption  

`Remote Reindex` - allows copying indices from one cluster to another on demand

### *Index State Management*
- automates index management policies  

examples
- delete old indices after a period of time
- move indices into read only state after a period of time
- move indices from hot -> ultrawarm -> cold storage over time
- reduce replica count over time
- automate index snapshots

ISM policies are run every 30-48 minutes 
- random jutter to ensure they do not all run at once
- can even send notifications when done  

### *Designing for Stability*
- Three dedicated master nodes is best - avoids split brain if you use two
- do not run out of disk space
    - minimum storage requirement is roughly:  
    `Source Data * ( 1 + Number of Replicas ) * 1.45`
- Chosing the number of shards
    - ( Source Data + Room to Grow ) * ( 1 + indexing overhead ) / desired shard size
    - in rare cases you may need to limit the number of shards per node - you usually run out of disk space first
- Choosing instance types
    - at least 3 nodes
    - mostly about storage requirements

### *Performance*
- `Memory Pressure` in the JVM can result if:  
    - you have unbalanced shard allocations across nodes
    - you have too many shards in a cluster
- Fewer shards can yield better performance if `JVMMemoryPressure` errors are encountered
    - Delete old or unused indices

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
- fully-managed, petabyte scale data warehouse service
- 10X better performance than other DW's
    - via ML, massively parallel query execution, columnar storage
- Designed for OLAP, not OLTP
- cost effective
- SQL, ODBC, JDBC interfaces
- scale up or down on demand
- built-in replication & backups
- monitoring via `CLoudWatch` / `CloudTrail`

**Use Cases**
- acceleate analytics workloads
- unified data warehouse & data lake
- data warehouse modernization
- analyze global sales data
- store historical stock trade data
- analyze ad impressions & clicks
- aggregate gaming data
- analyze social trends

**Architecture**  
- `cluster` - the core infrastrucute component of an `Amazon Redshift` data ware house 
    - composed of a `leader node` and one or more compute nodes ( `can have up to 128 compute nodes` )
    - each cluster can contain one or more databases
- `leader node` - manages communication with the client programs and with the compute nodes. 
    - receives all the queries from client applications, passes the queries and develops `execution plans`
        - `execution plans` - an ordered set of steps to process queries
- `compute node` - responsible for executing the steps specified in the execution plans that it is getting from the leader node and transmitting data mong themselves to serve those queries
    - each compute node has its own dedicated CPU, memory and attached disk storage which are determined by the node type you choose
- `Node Types`
    - Dense Storage - allows you to create a very large data warehouse using hard disk drives
        - sizes ( *extra-large* & *8xl* )
        - 16 TB of available storage
    - Dense Compute - to create very high performance data warehouses using fast CPUs large amounts of RAM and SSD
        - much much much more powerful and storage comparatively

> Every `compute node` is divided into `slices` and a portion of the nodes memory and
> disk space is going to be allocated to each slice where it processes a portion of the workload
> assigned to that node. 
> The number of slices per node is determined by the node size of the cluster.

### *Spectrum*
- query exabytes of unstructured data in `S3` without loading
- limitless concurrency
- horizontal scaling
- separate storage & compute resources
- wide variety of data formats
- support of Gzip and Snappy compression

> Much the same way that `Athena` could use the `AWS Glue Data Catalog` to make tables on top of your `S3` data,
> `Spectrum` can do the same thing, it is just that instead of having this console based query SQL engine in `Athena` it actually just looks like another table in your redshift database.

### *Performance Testing*
- massively parallel processing (MPP)
- columnar data storage
- column compression
- when loading to the cluster, use the COPY command ( automatically applies compression )

> `Redshift` uses a block size of ` megabyte which is more efficient and further reduces the number of IO requests needed to perform any database loading or other operations that are part of a query execution.

### *Durability*
- replication within cluster
- backup to `S3` - asynch replicated to another region
- automatd snapshots
- failed drives / nodes automatically replaced
- limited to a single availability zone 

### *Scaling*
- vertical and horizontal scaling on demand
- during scaling:
    - a new cluster is created while your old one remains available for reads
    - CNAME is flipped to new cluster ( a few minuts of downtime )
    - data moved in parallel to new compute nodes

### *Distribution Styles*
> the two primary golas of data distribution are to distribute the workload uniformly among the nodes in the cluster 
> and to minimize data movement during query execution

- `AUTO` - redshift configures it out based on size of data
- `EVEN` - rows distributed across slices in round-robin
    - appropriate when a table does not participate in joins or when the there is not a clear choice between key distributions or all distribution
- `KEY` - rows distributed based on one column
    - useful if you going to be doing queries based on a specific column in your data 
- `ALL` - entire table is copied to ever node
    - it takes much longer to load, update or insert data 
    - meant for tables that are not updated frequently or extensively as the cost of redistribution is low

### *Sort Keys*
- rows are stored on disk in sorted order based on the column you designate as a sort key
- like an index
- makes for fast range queries
- choosing a sort key ( recency? filtering? joins? )
- `single sort-key` - using a single column, a single value, to sort the data
- `compound sort-key` ( default ) - made up of all the columns listed in the sort key definition in the order they are listed in.
    - most useful when a query's filter applies conditions such as filters and joins that use a prefix of the sort keys  
- `interleaved sort-key` - gives equal weights to a each column or subset of columns in the sort key

### *Data Flows & the COPY Command*
**Importing / Exporting Data**
- COPY command 
    - parallelized; efficient
    - From `S3`, `EMR`, `DynamoDB`, remote hosts
    - `S3` requires a manifest file and IAM role
- UNLOAD command
    - unload from a table into files in `S3`
- enhanced `VPC` routing

**COPY command**
- use COPY to load large amounts of data from outside of `Redshift`
- if your data is already in `Redshift` in another table...
    - use INSERT INTO ... SELECT
    - or CREATE TABLE AS
- COPY can decrypt data as it is loaded from `S3`
    - hardware-accelerated SSL used to keep it fast
- Gzip, Izop and bzip2 compression supported to speed it up further
- automatic compression option
    - analyzes data being loaded and figures out optimal compression scheme for storing it
- `special case`: narrow tables ( lots of rows, few columns )
    - load with a sinlge COPY transaction if possible
    - otherwise hiddne metadata columns consume too much space

**Redshift copy grants for cross-region snapshot copies**  
- let's say you have a KMS-encrypted `Redshift Cluster` and a snapshot of it
- you want to copy that snapshot to another region for backup
- in the destination AWS region:
    - create KMS key if you do not have one already
    - specify a unique name for your snapshot copy grant
    - specify the KMS key ID for which you are creating the copy grant
- in the source AWS region:
    - enable copying of snapshots tot eh copy grant you just created

### *Integration*

### *WLM*

### *Vacuum*

### *Anti-Patterns*

### *Resizing*
- elastic resize
    - quickly add or remove nodes of same type 
        ( it can change node types, but not without dropping connections - it creates a whole new cluster )
    - cluster is down for a few minutes
    - tries to keep connections open across the downtime
    - limited to doubling or halving for some dc2 and ra3 node types
- classic resize
    - change node type and / or number of nodes
    - cluster is read-only for hours to days
- snapshot , restore , resize
    - used to keep cluster available during a classic resize
    - copy cluster, resize new cluster 

### *Security Concerns*
- using a Hardware Security Module ( HSM )
    - must use a client and server certificate to configure a trusted connection between `Redshift` and the HSM
    - if migrating an unencrypted clustr to an HSM-encrypted cluster, you must create the new encrypted cluster and then move data to it
- defining access privileges for user or group
    - use the GRANT or REVOKE commands in SQL
    - example: grant select on table foo to bob;