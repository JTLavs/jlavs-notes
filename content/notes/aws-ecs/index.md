---
title: AWS - Elastic Container Service
date: "2022-02-19"
tags: ["aws"]
---
### ECS Clusters
- logical groupings of EC2 instances
- instances run the ECS agent
- ECS agents register the instance to the cluster
- EC2 instances run a special AMI, made specifically for ECS

### Task definitions
- Metadata in JSON to tell ECS how to run a docker container
- Contains crucial information around: 
    - Image name
    - port binding for container & host
    - Memory & CPU required
    - Environment variables
    - Networking information
    - IAM role
    - Logging configuration

### ECS Service
- Help define how many tasks should run and how they should be run
- Ensure that the no. of tasks desired is running acorss out fleet of EC2 instances
- Can be linked to Load balancers (ALB/NLB)

### Fargate
- When launching an ECS cluster, we need to create ECS instances
- We need to add instances to scale, so we manage infrastructure
- Fargate is serverless so no infrastructure to manage
- Just create task definitions and to scale increase the task number

### ECS Task Placment
- Only for ECS on EC2 (not Fargate)
- When a new task is launched, ECS must determine where to place it with metric constraints
- When a service scales in, ECS needs to determine what task to terminate
- To assist you can define a task placement strategy & task placement constraints

### Task Placement Process
- Placement strategies are a best effort
- When ECS places tasks, it uses the following process:
    1. Identify the instances that satisfy the CPU, memory and port requirements in the task definition
    2. Idenfity instances that satisfy the placement constraints
    3. Identify instances that satisfy the placement strategies
    4. Select instances for task placement

### Task placement strategies
You can mix strategies together
- Binpack: Place tasks based on the least available amount of CPU/memory. Minimizes the no. of instances in use.
- Random: Place the task randomly
- Spread: Place the task evenly based on a specified value

### Task placement constraints
- distinctInstance: place each task on a different container instance
- memberOf: places tasks on instances that satisfy an expression. Uses Cluster Query Language (CQL)

### ECS IAM
### EC2 instance profile
- Used by ECS agent
- Makes API calls to ECS service
- Send container logs to CW logs
- Pull Docker image for ECR
### ECS Task role
- Allow each task to have a specific role
- Use different roles for different ECS services
- Defined in the task definition

### ECS - Service Auto Scaling
- CPU and RAM tracked in CLoudWatch at the ECS service level
- Target tracking - target a specified CloudWatch metric
- Step scaling - scale based on CW Alarms
- Scheduled scaling - based on predictable changes
- ECS Service Scaling (task level) does not equal EC2 auto scaling (instance level)
- Fargate auto scaling much easier to setup (serverless)

### ECS - Cluster capacity provider
- A capacity provider is used in association with a cluster to determine the infrastructure a task runs on
    - For ECS Fargate the FARGATE and FARGATE_SPOT capacity providers are added automatically
    - For Amazon ECS on EC2, you need to associate the capacity provider with an ASG
- When you run a task/service, you define a capacity provider strategy to prioritize in which provider to run.
- This allows the capacity provider to automatically provision infrastructure for you.

### ECS Data Volumes - EC2 Task Strategies
- EBS volume is already mounted onto EC2 instances
- Allows docker containers to mount the volume to extend the storage capacity of the tasks
- Problem arises if you task moves from one instance to another it will not be the same EBS volume and dat- Use cases: 
    - Mount a data volume between different containers on the same instance
    - Extend the temporary storage of a task

### ECS Data Volumes - EFS File Systems
- Works for both EC2 tasks and Fargate tasks
- Ability to mount EFS volumes onto tasks
- Tasks launched in any AZ will be able to share the same data in the EFS volume
-  Fargate + EFS = serverless + data storage without managing servers
- Use case: persistent multi-AZ shared storage for your containers

### ECS Volumes - Bind Mounts - Sharing data between containers
- Works for both EC2 tasks (using local instance storage) and Fargate tasks (get 4GB volume mounts)
- Useful to share an ephemeral storage between multiple containers part of the same ECS task
- Great for 'sidecar' container pattern where sidecar can be used to send metrics/logs to other destinations (separation of concern)

### Notes on ECS
- ECS Classic
    - EC2 instances must be created
    - Must configure the file `/etc/ecs/ecs.config` with cluster name
    - Instance must run ECS agent
    - Instances can run multiple containers on the same type:
        - You must not specify a host port (only container port)
        - Should use an ALB with dynamic port mapping
        - EC2 security group must allow traffic from ALB on all ports
- ECS tasks can have IAM roles to execute actions against AWS
- Security groups operate at instance level not task level
