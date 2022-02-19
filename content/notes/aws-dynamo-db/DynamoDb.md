# DynamoDB
- Fully managed, highly available with replication across 3 AZs
- NoSQL database
- Scales to massive workloads
- Millions of requests per second
- Fast and consistent in performance
- Integrated with IAM for security, authentication and admin
- Enables event driven programming with DynamoDB streams
- Low cost autoscaling capabilities

### DynamoDB - Primary Keys
- Option 1: (Partition key only)
    - Must be unique for each item
    - Must be 'diverse' so data is distributed
- Option 2: (Partition key + Sort key)
    - Combination must be unique
    - Data is grouped
    - Sort key == range key

### DynamoDB - Provisioned Throughput
- Must have provisioned read & write capacity units
- Read Capacity Units (RCU) - throughput for reads
- Write Capacity Units (WCU) - throughput for writes
- Option to setup autoscaling to meet demand
- Throughput can be exceeded temporarily using 'burst credit'
- If burst is empty, you will get ProvisionedThroughputException
- It's then advised to do an exceptional back-off retry

### DynamoDB - Write Capacity Units 
- 1 WCU represents one write per second for an item up to 1KB in size
- If the items are larger than 1KB more WCU are consumed


### Strongly consistent read ve Eventually consistent read
- Eventually consistent read
    - If we read just after a write, it's possible we'll get unexpected response becuase of replication
- Strongly consistent read
    - If we read just after a write, we will get correct data
- By default, Dynamo uses eventually consistent reads, but you can set ConsistentRead flag to true for some operations


### Read capacity units
- One read capacity unit represents one strongly consistent read per second, or two eventually consistent reads per second, for an item up to 4KB in size
- If items are greater than 4KB more RCU are consumed

### DynamoDB - Partitions Internal
- Data is divided into partitions
- Partition keys go through a hashing algorithm to know which partition they go to
- To compute the no. of partitions;
    - By capacity: `(Total RCU/3000) + (Total WCU/100)`
    - By size: `(Total size / 10 GB)`
    - Total partitions: `CEIL(MAX(capacity,size))`

### DynamoDB - Throttling
- If we exceed our RCU or WCU, we get `ProvisionedThroughputExceededException`
- Reasons:
    - Hot keys - one parition key is being read too many times
    - Hot partitions
    - Very large items: RCU & WCU depends on size of items
- Solutions:
    - Exponential backoff when exception is encountered =
    - Distribute partition keys as much as possible
    - If RCU issue, we can use DynamoDB Accelerator (DAX)

### DynamoDB - Writing Data
- `PutItem`: Write data to DynamoDB
    - Consumes RCU
- `UpdateItem`: Update data in DynamoDB
- Conditional writes:
    - Accept a write/update only if conditions are respected otherwise rejected
    - Helps with conncurent access to items
    - No performance impact

### DynamoDB - LSI (Local secondary index)
- Alternative range key for your table, local to the hash key
- Up to 5 secondary indexes per table
- The sort key consists of exactly one scaler attribute
- The attribute you choose must be a scalar String, Number or Binary
- LSI must be defined at creation time

### DynamoDB - GSI (Global secondary index)
- To speed up queries on non-key attributes, use a GSI
- GSI = partition key + optional sort key
- The index is a new 'table' and we can project attributes on it
    - The partition key and sort key of the original table are always projected
    - Can specify extra attributes to project (INCLUDE)
    - Can use all attributes from main table (ALL)

### Indexes and Throttling
- GSI
    - If the writes are throttled on the GSI, main table will also be throttled
    - Even if the WCU on the main tables are fine
    - Choose the GSI partition key carefully
- LSI
    - Uses the WCU and RCU of the main table
    - No special throttling considerations

### Deleting data
- `DeleteItem`
    - Delete an individual row
    - Ability to perform a conditional delete
- `DeleteTable`
    - Delete a whole table and all its items
    - Much quicker deletion than calling DeleteItem on all items

### Batching writes
- `BatchWriteItem`
    - Up to 25 PutItem and/or DeleteItem in one call
    - Up to 16MB of data written
    - Up to 400KB of data per item
- Batching allows you to save in latency by reducing the no. of API calls done against DynamoDB
- Operations are done in parallel for better efficiency
- It is possible for part of a batch to fail, in which case we have to retry the failed items (using exponential back-off algorithm)

### Reading data
- `GetItem`
    - Read based on primary key
    - Primary Key - HASH or HASH-RANGE
    - Eventually consistent read by default
    - Option to use strongly consistent reads (more RCU - may take longer)
    - ProjectionExpression can be specified to include only certain attributes
- `BatchGetItem`
    - Up to 100 items
    - Up to 16MB of data
    - Items are retrieved in parallel to minimize latency

