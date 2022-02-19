---
title: AWS - Policy types
date: "2022-02-19"
tags: ["aws"]
---

### Identity-based policies
- Attach managed and inline policies to IAM identities (users, groups to which users belong, or rules).
- Grant permissions to an identity
### Resource-based policy
- Attach inline policies to resources g. S3 bucket policy, IAM trust policy
- Grant permissions to the principal specified. 
- Principals can be in the same account as the resource or in other accounts
### Permissions boundary
- Defines the maximum permissions that the identity-based policies can grant to an identity 
- Does not grant permissions.
- Do not define the maximum permissions that a resource-based policy can grant to an entity
### Organizations SCPs
- Define maximum permissions for account members of an organisation or organisation unit. 
- Limit permissions that identity-based policies or resource-based policies can grant to entities with the account
- Does not grant permissions
### Session policies
- Advanced policies that you pass as a parameter when you programatically create a tempory session for a role/federated user.
- Permissions are the intersection of the identity-based policy of the entity used to create the session and the session policies
- Permissions can also come from a resource-based policy
- A resource-based policy can specify the ARN of the session as the principal. In this case the permissions from the resource-based policy are added after the session is created. The resource-based policy is not limited by the session policy. The resulting session has all the permissions of the resource-based policy plus the intersection of the identity-based policy and the session policy.
- A permissions boundary can set the max. permissions for a user/role that is used to create a session. In this case, the resulting session permission is the intersection of the session policy, the resource-based policy and the identity-based policy. However, a permissions boundary does not limit permissions granted by a resource-based policy that specifies the ARN of the resulting session.