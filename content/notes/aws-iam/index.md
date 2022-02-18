---
title: AWS - Identity and Access Management
date: "2022-02-16"
tags: ["aws", "cloud"]
---
- Root account created by default - shouldn't be shared
- Users are prple within organisation and can be grouped
- Groups only contain users not other groups
- Users don't have to belong to a group and one user can belong to multiple groups

### Permissions
- Users/groups can be assigned JSON policies
- These define the permissions of users
- You apply the least privilege principal. don't give a user more permissions than needed
- There are 3 ways to access AWS:
    - AWS console (protected by password & MFA)
    - AWS CLI (protected by access keys)
    - AWS SDK (access keys)

### Roles for services
- Some AWS services will need to perform actions on your behalf
- We assign them permissions using AWS roles

### Security tools
- IAM credentials report (account-level)
    - lists all your account users and status of their credentials
- IAM access advisor (user-level)
    - access advisor shows the service permissions granted to a user and when those services were last accessed
    - information useful to revise policies

### Shared responsibility model
- AWS
    - Infrastructure (global network security)
    - Configuration & vulnerability analysis
    - Compliance validation
- User
    - Users, groups, policies management and monitoring
    - Enable MFA on all accounts
    - Rotate all your keys often
    - Use IAM tools to apply appropriate permissions
    - Analyze access patterns & review permissions

### AWS - STS (Secure Token Service)
- Allows to grant temporary permissions and limited access to AWS resources (up to an hour)
- `AssumeRole`- assume roles within your account or cross account
- `AssumeRoleWithSAML` - return credentials for users logged with SAML
- `AssumeRoleWithWebIdentity` - return credentials for users logged with an Identity provider, AWS recommends against using this and using Cognito Identity Pools instead
- `GetSessionToken` - for MFA from a user or account root user
- `GetFederationToken` - obtain temporary credentials for a federated user
- `GetCallerIdentity` - return details about the IAM user or role used in the API call
- `DecodeAuthorizationMessage` - decode error message when an APi is denied

### Using STS to assume a role
- Define a role within your account or cross-account
- Define which principals can access this role
- Use AWS STS to retrieve credentials and impersonate the IAM role you have access to (`AssumeRole`)
- Temporary credentials can be valid between 15 mins to 1 hour

### STS with MFA
- Use `GetSessionToken` from STS
- Appropriate IAM policy using IAM conditions
- `aws:MultiFactorAuthPresent:true`
- `GetSessionToken` returns:
    - AccessID
    - SecretKey
    - SessionToken
    - ExpirationDate

### IAM best practice
- Never use root credentials, enable MFA for root
- Grant least privlidge
    - Each group/user/role should only have the minimum level of permission it needs
    - Never grant a policy of `*` to access a service
    - Monitor API calls made in CloudTrail
- Never store IAM credentials on any machine but a personal computer or on-premise server
- On premise-server best practice is to call STS to obtain temporary security credntials
- EC2 machines should have their own roles
- Lambdas should have their own roles
- ECS tasks should have their own roles
- CodeBuild should have its own service role
- Create a least-privlidged role for any service that needs it
- Create a role per app/function (do not reuse roles)

### Authorization model evaluation of policies
1. If there is an explicity DENY, end & DENY
2. If there is an ALLOW, end with ALLOW
3. Else DENY

### IAM policies & S3 bucket policies
- IAM policies are attached to users, roles and groups
- S3 policies are attached to buckets
- When evaluating if an IAM pricipal can perform an operation on a bucket the UNION of its assigned IAM & S3 policies are evaluated

### Inline vs managed policies
- AWS managed policy
    - Maintained by AWS
    - Good for power users and admins
    - UPdated incase of new services/APIs
- Customer managed policy
    - Best practice, re-useable, can be applied to many principals
    - Version controlled + rollback, central change management
- Inline
    - Strict 1-to-1 relationship between policy & principal
    - Policy deleted if you delete the principal

### Granting a user permissons to pass a role to an AWS service
- To configure many services, you must pass an IAM role to the service
- The service will later assume the role and perform actions
- For this you need the IAM permission `iam:PassRole`
- It often comes with `iam:GetRole` to view the role being passed
- Roles can only be passed to what their trust allows