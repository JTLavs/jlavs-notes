# AWS EC2, EBS & EFS
### EC2 User data
- Possible to bootstrap instances using a User Data script
- Only run once
- Used to automate tasks such as: installing updates, updating software and download common files.
- Script runs with the root user

### EC2 Instance types
#### General purpose
- Great for diversity workloads such as web servers or code repositories
- Balance between Compute, Memory, Networking

#### Compute Optimized
- Great for compute intensive tasks that require high performance processors such as Batch processing, Media transcoding, high performance web servers, high performace computing, dedicated game servers

#### Memory Optimized
- Fast performance for workloads that process large data sets in memory
- Use cases: High performance relational/non-relational databases, distributed web scale cache stores, in-memory databases optimized for BI, applications performing real-time processing of unstructured data.

#### Storage Optimized
- Great for storage intensive tasks that require high, sequential read & write access to large data sets on local storage.
- Use cases: High frequency online transactional processing (OLTP) systems, relational and NoSQL databases, cache for in-memory databases, data warehousing applications, distributed file systems

### Security Groups
- Fundamental to network security in AWS
- Control what traffic is allowed into of out of EC2 instances
- Can only contain allow rules
- Can reference by IP or by security group
- Acts as a firewall on EC2 instances
- Regulates: access to ports, authorised IP ranges, control of inbound network, control of outbound network
- Can be attached to multiple instances
- Region/VPC locked
- All inbound traffic is denied by default
- All outbound traffic is authorised by default


### EC2 Instance purchasing options
#### On demand instance
- Short workload, predictable pricing
- Highest cost, no upfront payment
- Pay for what you use

#### Reserved instances
- Reserved a specific instance type 
- Recommended for steady-state usage applications
- Convertible reserved instances - you can change the instance type
- Scheduled reserved instances - launch within a time window you reserve only a 1 year commitment

#### Spot instances
- Instances you can 'lose' at any point in time if your max price is less than the current spot price
- Useful for workloads that are resiliant to failure
- Not suitable for critical jobs or databases 

#### EC2 Dedicated Hosts
- A physical server fully dedicated to your use. 
- Dedicated hosts can help yoou addresss compliance requirements and reduce costs by allowing you to use your existing server-bound software licenses.
- Useful for software that has a complicated license model
- Or companies that have strong regulatory/compliance needs

#### Dedicated instances
- Instances running on hardware that is dedicated to you
- May share hardware with other instances in the same account
- No control over instance placement

### EBS
- A network drive you can attach to EC2 instances
- Can nonly be mounted to one instance at a time
- Bound to availability AZ
- To move volume across zones you need to snapshot it
- By default the root volume is deleted on termination and other volumes are persisted. The root can be perserved by using the 'Delete on termination' attribute

### AMI (Amazon Machine Image)
- Customization of EC2 instance
- Built for specific region but can be copied across

### EC2 Instance Store
- EBS drives are network drives with good, but limited performance
- If you need a high performance hardware disk use EC2 instance store for: better I/O performance, good for buffer/cache/scratch data/temporary content
- There is risk of data loss if the hardware fails
- Backups and replication are your responsibility

### EBS volume types
#### gp2/gp3
- General purpose SSO volume that balances price and performace for a wide variety of workloads
- Can be used as a boot volume

#### io1/io2
- Highest performance SSO volume for mission-critical low-latency or high-throughput workloads
- Can be used as a boot volume

#### st1
- Low cost HDD volume designed for frequently accessed throughput intensity workloads
- Cannot be used as a boot volume

#### sc1
- Low cost HDD volume designed for less frequently accessed workloads
- Cannot be used as a boot volume

### EBS volume use cases
#### General purpose SSO
- Cost effective low latency
- System boot volumes, virtual desktops, development and test environments

#### Provisioned IOPS (PIOPS) SSO
- Critical business applications with sustained IOPS performance
- For apps that need more than 16000 IOPS
- Great for database workloads
- Supports EBS Multi attach

#### Hard Disk Drives
- Cannot be a boot volume
- st1 for Big Data, log processing - Max throughput: 500 MiB/s
- st2 for data that is infrequently accessed - Max throughput: 250 MiB/s

### EFS - Elastic File System
- Managed network file system that can be mounted on many EC2 instances
- Highly available, scalable and expensive
- For content management, web serving, data sharing, Wordpress
- Use security groups to control access
- Only compatible with Linux-based systems, not Windows

### EFS vs EBS
- EBS can only be attached to one instance at a time
- EBS locked to AZ 
- EFS can be mounted on 100s of EC2 instances across AZs
- EFS has a higher price point
- EFS is only for Linux systems




