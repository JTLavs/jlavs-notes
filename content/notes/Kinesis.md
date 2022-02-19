# AWS Kinesis
- Makes it easy to collect, process and analyse streaming data in real-time
- Kinesis Data Streams: Capture, process and store data streams
- Kinesis Data Firehose: Load data streams into AWS data stores
- Kinesis Data Analysis: Analyze data streams with SQL or Apache flink

### Kinesis Data Streams
- Billing per shard provisioned, as many shards as you want
- Retention between 1 day or 365 days
- Ability to reprocess data
- Once data is inserted into Kinesis, it cannot be deleted
- Data that shares the same partition goes to the same shard
#### Consumers
- Write your own consumer: Kinesis Client Library, AWS SDK
- Managed by AWS: Lambda, Data firehose, Data analytics

### Kinesis Data Stream Security
- Control access/authentication using IAM
- Encryption in flight using HTTPS endpoints
- Encryption at rest using KMS
- Can implement encryption/decryption on client side
- Monitor API calls in Cloudtrail

### Kinesis Producers
- Puts data records into data streams
- Data record consists of: Sequence no (unique per partition-key within shard)
- Partition key (must specify)
- Data blob (up to 1MB)
- Can use AWS SDK, Kinesis Producer Library (KPL) or Kinesis Agent
- Write throughput: 1MB/sec or 1000 records/sec per shard
- Use `PutRecord` API
- Use batching with `PutRecord` to reduce costs and increase throughput
- Use highly distributed partition key to avoid 'hot partition'
- Can get `ProvisionThroughputExceeded` error. To solve: Use highly distributed partition key, retry with exponential backoff or increase shards (scaling)
- Same partition key = same shard

### Kinesis Consumers
- Get data records from data streams and process them
- Lambda, Firehose, Analytics, Custom Consumer (AWS SDK), Kinesis Client Library
### Consumer types
#### Shared (classic) fan-out consumer - pull
- Low no. of consuming applications
- Read throughput: 2MB/sec per shard across all consumers
- Max 5 `GetRecords` API calls/sec
- Latency approx. 200ms
- Minimize costs
- Consumers poll data from Kinesis using `GetRecords` API call
- Returns up to 10MB or up to 10000 records
#### Enhanced fan-out consumer - push
- Multiple consuming applications for the same stream
- 2MB/sec per consumer per shard
- Latency approx. 70 ms
- Higher cost
- Kinesis pushes data to consumers over HTTP/2 using `SubscribeToShard` API
- Soft limit of 5 consumer applications (KCL) per data stream
#### Lambda
- Supports both classic and enhanced
- Reads records in batches
- Can configure batch window and batch size
- If error occurs, Lambda retries until successful or data expires
- Can process up to 10 batches per shard simultaneously

### Kinesis Client Library
- Java library that helps read record from a Kinesis Data Stream with ditributed applications sharing the read workload
- Each shard is to be read by only one KCL instance eg. 4 shards = 4 KCL instances
- Progress check pointed into DynamoDB (needs IAM access)
- Track other workers and share work amongst shards using DynamoDB
- KCL can run on EC2, Elastic Beanstalk and on-premises
- Version 1.x (supports shared consumer)
- Version 2.x (supports shared and enhanced fan-out consumer)

### Kinesis Operation - Shard splitting
- Used to increase stream capacity (1MB/s data in shard)
- Used to divide a hot shard
- Old shard is closed and will be deleted once data is expired
- No auto. scaling (manually increase/decrease capacity)
- Can't split into more than 2 shards in a single operation

### Kinesis Operation - Merging shards
- Decrease stream capacity and decrease costs
- Can be used to group two shards with low traffic (cold shards)
- Old shards are closed and will be deleted once data is expired
- Can't merge more than two shards in a single operation

### Kinesis Data Firehose
- Fully managed service, no admin, auto scaling and serverless
- Pay for data going through firehose
- Near real-time: 60 secs latency for non full batches, or minimum 32MB of data at a time
- Supports many data formats, conversations, transformations, compression
- Supports custom data transformations using AWS Lambda
- Can send failed or all data to a backup S3 bucket

### Kinesis Data streams vs Firehose
#### Data streams
- Streaming service for ingest at scale
- Write custom code (producer/consumer)
- Real-time (approx. 200 ms)
- Managed scaling (shard splitting/merging)
- Data storage for 1-365 days
- Supports replay capability
### Firehose
- Load streaming data into S3/Redshift/3rd Party
- Fully managed
- Near real-time (buffer time min. 60 secs)
- Automatic scaling
- No data storage
- Does not support replay capability

### Kinesis Data Analytics
- Perform real-time analysis on Kinesis Streams using SQL
- Fully managed, no servers to provision
- Automatic scaling
- Realtime analytics
- Pay for consumption rate
- Can create streams out of real-time queries
- Use cases: time-series analysis, real-time dashboards, real-time metrics

### Ordering data into Kinesis
- The same key will always go to the same shard

### Kinesis vs SQS ordering
- Lets assume you have 100 users, 5 Kinesis shards and 1 SQS FIFO queue
#### Kinesis Data streams
- On average you will have 20 users per shard
- Users will have their data ordered within each shard
- The max. amount of consumers we can have in parallel is 5
- Can receive up to 5MB/s of data
#### SQS FIFO
- You only have one SQS FIFO queue
- Will have 100 group IDs
- You can have up to 100 consumers, due to 100 group IDs
- Can have up to 300 messages/sec or 3000 if using batching

### SQS vs Kinesis vs SNS
#### SQS
- Consumers 'pull data'
- Data deleted after consumed
- Can have as many consumers as you need
- No need to provision throughput
- Ordering guarantees only FIFO queue
- Individual message delay capability
#### SNS
- Push data to many subscribers
- Up to 12500000 subscribers
- Data is not persisted (lost if not delivered)
- Publish/Subscribe model
- Up to 1000 topics
- No need to provision throughput
- Integrates with SQS for fan-out architecture approach
- FIFO capability for SQS FIFO
#### Kinesis
- Standard: pull data - 2MB per shard
- Enhanced: fan-out - 2MB per shard per consumer
- Possible to replay data
- Meant for real-time big data, analytics and ETL
- Ordering at shard level
- Data expires after X days
- Must provision throughput