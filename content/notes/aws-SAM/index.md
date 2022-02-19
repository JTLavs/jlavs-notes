---
title: AWS - Serverless Application Model (SAM)
date: "2022-02-19"
tags: ["aws"]
---
- Framework for developing and deploying serverless applications
- All the config is YAML code
- Generate complex CloudFormation from simple YAML files
- Supports anything from CloudFormation: Outputs, Mappings, Parameters, Resources
- SAM can use CodeDeploy to deploy Lambda functions
- SAM can help you run Lambda, API Gateway, DynamoDB locally
- Transform header indicates it is a SAM template eg.  `Transform:'AWS::Serverless-2016-10-31'`
- Write Code:
    - AWS::Serverless::Function
    - AWS::Serverless::API
    - AWS::Serverless::SimpleTable
- Package and Deploy
    - `aws cloudformation package/sam package`
    - `aws cloudformation deploy/sam deploy`
- SAM policy templates for easy IAM policy definition
- SAM is integrated with CodeDeploy to deploy Lambda aliases