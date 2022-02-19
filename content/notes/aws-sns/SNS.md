# AWS SNS
- Event producer that sends messages to one SNS topic
- Event recievers listen to the SNS topic and will get all the messages
- Up to 10,000,000 subscriptions per topic
- 100,000 topic limit
- Subscribers can be: SQS, Lambda, HTTP/HTTPS, Emails, SMS, Mobile notifications
- Many services can send messages to SNS for notifications: CloudWatch (for alarms), Auto-scaling groups, S3 (for bucket events), CloudFormation (when state changes)

### SNS Security
- In-flight encryption using HTTPS API
- At-rest encryption using KMS keys
- Client-side encryption if client wants to encrypt/decrypt
- IAM policies to regulate access to the SNS API
#### SNS access policies
- Useful for cross account access to SNS topics
- Useful for allowing other services to write a topic

### SNS - FIFO
- First-in-first-out
- Similar to SQS FIFO
    - Ordering by MessageGroupID
    - Deduplication using a DeduplicationID or Content-based
- Can only have SQS FIFO queues as subscribers
- Limited throughput
- Combine with fan-out for fan-out + ordering + deduplication

### SNS - Message Filtering
- JSON policy used to filter messages sent to SNS topic subscriptions
- If a subscription does not have a filter it recieves every message from the topic