### DynamoDb as a session state cache
- Common to use DynamoDB to store session state
- vs. Elasticache
    - Elasticache is in memory, DynamoDb is serverless
- vs. EFS
    - Must be attached to EC2 instances as a network drive
- vs. EBS & Instance Store
    - EBS & Instance store can only be used for local caching not shared caching
- vs. S3
    - S3 is higher latency and not meant for small objects

### DynamoDb Write sharding
- eg. Imagine we have a voting app with two candidates: A & B
- If we use a partition key of candidate_id we will run into partition issues, as only have two options
- Solutions: Add a suffix (usually random, sometimes calculated)

### DynamoDb Write Types
- Concurrent writes

![](./images/ConcurrentWrite.PNG)

- Conditional writes

![](./images/ConditionalWrite.PNG)

- Atomic writes

![](./images/AtomicWrite.PNG)

- Batch writes

![](./images/BatchWrite.PNG)


### DynamoDB - Large objects pattern
![](./images/DynamoDbLargeObjects.PNG)

### DynamoDB - Operations
- Table cleanup
    - Option 1: Scan & Delete -> Very slow, expensive, consumes RCU & WCU
    - Option 2: Drop table & recreate -> fast, cheap,, efficient
- Copying a table
    - Option 1: Use AWS DataPipeline (uses EMR)
    - Option 2: Create a backup and restore the backup into a new table (can take time)
    - Option 3: Write owmn code -> Scan & Write

### DynamoDb - Concurrency
- Dynamo has a feature called 'condition update/delete'
- Means you can ensure an item hasn't changed before deleting/updating it
- That make DynamoDB an optimistic locking/concurrency database

### DynamoDB - DAX
- DynamoDb Accelerator
- Seamless cache for DynamoDB, no application re-write
- Writes go through DAX
- Micro second latency for cached reads & queries
- Solves hot-key partitions (too many reads)
- 5 mins TTL for cache by default
- Up to 10 modes in cluster
- Multi-AZ (3 nodes min. recommended for production)
- Secure (encryption at rest with KMS, VPC, IAM, CloudTrail)

### DynamoDB Streams
- Changes in the DB can end up on a DynamoDB stream
- Stream can be read by AWS Lambda & EC2 instances and then we can do:
    - react to changes in real time
    - Analytics
    - Create derivative tables
    - Insert into ElasticSearch
    - Streams are made of shards, automated by AWS
- Could implement cross region replication using streams
- Stream has 24 hr of data retention
- Choose from the information that will be written to the stream whenever data is modified:
    - KEYS_ONLY: Only the key attributes of the modified item
    - NEW_IMAGE: The entire item, after it was modified
    - OLD_IMAGE: The entire item, before it was modified
    - NEW_AND_OLD_IMAGES: Entire new and old images

### DynamoDB Steams & Lambda
- Need to define an event source mapping to read from a stream
- Need to ensure Lambda has appropriate permissions
- Lambda function invoked synchonously

### DynamoDB - TTL (time-to-live)
- Automatically delete an item after an expiry date/time
- Provided at no extra cost, deletions do not use WCU/RCU
- Background task operated by DynamoDB itself
- Helps reduce storage & manage table size over time
- Helps to adhere to regulatory norms
- Enabled per row (add a TTL column and add a date there)
- Typically deletes expired items with 48 hrs of expiration
- Deleted items due to TTL are also deleted in GSL/LSI
- Streams can help recover expired items

### DynamoDb - Transactions
- Ability to create/update/delete multiple rows in different tables at the same time
- 'All or nothing' operation
- Write modes: Standard/Transactional
- Read modes: Eventual consistency, strong consistency, transactional
- Consume 2x of WCU/RCU
- APIs: `TransactWriteItems`, `TransactGetItems`

### Transactional capacity computations
- 5KB item size
- Transactional Item Writes/second: 3
    - [5KB/1KB per WCU] x 2 (cost of transactional) x 3(writes per second)  = 30 WCU

### DynamoDb - Security & Other features
- Security
    - VPC endpoints available to access DynamoDB without internet
    - Access fully controlled by IAM
    - Encryption at rest using KMS
    - Encryption in transit using SSL/TLS
- Backup and restore feature available
    - Point-in-time restore like RDS
    - No performance impact
- Global Tables
    - Multi-region, fully replicated, high performance
- Amazon DMS can be used to migrate to DynamoDB (from Mongo, Oracle, MySQL etc..)
- Can launch a local DynamoDB on your computer

### DynamoDB - Fine-Grained Access Control
- Using Web Identity Federation or Cognito Identity Pools, each user gets AWS credentials
- You can assign an IAM role to these users with a condition to limit their API access to DynamoDB
- LeadingKeys - limit low-level access for users on the primary key
- Attributes - limit specific attributes the user can see