# AWS SQS and SNS
## AWS SQS
- Used to decouple applications
- Unlimited throughput
- Default retention, unlimited no. of messages in queue
- Low latency (< 10ms on publish/recieve)
- Limitation of 256 KB
- Can have duplicate messages (at least once delivery)
- Can have out of order messages (best effort ordering)

### SQS Security
#### Encryption
- In flight using HTTPS API
- At-rest encryption using KMS keys
- Client-side encryption if client wants to perform encryption/decryption
#### Access controls
- IAM policies to regulate access to SQS API
#### SQS Access Policies
- Useful for cross-account access to SQS queues
- Useful for allowing other services to write to queues

### SQS - Message Visiblity timeout
- After message is polled by a consumer, it becomes invisible to other consumers
- By default, the timeout is 30 seconds
- This means the message has 30 secs to be processed before it is visible again to the other consumers
- If not processed in the window it will be processed twice
- Consumer could call `ChangeMessageVisibility` API to get more time
- If timeout too high, consumer creates and reprocessing takes time
- If visibility too low, we may get duplicates

### SQS - Dead Letter Queue
- If the consumer fails to process message within the VisibiltyTimeout it goes back to the queue.
- Can set a threshold of how many times a message can go back to the queue.
- After the `MaximumRecieves` threshold is exceeded, the message goes into a DeadLetterQueue.
- Useful to debug why a message failed to process
- Ensure to process messages in DLQs before they expire, default retention period of 14 days

### SQS - Delay Queue
- Delay a message, so consumers don't immediately see it up to 15 mins
- Default is 0 seconds, so the messages are seen straight away
- Can set a default at queue level
- Can override default using `DelaySeconds` parameter

### SQS - Long Polling
- When consumers request messages from the queue they can 'wait' for messages to arrive if there are none in the queue
- This is called Long Polling
- Decreases the no. of API calls made to SQS while increasing efficiency and latency of you application
- Wait time can be between 1-20 secs (20 sec preferred)
- Long polling is preferred to short polling
- Long polling can be enabled at the queue level or at API level using `WaitTimeSeconds`

### SQS - Extended Client
- Message size limit is 256KB, so to send large messages use SQS extended client (Java library)

![](./images/SQSExtendedClient.PNG)

### SQS - APIs
- `CreateQueue(MessageRetentionPeriod)`
- `DeleteQueue`
- `PurgeQueue` - Delete all messages in queue
- `SendMessage(DelaySeconds)`
- `RetrieveMessage`
- `DeleteMessage`
- `MaxNumberOfMessages` (default 1, max 10)
- `RecieveMessage`
- `ChangeMessageVisibility`

### SQS - FIFO Queue
- First-in-first-out
- Limited throughput 300 msgs/s without batching (3000 msg/s with batching)
- Exactly one send capability (by removing duplicates)
- Messages processed in order by consumer

### SQS FIFO - Deduplication
- De-duplication interval is 5 mins
#### 2 deduplication methods: 
- Content-based deduplication - will do a SHA-256 hash of the message body
- Explicity provide a `MessageDeduplicationID`

### SQS FIFO - Message Grouping
- If you specify the same value is `MessageGroupID` in an SQS queue, you can only have one consumer and all the messages are in order
- To get ordering at the level of a subset of messages, specify different values for `MessageGroupID`, messages that share a common `MessageGroupID` will be in order within the group. Each ID can have a different consumer, although ordering is not guaranteed within the group.

### SQS & SNS Fan-out
- Push to SNS, receive in all SQS queues that subscribe to the topic
- Fully decoupled - no data loss
- SQS allows for: data persistence, delayed processing and retries
- Ability to add more SQS subscribers
- Make sure your SQS access policy allows SNS to write
- For the same combination of event type (eg. object create) and prefix (eg. images/) you can only have one S3 event rule
- To send the same event to many SQS queues use fan-out