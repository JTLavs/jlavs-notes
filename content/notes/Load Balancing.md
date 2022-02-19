# AWS Load Balancing

### Vertical Scalability 
- scaling size of instance

### Horitontal Scalability 
- increasing no. of instances

### High Availablity 
- Running application in multiple AZs
- Goal is to survive data centre loss

### Load Balancers
- Forward traffic to multiple instances
- Spread load across multiple downstream instances
- Expose a single point of access
- Enforce stickiness with cookies
- High availability

### Types of Load Balancers
- Classic Load Balancer - HTTP, HTTPS, TCP
- Application Load Balancer - HTTP. HTTPS, WebSocket
- Network Load Balancer - TCP, TLS, & TDP
- Recommended to use newer/V2 generation load balancers as they have more features
- Can scale but not instantaenously 
- ELB access logs will log access requests

### Application Load Balancers
- ALB has a fixed hostname
- Application servers don't see client IP directly. 
- True IP is inserted into the header `X-Forwarded-For`
- Can also set port and proto this way
- Load balance to multiple applications on same machine (containers)
- Load balance to multiple apps across machines (target groups)
- Routing based on:
    - path url
    - query string, headers
    - hostname in URL
- Has port mapping feature to redirect to a dynamic port in ECS

### ALB (v2) Target groups
- EC2 instances (managed by ASGs) - HTTP
- ECS tasks (managed by ECS itself) - HTTP
- Lambda functions - HTTP request translated to JSON event
- IP addresses - must be private IPs
- Can route to multiple target groups
- Health checks at target group level

### Network Load Balancers (NLB)
- Allow you to forward TCP & UDP traffic to your instances
- Handle millions of requests per second
- Less latency
- Has one static IP per AZ, and supports assigning Elastic IPs
- Used for extreme performace, TCP or UDP traffic

### Load Balancer stickiness
- Possible to implement stickiness so the same client is always redirected to the same instance
- Works for classic and application load balancers
- The 'cookie' used for stickiness has an expiration date you control
- Used to ensure user doesn't load their session data
- May bring imbalance to the load over the backend EC2 instances

### Connection Draining
- Target group - deregistration delay
- Classic Load Balancer - connection draining
- Time to complete "in-flight" requests while the instance is deregistering
- Stops sending new requests to the instance which is deregistering
- Between 1-3600 seconds, default is 300
- Can be disabled (set value to 0)
- Set to a low value if requests are short

### Auto scaling groups
- The goal of an ASG is to:
    - Scale out (add instances) to match increased load
    - Scale in (remove instances) to match decreased load
    - Ensure we have minimum and a maximum no. of machines running
    - Auto register new instances to a load balancer

### Auto scaling alarms
- Possible to scale based on CloudWatch alarms
- An alarm monitors a metric (such as avg. CPU)
- Metrics are computed for the overall instances

### SSL Certificates
- Uses X.509 certificate (SSL/TLS certificate)
- Manage certificates using ACM
- Create and upload your own certificates
- HTTPS Listener
    - Must specify a default attribute
    - You can add an optional list of certs
    - Clients can use SNI (Server Name Indication) to specify the host name they reach
- Classic Load Balancer
    - Supports only one SSL certificate
    - Must use multiple CLB for multiple hostname with multiple SSL certificates
- Application Load Balancer & Network Load Balancer
    - Supports multiple listeners with multiple SSL certificates
    - Uses SNI (Server Name Indication) to make it work

### Server Name Indication
- Solves the problem of loading multiple SSL certs into one web server
- 'Newer' protocol and requires the client to indicate the hostname of the target server to the initial handshake
- Server will then find the correct certificate or return the default one
- Only works for ALB and NLB.
