# AWS VPCs
- Private network to deploy your resources
- Subnets allow you to partition your network inside your VPC
- Public subnets are accessible from the internet
- Private subnets are not accessible from the internet
- To define access to the internet and between subnets we use Route Tables

### Internet and NAT Gateways
- Internet gateways help our VPC instances connect with the internet
- Public subnets have a route to the internet gateway
- NAT gateways (managed by AWS) and NAT instances(self-managed) allow instances in your private subnets to access the internet while remaining private.

### Network ACLs and Security Groups
#### NACL
- A firewall which controls traffic from and to a subnet
- Can have ALLOW and DENY rules 
- Are attached at the subnet level
- Rules only include IP addresses
- Stateless - Return traffic must be explicitly allowed
- Process rules in number order when deciding whether to allow traffic
#### Security groups
 - Operates at instance level
 - Supports ALLOW rules only
 - Stateful - Return traffic automatically allowed, regardless of rules
 - Evaluate all rules before deciding on any traffic
 - Rules can include IP addresses and other security groups 
 - Applies to instance only if instance associated with the security group.

 ### VPC Flow Logs
 - Capture information about IP traffic going to your interfaces
 - VPC Flow logs, Subnet flow logs and Elastic Network interface flow logs
 - Helps to monitor and troubleshoot connectivity issues eg. Subnets to internet, Subnets to subnets
 - Captures network information from AWS managed interfaces to: Load balancers, ElastiCache, RDS, Aurora etc
 - VPC Flow Logs data can go to S3/Cloudwatch Logs

 ### VPC Peering
 - Connect two VPC, privately using AWS network
 - Make them behave as if they were in the same network
 - Must not have overlapping CICR (IP address range)
 - VPC Peering connection is not transistive (must be established for each VPC that needs to communicate with one another)

 ### VPC Endpoints
 - Allow you to connect to AWS services using a private network instead of the public www network.
- Gives you enhanced security and lower latency to AWS services
- VPC Endpoint Gateway can be used for S3 and DynamoDB
- VPC Endpoint Interface is used for all other services
- Can only be used within your VPC

### Site-to-site VPN and Direct Connect(DX)
#### Site-to-Site
- Connect an on-premises VPN to AWS
- Connection automatically encrypted
- Goes over public internet
#### Direct Connect (DX)
- Establish a physical connection between on premises and AWS
- Connection is private, secure and fast
- Goes over private network
- Takes at least a month to establish

 