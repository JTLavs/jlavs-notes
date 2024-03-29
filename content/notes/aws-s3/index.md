---
title: AWS - S3
date: "2022-02-19"
tags: ["aws"]
---
- Allows storage of objects (files) into buckets
- Must have globally unique name
- Naming convention
    - No uppercase
    - No underscores
    - 3-63 characters
    - Not an IP
    - Must start with lowercase or number
- Max. object size is 5TB, if uploading more than 5GB must use multi-part upload
- Can version files, enabled at the bucket level
- Versioning protects against unintended deletes (can restore a version)
- Easy rollback to previous version

### S3 Object Encryption
- 4 ways to encrypt objects
#### SSE-S3
- Encryption using keys handled and managed by AWS
- Object encrypted server-side
- AES-256 encryption type
- Must set header `"x-amz-server-side-encryption": "AES-256"`
#### SSE-KMS
- Encryption using keys handled and managed by AWS
- KMS advantages: user control and audit trail
- Encrypted server side
- Must set header `"x-amz-server-side-encryption": "aws:kms"`
#### SSE-C
- Server side encryption using data keys fully managed by the customer outside of AWS
- S3 does not store encryption key
- HTTPS must be used
- Key must be provided in HTTP headers for every request made
#### Client-side encryption
- Client library such as the S3 encryption client
- Clients must encrypt data before sending
- Clients must decrypt data when retrieving
- Customer fully manages keys & encryption cycle

### S3 encryption in transit
- S3 exposes both HTTP & HTTPS endpoints
- Can use either but HTTPS is recommended
- HTTPS mandatory for SSE-C
- Encryption in flight/transit also called SSL/TLS
- Most clients use HTTPS by default

### S3 Object lock
- Store objects using write-once-read-many (WORM)
- Prevents objects from being overwritten  or deleted for a fixed amount of time or indefinitely
- Helps meet regulatory requirements
- Provides two ways to manage retention
    - Retention period: Specifies a fixed period of time during which an object is locked
    - Legal hold - Same as retention period, but has no expiration. In place until removed
- Only works on versioned buckets
- A new version of object can still be created
- Object can gave both retention period and legal hold, one but not the other or none.

### S3 Bucket policies
- JSON based policies
    - Resources: buckets and objects
    - Actions: Set of APIs to ALLOW or DENY
    - Principal: The account or users to apply the policy to
- Use S3 bucket policy for:
    - Grant public access to bucket
    - Force objects to be encrypted
    - Grant access to another account (cross account)

- Block public access to buckets and objects granted through new ACLS, any ACLS, new public bucket or access point
- Block public and cross-account access to buckets & objects through any public bucket or access point policies
- These settings were created to prevent company data leaks
- If you know the bucket should never be public leave these settings on. They can be set at the account level

### S3 - Moving between classes
- You can transisition between storage classes
- Moving objects can be automated using a lifecycle configuration

### S3 - Lifecycle rules
- Transition actions
    - Defines when objects are transitioned to another storage class
- Expiration actions
    - Configure objects to delete after some time
    - Can be used to delete old versions of files (if versioning enabled)
- Rules can be created for a certain prefix
- Rules can be created for object logs

### S3 - Performance
- S3 autoscales to high request rates
- Your app can achieve at least 3500 PUT/COPY/POST/DELETE and 5500 GET/HEAD requests per second per prefix
- No limits to no. of prefixes in a bucket
- If you spread reads across prefixes evenly, you can achieve 22000 requests per second for GET and HEAD requests
- Multi-part upload:
    - recommended for files > 100 MB
    - must use for files > 5GB
    - can help parallellize uploads
    - file divided into parts and uploaded
- S3 Transfer Acceleration
    - Increase transfer speed by transferring file to an AWS edge location which will forward the data to the S3 bucket in target region
    - Compatible with multi-part upload


### S3 Security
- User based
    - IAM policies: which API calls should be allowed for a specific user
- Resource based
    - Bucket policies: bucket wide rules from the S3 console allows cross account
    - Object Access Control List (ACL) - finer grain
    - Bucket Access Control List (ACL) - less common
- An IAM principal can access object if:
    - the user permissions allow it OR the resource policy allows it AND no explicit DENY
- Networking
    - Supports VPC endpoints
- Logging & Audit
    - S3 Access logs can be stored in another bucket
    - API calls logged in CloudTrail

### S3 Cors
- If a client does a corss origin request on our S3 bucket we need to enable the correct CORS headers
- Can allow for a specific origin or all origins

### S3 Consistency Model
- After a successful write of a new object or an overwrite or delete any subsequent request immediately recieves the latest version of the object (read after write consistency)
- Any subsequent list request immediately reflects changes (list consistency)

### S3 Replication (CRR & SRR)
- Must enable versioning in source and destination
- Cross region replication (CRR)
- Same Region replication (SRR)
- Buckets can be in different accounts
- Copying is asynchronous
- Needs proper IAM permissions
- CRR uses cases:
    - compliance, lower latency access, replication across accounts
- SRR use cases:
    - log aggregation, live replication between test and production accounts
- Only new objects can be replicated
- For DELETE operations:
    - Can replicate delete markers from source to target
    - Deletions with a version ID are not replicated
- No chaining of replication
    - eg. If bucket 1 replicates to bucket 2, which repilcates to bucket 3, objects in 1 are not replicated to 3.

### S3 Storage classes
- Standard General purpose
- Standard Infrequent access
- One-Zone infrequent access
- Intelligent tiering
- Glacier
- Glacier Deep archive

### S3 Standard - General purpose
- High durability of objects
- High availability
- Use cases: Big Data, Analytics, Mobile/gaming, content distribution

### S3 Standard - Infrequent Access (IA)
- For data accessed less frequently, but requires rapid access when needed
- High durability
- High availability
- Use cases: Data store for disaster recovery, backups

### S3 One Zone - IA
- Same as standard IA, but data is stored in single AZ
- High durability, but data lost when AZ destroyed
- 99.5% availability
- Low latency and high throughput performance
- Supports SSL for data at transit and encryption at rest
- Low cost compared to standard IA
- Use cases: Storing secondary backups, storing data you can recreate

### Amazon Glacier
- Low cost storage meant for archiving/backups
- Data is retained for the longer term (10s of years)
- Alternative to on-premise magnetic tape storage
- Avg. annual durability is 99.9%
- Each item in Glacier is called an archive
- Archives are stored in "vaults"

### Amazon Glacier & Glacier Deep Archive
- Amazon Glacier - 3 retrieval options
    - Expedited (1-5 mins)
    - Standard (3-5 hrs)
    - Bulk (5 - 12 hrs)
    - Minimum storage duration of 90 days
- Amazon Glacier Deep Archive - for long term storage - cheaper
    - Standard (12 hrs)
    - Bulk (48 hrs)
    - Min. storage duration of 180 days

### S3 Intelligent tiering
- Same low latency & high throughput performance as standard S3
- Small monthly monitoring & auto-tiering fee
- Auto move objects between two access tiers based on changing access patterns
- High durability of objects
- Resilient against events that impact an entire AZ
- Designed for 99.9% availability over a given year