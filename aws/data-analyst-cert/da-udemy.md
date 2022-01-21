## "You will need to know this..."
### Course Material
---
[Udemy Class Link](https://www.udemy.com/course/aws-data-analytics/)  
[Sundog Course Material](https://sundog-education.com/aws-certified-big-data-course-materials/)

### **Kinesis Suite**
---
* Kinesis Firehose is `near-real time` streaming of data
* A kinesis Streams Record is a data blob `up to 1 MB in size`
* Producers can only produce 1MB or 1000 messages per second at write per shard  
    * If you go over that limit: `ProvisionedThroughputException` is raised
* Consumer Classic gets 2MB per second per shard across all consumers  
* or you get five API calls per second per shard across all consumers
* Consumer Enhanced Fanout gets 2MB per second at read per shard, per enhanced Consumer
* Data Retention  
    * 24 hour retention by default
    * can be extended by 365 days


### **Kinesis Producers**
---
* Kinesis producer library acronym is KPL
* Kinesis Agent is `an agent that runs on servers`
* Kinesis Producer SDK - PutRecords()  
    * Use Case: low throughput, higher latency, simple API, AWS lambda  
    * `Managed AWS sources for Kinesis Data Streams`
        * Cloudwatch Logs
        * AWS IoT 
        * Kinesis Data Analytics
  
> Whenever you see on the exam 'we need to send data asynchronously to Kinesis Data Streams' usually KPL is the way to do it  
  
`KPL Batching (both turned on by default) - increase throughput and decrease cost`
* `Collect Reocrds and Write to multiple sshards in the same PutRecords() call`
* `Aggregae - increased latency`
    * `Capability to store multiple records in one record (go over 1000 records per second limit)`
    * `increase payload size and improve throughput (max 1MB/s limit)`
* When not to use KPL:
    * application that cannot tolerate this additional delay may need to use the SDK

### **Kinesis Consumers**
---
* Apache Spark is able to read from Kinesis Data Streams as a consumer
* Maximum of five GetRecords() calls per shard per second = `200 ms latency on your data`
* Kinesis Client LIbrary (KCL)
    * If you get an expired iterator exception that means something is going on in your KCL library. That means you need to increase the WCU because Dynamo is not fast enough
        * `ExpiredIteratorException` => Increase WCU in dynamo

### **Kinesis Scaling**
---
> A producer is sending data to a kinesis stream with the correct parition key, yet your consumer received the data out of order...a reason for that would be resharding.
* When you do a kinesis resharding, you can read it straight from the child shards after the resharding. However if you have not read all the data from the parent shard you may read the data out of order for a particular hash key
* After a reshard, you need to ensure your consumer application logic reads the parent shard entirely until there are no new records from the parent  

`Scale resharding cannot be done in parallel and resharding takes a few seconrds per shard`  
* `for 1000 shards to double to 2000, it takes about 8.3 hours`  
### **Kinesis Data Streams - Handling Duplicates for Producers**
---
* Producer retries can create duplicates due to network timeouts
    * ` fix is to embed a unique record ID in the data to be able to deduplicate based on that surrogate key`

### **Kinesis Data Streams - Handling Duplicates for  Consumers**
---
* consumer retries happen when record processors restart:
    * `a worker terminates unexpectedly`
    * `worker instances are added or removed`
    * `shards are merged or split`
    * `application is deployed`
* fix: make your conusmer application idempotent, if the final destination can handle duplicates, it's recommended to do it there  

### **Data Firehose**
---
* stores data into a target destination (batch writes)
    * Destination examples:
        * `Amazon S3`
        * `Amazon Redshift (copy through s3)`
        * `Amazon Elasticsearch`
        * `Splunk`
    * fully managed service with no administration
    * near real time (60 second latency minimum for non full batches)
    * You do not lose data with firehose, either it ends in your targets or you will have a recollection of all the transformation failes, delivery failures, etc in another s3 bucket
> `How do you get all the source data into an amazon S3 bucket through Kinesis data Firhose, this is directly a feature of Firehose!`

### **Cloudwatch Logs Subscription Filter**
---
You can stream CloudWatch logs into:
* `Kinesis Datastream`
* `Kinesis Data Firehose`
* `AWS Lambda`