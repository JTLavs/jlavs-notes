---
title: AWS - Encryption
date: "2022-02-19"
tags: ["aws"]
---
### AWS KMS
- Easy way to control access to data - AWS manages keys
- Fully integrated with IAM
- Seamlessly integrated into EBS, S3, Redshift, RDS, SSM

### KMS - Customer managed keys
- Able to fully manage key policies
- Able to audit usage using CloudTrail
- Three types of CMK: AWS managed (free), User keys KMS ($1/mo), User keys ($1/mo)
- Pay for API calls to KMS
- Use KMS anytime you need to share information
- User can never retrieve the CMK used to encypt the data and CMK can be rotated
- Can only encrypt up to 4KB of data per call
- If greater than 4KB use envelope encryption

#### Symmetric (AES-256)
- Single key used to encrypt/decrypt
- Necessary for envelope encryption
- No access to the key unencrypted (must call KMS APIs to use)
#### Asymmetric
- Public (encrypt) and Private (decrypt) key pair
- Used for encrypt/decrypt or sign/verify operations
- Public key is downloadable
- Use case: encryption outside of KMS by users who can't access KMS API.

### KMS Key Policies
- Cannot control access without a key policy
#### Default
- Created if no other policy is specified
- Complete access to the key to the root = entire account
- Gives access to the IAM policies to the KMS key
#### Custom
- Define users, roles that can access the KMS key
- Define who can administer the key
- Useful for cross-account access

### Copying snapshots across accounts
1. Create snapshot encrypted with your own CMK
2. Attach a KMS key policy to authorize cross-account access
3. Share encrypted snapshot
4. In target, create a copy of the snapshot using a key in your account
5. Create volume from snapshot

### Envelope encryption
- KMS `Encrypt` API call has a limit of 4 KB
- If we need larger use Envelope encryption
- Using `GenerateDataKey` API
- AWS SDK implements Envelop Encryption for us
- SDK Features: Re-use data keys instead of creating new ones for each encryption, helps with reducing the no. of call to KMS with security trade-off and use `LocalCryptoMaterialsCache(max age, max bytes, max no. messages)`

### KMS Symmetric Summary
- `Encrypt`: encrypt up to 4KB of data through KMS
- `GenerateDataKey`: generates a unique symmetric data key (DEK), returns a plaintext copy of the data key and a copy that is encrypted under the CMK you specify
- `GenerateDataKeyWithoutPlaintext`: Generate a DEK to use at some point (not immediately), DEK is encrypted under the CMK you specify and you must use `Decrypt` later.
- `Decrypt` - Decrypt up to 4KB of data (including DEKs)
- `GenerateRandom` - Return a random byte string

### KMS Request Quotas
- When you exceed a request quota, you will get a ThrottlingException
 - To respond use exponential backoff (backoff & retry)
 - For cryptographic operations, they share a quota
 - Includes requests made on your behalf
 - For `GenerateDataKey` consider using DEK caching from the Encryption SDK
 - Can request a Request Quota increase through API or AWS support.

 ### SSM Parameter Store
 - Secure storage for configuration and secrets
 - Optional seamless encryption using KMS
 - Serverless, scalable, durable, easy SDK
 - Version tracking of configurations/secrets
 - Configuration management using path & IAM
 - Notifications with CloudWatch Events
 - Integration with CloudFormation

### Parameter Policies (for advanced parameters)
- Allow to assign a TTL to a parameter (expiration date) to force updating or deletion of sensitive data
- Can assign multiple policies at a time

### AWS Secrets Manager
- Newer service, meant for storing secrets
- Capability to force rotation of secrets every X days
- Automate generation of secrets on rotation
- Integration with RDS
- Secrets encrypted using KMS
- Mostly meant for RDS integration

### Parameter Store vs Secrets Manager
#### Secrets Manager
- Automatic rotation of secrets with AWS Lambda
- Integration with RDS, Redshift, Document DB
- KMS encryption is mandatory
- Can integrate with CloudFormation
#### Parameter store
- Simple API
- No secret rotation
- KMS encryption optional
- Can integrate with CloudFormation
- Can pull secrets from Secrets Manager using Parameter Store API