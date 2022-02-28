## "You will need to know this..."
### Course Material
---
[Udemy Class Link](https://www.udemy.com/course/aws-data-analytics/)  
[Sundog Course Material](https://sundog-education.com/aws-certified-big-data-course-materials/)

## S3
---
### *Buckets*. 
- `Buckets` must have a globally unique name
- `Buckets` are defined at the region level
- naming convention
    - no uppercase
    - no underscore
    - 3 - 63 characters long
    - not an IP
    - must start wth lowercase letter or number

### *Objects*  
- `Objects` have a `Key`. The `Key` is the FULL path
    - there is no concept of directories within a `Bucket`
- max size for an object is `5 TB`
- anything more than 5 GB must use 'multi-part upload'

> `S3` is strongly consistent, anytime you do a write then you do a read after, 
> the read is going to give you the same results of what you just uploaded.  

## *S3 Storage Classes*
---  
### *S3 Standard - General Purpose*. 
- high durability of objects across multiple regions
- sustain two concurrent facility failures

`Use Cases`: big data analytics, mobile & gaming applications, etc

### *S3 Standard-Infrequent Access (IA)*  
> suitable for data that is less frequently accessed, but requires rapid access when needed.

- lower cost compared to `S3 standard`
  
`Use Cases`: data store for disaster recovery, backups,

### *S3 One Zone-Infrequent Access*  
- same as `Infrequent Access` but stored in a single `Availability Zone`
- supports SSL for data-in-transit and encryption-at-rest
- low cost compared to `Infrequent Access` (20%)

### *S3 Intelligent Tiering*  
- small monthly monitoring and auto-tiering fee
- automatically moves objects between two access tiers based on changing access patterns

### *Amazon Glacier*  
- low cost object storage meant for archiving / backup
- data is retained for the longer term (decades)
- Each item in Glacier is called an 'Archive' (up to 40TB)
- `Archives` are stored in Vault
- minimum storage duration of 90 days

**Three Retrieval Options:**
- `expedited (1 to 5 minutes)`
- `standard (3 to 5 hours)`
- `bulk (5 to 12 hours)`

### *Amazon Glacier Deep Archive*
- minimum storage duration of 180 days

**Retrieval Options**
- `standard (12 hours)`
- `bulk (48 hours)`

### *S3 Lifecycle Rules*
- `transition actions` - defines when objects are transitioned to another storage class
- `expiration actions` - configure objects to expire ( delete ) after some time
    - can be used to delete old versions of files ( if versioning is enabled )

`rules can be created for a certain prefix`
`rules can be created for certain object tags`

### *S3 Versioning*
`Any file that is not versioned prior to enabling versioning will have version 'null'`
`Suspending versioning does not delete the previous versions`

### *S3 Replication (CRR & SRR)*
`Buckets can be in different accounts`
`Cross Region Replication (CRR)` - Use Case: compliance, lower latency access, replication across accounts
`Same Region Replication (SRR)` - Use Case: log aggregation, live replication between production and test accounts

- After activating, only new objects are replicated
- for `DELETE` operations
    - cannot replicate delete markers from source to target 
    - deletions with a version ID are not replicated
    - basically deletion replication isn't a thing
- there is no chaining of replication

### S3 Performance
---
- 3,500 `PUT` / `COPY` / `POST` / `DELETE` per second per prefix
- 5,500 `GET` / `HEAD` requests per second per prefix

**Multi-Part Upload:**
- recommended for files more than 100MB
- must be used for files more than 5 GB

**S3 Transfer Acceleration**
- increase transfer speed by transferring file to an AWS edge location which will forward the data to the S3 bucket in the target region
- compatible with multi-part upload

### *S3 Encryption*
> it is important to understand which ones are adapted to which situation for the exam

`SSE-S3` - encrypts S3 objects using keys handled and managed by AWS
- object is encyrpted server side
- AES-256 encryption type
- must set header "x-amz-server-side-encryption":"AES256"

`SSE-KMS` - leverage `AWS Key Management Service` to manage encryption keys
- `KMS` advantages: user control + audit trail
- object is encrypted server side
- must set header "x-amz-server-side-encryption":"aws:kms"

`SSE-C` - when you want to manage your own encryption
- Amazon S3 does not store the encryption key you provide
- HTTPS must be used
- Encryption key must be provided in HTTP headers, for every HTTP request made

### *Client Side Encryption*
- client library such as the S3 Encryption Client
- clients must encrypt data themselves before sending to S3
- clients must decrpt data themselves when retrieving from S3
- customer fully manages the keys and encryption cycle

**Encryption in transit(SSL/TLS)**  
S3 exposes
- HTTP endpoint: non encrypted
- HTTPS endpoint: encryption in flight

Mandatory for SSE-C

**S3 Security**
user based - `IAM Policies`

**Resource Based** 
- `Bucket Policies` - bucket wide rules from the s3 console - allows cross account
- object access control list (ACL) - finer grain
- bucket access control list (ACL) - less common

> You do not need to remember these going into the exam.
> What you do need to remember is that there is a way to block public access to your S3 bucket through these settings.  

`anytime in the exam you see the access of certain files to certain users for a limited amount of time, think **pre-signed URLs**`

### *S3 Select & Glacier Select*
- retrieve less data using SQL by performing select statements
`Note: Glacier Select can only do **uncompressed** csv files`
- restore the file, then use s3 select for compressed csv files in glacier

### *S3 Event Notification*
Target Services:
- SNS
- SQS
- Lambda

## DynamoDB
---  
- fast and consistent in performance (low latency on retrieval)
- enables event driven programming with DynamoDB Streams
- maximum size of a item is 400KB
- string, number, binary, boolean, null
- document types: list, map
- set types: string set, number set, binary set

### *Partition Keys*
- Partition Key only (HASH)
- Partition Key + Sort Key

> dynamoDB is going to be more for when your data is hot and smaller. While S3 is going to be more when data is a bi colder but much bigger

### *Provisioned Throughput*
- you can set up auto-scaling if you do not want to manually configure RCU and WCU
- Throughput can be exceeded temporarily using 'burst credit'
    - if burst credit is empty, you will get a `ProvisionedThroughputException`
    - then it is advised to do an exponential back-off retry

#### Write Capcity Units (WCU)
- one write capacity unit represent one write per second for an item up to 1 KB in size
- if the items are larger than 1 KB, more WCU are consumed

#### Strongly vs Eventually Consistent Read
**Eventually Consistent** - If we read just after a write, it's posisble we will get unexpected response because of replication
**Strongly Consistent** - If we read just after a write, we will get the correct data

By Default DynamoDB uses Eventually Consistent Rreads, but GetItem, Query & Scan provide 'ConsistentRead' parameters you can set to True

#### Read Capacity Units (RCU)
- one read capacity unit represents one strongly consistent read per second, or two eventually consistent read per second for an item up to 4 KB in size
- if the items are larger than 4 Kb more RCU are consumed

### DynamoDB - Partitions Internal
Each partition:
- can have a maximum of 3000 RCU / 1000 WCU
- Maximum of 10GB per partition

`To compute the number of partitions:`
- By capacity (TOTAL RCU / 3000) + (TOTAL WCU / 1000)
- By Size: total size / 10 Gb
- Total Partitions = CEILING(MAX(Capacity, Size))

` WCU and RCU are spread evenly between partitions`

### DynamoDB - Writing Data
---  
**putItem** - Write data to DynamoDB (create or full replace)
- consumes WCU
**UpdateItem** - Update data in DynamoDb (partial update of attributes)
- possibility to use Atomic Counters and increase them
**Conditional Writes** 
- accept a write/update only if conditions are respected, otherwise reject
- helps with concurrent access to items
- no performance impact
**BatchWriteItem**
- Up to 25 PutItem and / or DeleteItem in one call
- Up to 16 MB of data written
- Up to 400 KB of data per item

### DynamoDb - Deleting Data
---
**DeleteItem**
- delete an individual row
- ability to perform a conditional delete
**DeleteTable**
- delete a whole table and all its items
- much quicker deletion than calling DeleteItem on all items
- much quicker than deleting individual records but it's the whole thing
- it's possible for part of a batch to fail, in which case we have to try the failed items again

### DynamoDb - Reading Data
**GetItem** 
- Read based on Primary key
- Primary Key = HASH or HASH-RANGE
- Eventually consistent read by default
- option to use strongly consistent reads
**BatchGetItem**
- Up to 100 Items
- Up to 16 MB of data
- Items are retrieved in parallel to minimize latency
**Query**
**Scan**
- scan the entire table and then filter out data (inefficient)
- for faster performance use parallel scans

### LSI & GSI
---  
#### LSI
- alternate range key for your table, local to the hash key
- up to five LSI
- sort key consists of exactly one scalar attribute
- must be specified on table creation

#### GSI
- define new partition key + optional sort key
- must define RCU and WCU for each GSI
