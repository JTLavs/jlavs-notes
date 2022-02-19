---
title: AWS - Xray
date: "2022-02-19"
tags: ["aws"]
---

- Visual analysis of our applications
- Troubleshooting performance bottlenecks
- Understand dependancies in a microservice architecture
- Pinpoint service issues
- Review request behaviour
- Find errors & exceptions
- Identify users which are impacted

### Compatible with:
- Lambda
- Elastic Beanstalk
- ECS
- ELB
- API Gateway
- EC2 instances or any application server

### X-Ray tracing
- End-to-end way to follow a 'request'
- Each component dealing with the request adds on its trace
- Tracing is made up of segments and sub-segments
- Annotations can be added to provide extra information
- Ability to trace every request or sample request
- Security provided by IAM for authentication and KMS for encryption at rest
- Collects data from all the different services
- Service map is computed from all the segments and traces
- Graphical, so non-technical people can understand it

### Enabling X-Ray
- Code imports the AWS X-Ray SDK
    - Very little modification needed
    - Captures calls to AWS services, HTTP/HTTPS requests, database calls, queue calls
- Install X-Ray daemon
    - Works as a low-level UDP packet interceptor
    - AWS Lambda/other services already run the daemon
    - Must have IAM rights to write data to X-Ray
- If X-Ray is not working on EC2:
    - Ensure EC2 role has proper permissions
    - Ensure EC2 is running the X-Ray daemon
- If X-Ray is not working on Lambda
    - Ensure it has an IAM execution role (`X-RayWriteOnlyAccess`)
    - Ensure X-Ray is imported in the code

### X-Ray instrumentation in your code
- Instrumentation means the measure of products performance, diagnose errors and write trace information
- To instrument your application code use the AWS X-Ray SDK


### X-Ray Concepts
- Segments: each application/service will send them
- Subsegments: if you need more details in your segment
- Trace: segments collected together to form an end-to-end trace
- Sampling: decrease the amount of requests send to X-Ray, reduce cost
- Annotations: Key-value pairs used to index traces and use with filters
- Metadata: Key-value pairs not indexed, not used for searching

- The X=Ray daemon/agent has a config to send traces cross account
    - Make sure IAM permissions are correct - agent will assume the role
    - Allows to have a central account for all your application tracing

### X-Ray Sampling rules
- With sampling rules, you control the amount of data that you record
- Can modify sampling rules without changing your code
- By default, the SDK records the first request each second, and 5% of any additional requests
- One request per second is the reservoir, which ensures that at least one trace is recorded each second as long as the service is serving requests
- 5% is the rate at which additional requests beyond the reservoir side are sampled
- You can create your own rules with the reservoir and rate

### X-Ray APIs
- `PutTraceSegments` - Upload segment documents to AWS X-Ray
- `PutTelemetryRecords` - Used by the AWS X-Ray daemon to upload telemetry: SegmentsRecievedCount, SegmentsRejectedCount
- `GetSamplingRules` - Retrieve all sampling rules (to know what/when to send)
- `GetSamplingTargets`
- `GetSamplingStatisticSummaries`
- `GetServiceGraph` - main graph
- `BatchGetTraces` - Retrieves a list of traces specified by ID. Each trace is a collection of segment documents that originates from a single request
- `GetTraceSummaries` - Retrieves IDs and annotations from traces available for a specified time frame using an optional filter. To get all full traces pass IDs to `BatchGetTraces`
- `GetTraceGraph` - Retrieves a graph for one or more specific trace IDs
- X-Ray daemon needs to have an IAM policy authorizing the correct API calls to function

### X-Ray with ElasticBeanstalk
- AWS Elastic Beanstalk platforms include X-Ray daemon
- Can run the daemon by settings an option in the EB console or in `.ebextensions` add a configuration file (`x-ray-daemon.config`)
- Ensure instance has correct IAM permissions so that the X-Ray daemon can function
- Ensure app code is instrumented with X-Ray SDK
- X-Ray daemon is not provided for multi-container Docker

### Elastic Container Service + X-Ray integration options
- X-Ray container as a daemon

![](./images/X-RayContainerDaemon.PNG)

- X-Ray container as a 'side-car'

![](./images/X-RayContainerSidecar.PNG)
