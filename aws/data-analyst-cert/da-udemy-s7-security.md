### Course Material
[Udemy Class Link](https://www.udemy.com/course/aws-data-analytics/)  
[Sundog Course Material](https://sundog-education.com/aws-certified-big-data-course-materials/)

## Encryption 101
---  
### *Overview*
**Encryption in Flight** ( TLS / SSL )
- data is encrypted before sending and decrypted after receiving
- TLS certificates help with encryption ( HTTPS )
- encryption in flight ensures no man in the middle attach ( MITM ) can happen

**Server-side Encryption at Rest**
- data is encrypted after being received by the server
- data is decrypted before being sent
- it is stored in an encrypted form thanks to a key ( usually a data key )
- the encryption / decryption keys must be managed somewhere, and the server must have access to it

**Client-side Encryption**  
- data is encrypted by the client and never decrypted by the server
- data will be decrypted by a receiving client
- the server should not be able to decrypt the data
- could leverage `Envelope Encryption`

### *S3 Encryption*
**S3 Encryption for Objects**
- there are 4 methods of encrypting objects in `S3`
    - `SSE-S3`: encrypts `S3` objects using keys handled & managed by AWS
    - `SSE-KMS`: leverage `AWS Key Management Service` to manage encryption keys
    - `SSE-C`: when you manage your own encryption keys
    - `Client Side Encryption`

**SSE-S3**
- encryption using keys handled & managed by `Amazon S3`
- object is encrypted server side
- AES-256 encryption type
- must set header: `"e-amz-server-side-encryption" : "AES256"`

**SSE-KMS**
- leverage `AWS Key Management Service` to manage encryption keys
- KMS advantages: user control & audit trail
- object is encrypted server side
- must set header: `"e-amz-server-side-encryption" : "aws:kms"`

**SSE-C**
- server-side encryption using data keys fully managed by the customer outside of AWS
- `Amazon S3` does not store the encryption key you provide
- HTTPS must be used
- Encryption key must be provided in HTTP headers, for every HTTP request made

**Client Side Encryption**
- client library such as the `Amazon S3 Encryption Client`
- clients must encrypt data themselves before sending to `S3`
- clients must decrypt data themselves when retrieving from `S3`
- customer fully manages the keys and encryption cycle

**Encryption in transit ( SSL / TLS )
- Amazon S3 exposes:
    - HTTP endpoint: non encrypted
    - HTTPS endpoint: encryption in flight
- you are free to use the endpoint you want, but HTTPS is recommended
- most clients would use the HTTPS endpoint by default
- HTTPS is mandatory for `SSE-C`

## KMS
---  
### *Overview*
- anytime you hear `encryption` for an AWS service, it's most likely `KMS`
- easy way to control access to your data, AWS manages keys for you
- fully integrated with `IAM` authorization
- seamlessly integrating into:
    - `Amazon EBS`: encrypt volumns
    - `Amazon S3`: server side encryption objects

- able to fully manage the keys & policies
    - Create, rotation policies, disable, enable
- able to audit key usage  

Three Types of Customer Master Keys ( CMK )  
- `AWS Managed Service Default CMK`: free  
- `User Keys created in KMS`: $1 / month
- `User Keys imported` ( must be 256-bit symmetric key ): $1 / month

**KMS 101**
- anytime you need to share sensitive information...use `KMS`
    - database passwords, credentials to external servies, private key of SSL certs
- the value in `KMS` is that the CMK used to encrypt data can never be retrieved by the user, and the CMK can be rotated for extra security
- never ever store your secrets in plaintext, especially in your code
- encrypted secrets can be stored in the code / environment variables
- `KMS` can only help in encrypting up to 4KB of data per call
    - if larger than this use `Envelope Encryption`

### *Key Rotation*
- For Customer-managed CMK ( not AWS managed CMK )
- if enabled: automatic key rotation happens once per year
- previous key is kept active so you can decrypt old data
- New Key has the same CMK ID ( only the backing key is changed )

**Manual Key Rotation**
- when you want to rotate key more than once a year
- new key has a different CMK ID
- keep the previous key active so you can decrypt old data
- better to use aliases in this case ( to hide the change of key for the application )

**Alias Updating**
- better to use aliases in this case ( to hide the change of key for the application ) 

> if you want to have automatic key rotation, the period is one year.
> But if you want to have manual key rotation then you can go to 90 days or 180 days based on the requirements

## Cloud HSM 
--- 
### *Overview*
- `KMS` => AWS manages the software for encryption
- `CloudHSM` => AWS provisions encryption hardware
- dedicated hardware ( HSM = Hardware Security Module )
- manage your own encryption keys entirely ( not AWS )
- HSM device is tamper resistant, FIPS 140-2 level 3 compliance
- `CloudHSM` clusters are spread across Multi AZ ( HA ) - must setup
- supports both symmetric and asymmetric encryption (SSL / TLS keys )
- no free tier available
- must use the `CloudHSM Client Software`
- `Redshift` supports `CloudHSM` for database encryption and key management
- good option to use with `SSE-C` encryption

## AWS Services Security Deep Dive
---  
### *Kinesis*
- `Kinesis Data Streams`
    - SSL endpoints using the HTTPS protocol to do encryption in flight
    - `AWS KMS` provides server-side encryption
    - for client side encryption, you must your own encryption libraries
    - supported interface `VPC Endpoints` / `Private Link` - access privately
    - `KCL` - must get read / write access to DynamoDB table
- `Kinesis Data Firehose`
    - attach `IAM` roles so it can deliver to `S3` / `ES` / `Redshift` / `Splunk`
    - can encrypt the delivery stream with `KMS` 
    - supported Interface `VPC Endpoints` / `Private Link` - access privately
- `Kinesis Data Analytics`
    - attach `IAM` role so it can read from the `Kinesis Data Streams` and reference sources and write to an output desitination 

### *SQS*
- encryption in flight using the HTTPS endpoint
- server side encryption using `KMS`
- `IAM` policy must allow usage of `SQS`
- `SQS` queue access policy
- client-side encryption must be implemented manually
- `VPC endpoint` is provided through an `Interface`

### *IoT*
- `Iot Policies` 
    - attached to X.509 certificates or `Cognito Identities`
    - able to revoke any device at any time
    - `IoT policies` are JSON documents
    - can be attached to groups instead of indigidual things
- `IAM Policies`
    - attached to users, group or roles
    - used for controlling `IoT AWS APIs`
- attach roles to `Rule Engine` so they can perform their actions

### *S3*
- IAM policies
- `S3` bucket policies
- access control lists ( ACLs )
- encryption in flight using HTTPS
- encryption at rest   
    - server-side encryption: `SSE-S3`, `SSE-KMS`, `SSE-C`
    - client-side encryption - such as Amazon `S3 Encryption Client`
- versioning + MFA delete
- CORS for protecting websites
- `VPC Endpoint is provided through a gateway
- `Glacier` - vault lock policies to prevent deletes ( WORM )

### *DynamoDB*
- data is encrypted in transit using TLS ( HTTPS )
- `DynamoDB` tables are encrypted at rest
    - KMS encryption for base tables and secondary indexes
    - AWS owned key ( default )
    - AWS managed key ( aws / dynamodb )
    - AWS customer managed key ( your own )
- access to tables / API / DAX / using `IAM`
- `DynamoDB streams are encrypted
- `VPC Endpoint` is provided through a Gateway

### *RDS*
- `VPC` provides network isolation
- `Security Groups` control network access to DB instances
- `KMS` provides encryption at rest
- SSL provides encryption in-flight
- `IAM` policies provide protection for the `RDS API`
- `IAM authentication is supported by PostgresSQL and MySQL
- must manage user permissions within the database itself
- MSSQL Server and Oracle support TDE ( Transparent Data Encryption )

### *Aurora*
- similar to `RDS`
- `VPC` provides network isolation
- `Security Groups` control network access to DB instances
- `KMS` provides encryption at rest
- SSL provides encryption in-flight
- `IAM` policies provide protection for the `RDS API`
- `IAM authentication is supported by PostgresSQL and MySQL
- must manage user permissions within the database itself

### *Lambda*
- `IAM Roles` attached to each `Lambda Function`
- Sources & Targets
- `KMS encryption` for secrets
- SSM parameter store for configurations
- `CloudWatch Logs`
- deploy in `VPC` to access private resources

### *Glue*
- `IAM policies` for the `Glue` service
- configure `Glue` to only access JDBC through SSL
- Data Catalog
    - encrypted by KMS
    - resouce policies to protect `Data Catalog` resources ( similar to `S3 bucket policy )
- connection passwords: encrypted by `KMS`
- data written by `AWS Glue` - security configurations
    - `S3` encryption mode: `SSE-S3` or `SSE-KMS`
    - `CloudWatch` encryption mode
    - job bookmarks encryption mode

### *

## STS
---
### *Overview*
- Security Token Service
- allows to grant limited and temporary access to AWS resources
- Token is valid for up to one hour ( must be refreshed )
- Cross Account Access - allows users from one AWS account access resources in another
- Federation ( Active Directory )
    - provides a non-AWS user with temporary AWS access by linking users AD credentials
    - uses SAML ( security assertion markup language )
    - allows Single SIgn On ( SSO ) which enables users to log in to AWS console without assigning IAM credentials
Federation with third party providers / `Cognito`
    - used mainly in web and mobile applications
    - makes use of Facebook / Google / Amazon etc to federate them

> allows users to get temporary access to AWS resources and its used for cross account access for the Federation stuff 

### *Cross Account Access*
- define an `IAM Role` for another account to access
- define which accounts can access this `IAM Role`
- uses `AWS STS` to retrieve credentials and impersonate the `IAM ROle` you have access to ( AssumeRole API )
- temporary credentials can be valid between 15 minutes to 1 hour