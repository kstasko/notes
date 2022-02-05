### Course Material
[Udemy Class Link](https://www.udemy.com/course/aws-data-analytics/)  
[Sundog Course Material](https://sundog-education.com/aws-certified-big-data-course-materials/)

## Kinesis Suite
---  
- `Kinesis Firehose` is **near-real time** streaming of data
- a `Kinesis Streams` Record is a data blob **up to 1 MB in size**
- `Producers` can only produce 1MB or 1000 messages per second at write per shard  
    - If you go over that limit: `ProvisionedThroughputException` is raised
- `Consumer Classic` gets 2MB per second per shard across all consumers  
   - or you get five API calls per second per shard across all consumers
- `Consumer Enhanced Fanout` gets 2MB per second at read per shard, per enhanced Consumer. 

Data Retention
- 24 hour retention by default
- can be extended by 365 days


### Kinesis Producers (KPL)
- `Kinesis Agent` is **an agent that runs on servers**
- `Kinesis Producer SDK` uses PutRecords()  
   - Use Case: low throughput, higher latency, simple API, AWS lambda  
   - `Managed AWS sources for Kinesis Data Streams`
      - Cloudwatch Logs
      - AWS IoT 
      - Kinesis Data Analytics
  
> Whenever you see on the exam 'we need to send data `asynchronously` to Kinesis Data Streams' usually KPL is the way to do it  
  
`KPL Batching (both turned on by default) - increase throughput and decrease cost`
- `collect records and write to multiple shards in the same PutRecords() call`
- `Aggregate - increased latency`
    - `Capability to store multiple records in one record (go over 1000 records per second limit)`
    - `increase payload size and improve throughput (max 1MB/s limit)`
- When not to use KPL:
    - application that cannot tolerate this additional delay may need to use the SDK

### Kinesis Consumers
- *Apache Spark* is able to read from Kinesis Data Streams as a consumer
- maximum of 5 GetRecords() calls per shard per second = `200 ms latency on your data`
- *Kinesis Client LIbrary (KCL)*
    - if you get an expired iterator exception that means something is going on in your KCL library. It means you need to increase the WCU because Dynamo is not fast enough
        * `ExpiredIteratorException` => Increase WCU in dynamo

### Kinesis Scaling
> A producer is sending data to a kinesis stream with the correct partition key, yet your consumer received the data out of order
> ... a reason for that would be resharding.  

- when you do a kinesis resharding, you can read it straight from the child shards after the resharding. However if you have not read all the data from the parent shard you may read the data out of order for a particular hash key
- after a reshard, you need to ensure your consumer application logic reads the parent shard entirely until there are no new records from the parent  
- `Scale resharding cannot be done in parallel and resharding takes a few seconds per shard`  
   - `for 1000 shards to double to 2000, it takes about 8.3 hours`  
   - 
## Kinesis Data Streams 
---
**Handling Duplicates for Producers**
- producer retries can create duplicates due to network timeouts
   - `fix is to embed a unique record ID in the data to be able to deduplicate based on that surrogate key`
**Handling Duplicates for  Consumers**
consumer retries happen when record processors restart:
- `a worker terminates unexpectedly`
- `worker instances are added or removed`
- `shards are merged or split`
- `application is deployed`
   - `fix`: make your conusmer application idempotent, if the final destination can handle duplicates, it's recommended to do it there  

## Data Firehose
---
stores data into a target destination (batch writes)
- Destination examples:
     - `Amazon S3`
     - `Amazon Redshift (copy through s3)`
     - `Amazon Elasticsearch`
     - `Splunk`
 - fully managed service with no administration
 - near real time (60 second latency minimum for non full batches)

`You do not lose data with firehose, either it ends in your targets or you will have a recollection of all the transformation failes, delivery failures, etc in another s3 bucket`

> How do you get all the source data into an amazon S3 bucket through Kinesis data Firhose, this is directly a feature of Firehose!

### Cloudwatch Logs Subscription Filter
---  
You can stream CloudWatch logs into:
* `Kinesis Datastream`
* `Kinesis Data Firehose`
* `AWS Lambda`

## SQS 
---
### *Overview*
- default `retention is 4 days, can be set up to 14 days`. 
- 256 kB per message sent  
- horizontal scaling in terms of number of consumers  
- sends strings (as opposed to kinesis' bytes)  
- consumers poll messages, consumer processes that message and then deletes it from the queue
    - `messages cannot be processed by multiple different consumer applications`

### *Use Case*
- decouple applications (e.g. asyncronous payments)
- buffer writes to database (e.g. voting application)
- handle large loads of incoming messages (email sender)

### *SQS Extended Client*
- sends S3 key to SQS queue and tells consumers where to download the larger than 256kB file

### *Limits*
- `FIFO supports up to 3000 message per second (using batching)`
- maximum message size is 256kB

### SQS vs Kinesis
---
`**Check Udemy Note for lecture**' 

## IoT 
---
### *Overview*
> I would say the most important things [to know] is going to be the thing registry to know that there is security, the IoT message broker, the rules engine and the device shadow.  

`**device gateway**` - enables your device to securely communicate
`**rules engine**` - contains a bunch of rules that you can define allowing you to modify the behavior of your devices based on them. `It alls us to send data to many different targets.`
`**device shadow**` - literally a shadow of my device. If your device is disconnected from the network, the shadow helps tell it what rule/action to follow to be back up to date

### *Components Deep Dive*
#### **Device Gateway**  
- supports the entrypoint of IoT Devices  
- allows devices to securely and efficiently communicate with AWS IoT  
- `supports the MQTT, Websockets and HTTP 1.1`  
- fully managed and scales to 1 billion devices  
- no infrastructure management  

#### **Message Broker**
* pub/sub messaging pattern - low latency
* devices can communicate this way using `MQQTT, Websockets, or HTTP 1.1.`  
* anything connected to the message broker will receive the message

#### **Registry**  
* all connected devices are represented in the Registry  
* organizes the resources associated with each device
* supports metadata for each device
* can create X.509 certificates to help IoT devices connect to AWS 
* group devices and apply their permissions (think IAM)

> So remember your devices are ruled by AWS IoT policies whereas your users, roles, and groups just like in AWS normal are ruled by IAM polciies  

#### **Device Shadow**
* JSON document representing the state of a connected thing  
* we can set the state to a different desired state

#### **Rules Engine**
* Rules are defined on the MQTT topics, and say when it's triggered and actions to do from that trigger

#### **IoT Greengrass**
* brings compute layer directly to the device  
    * we have our local device and we can run lambda functions on the device   

### Database Migration Service
---
* resilient and self-healing  
* source database remains available during the migration
* continuous data replication using CDC

#### **Schema Conversion Tool
* converts the databases schema from one to another

### Direct Connection
---
Provides a dedicated private connection from the remote network into your VPC  
* will need to set up virtual private gateway on your VPC  
* access public resources (s3 and ec2) while on this connection as well
* data in transit is NOT encrypted
    * to do this you will have to set up a VPN alongside DX

#### Direct Connect Gateway
* used when you want to connect one or more VPCs in different regions

#### **Connection Types**
* dedicated connection - 1Gb/s to 10 Gb/s capacity  
    * physical ethernet port per dedicated customer  
* hosted connection - up to 10 Gb/s
> in the exam they will ask you questions around 'we want to transfer some data within a week and we want it to be fast.' So  an answer CANNOT be direct connect

#### **High Resiliency**
> Two corporate data centers and have two different direct conncect locations, and this gives us some redundnacy

#### **Maximum Resiliency**
> setup again two direct connect locations, but this time each direc connect locations will have two connections independence to give you max resiliency. 
> So in this case, we have four connections across two locations going into AWS. 

### Snow Family
---
Offline devices that allow you to perform data migrations
* if it takes more than a week to upload data, consider the snow family

#### Snow Edge
* storage - 80 terrabytes
* migration size - up to petabytes offline
* storage clustering - up to 15 nodes

#### Snow Cone
* storage - 8 terrabytes
* migration size - up to 24 terrabytes offline
* datasync agent - pre installed

#### Snowmobile
* storage - 100 petabytes
* migration size - up to exabytes offline

### Management Streaming for Apache Kafka (MSK)
---
* kafka is an alternative to kinesis 
* can create custom configurations for your cluster
    * `can customize the cluster to receive messages from 1MB up to 10MB!`

#### **Security**
Encryption
* optional in-flight using TLS between the brokers  
* optional in-flight with TLS between the clients and brokers
* at rest for your EBS volumes using KMS  
Network Security  
* authorize specific security groups for your Apache Kafka clients
`Authentication and Authorization `
* `define who can read/write to which topics  `
* `Mutual TLS (AuthN) + Kafka ACLs (AuthZ)  `
* `SASL/SCRM (AuthN) + Kafka ACLs (AuthZ)  `

> one thing you need to know is that Kafka ACLs for mutual TLS and SASL/SCRAM `cannot` be managed using IAM polciies. They have to be defined from within your Kafka cluster

Come back and make table of differences between MSK vs Kinesis
