## WAF

For creating ALB, first create target group and attach EC2 / Lambda to that target group, and then attach that target group to ALB / NLB.
And then attach that ALB with WAF.

<img width="1039" height="581" alt="image" src="https://github.com/user-attachments/assets/997c55fa-1572-4a06-86d8-a20024bee7e2" />


## ECS

<img width="1536" height="1024" alt="AWS ECS Overview Infographic" src="https://github.com/user-attachments/assets/956fa06d-ba8c-4094-ba97-ccf1347f7064" />

## AWS Systems Manager

This service is mainly used to manage 100's of EC2, VMs on another cloud, VMs running in on-premise DC.
Provides secure, one-click browser-based interactive shells or CLI access to your instances without needing open inbound ports, SSH keys, or bastion hosts. Install the agent on all machines, from you can access it.

<img width="1536" height="1024" alt="AWS Systems Manager Infographic Guide" src="https://github.com/user-attachments/assets/59c4f6ba-7743-44f6-a860-2926809c48de" />

## Web app on Private subnet
If web app is placed on EC2 of private subnet, if you want to access the customers, then ALB/NLB is the best option.


## KMS

Key managed service by AWS

<img width="1536" height="1024" alt="AWS S3 KMS Encryption Flow" src="https://github.com/user-attachments/assets/c12be650-ac54-4eb3-ac8a-7dd90dc20d49" />

## RDS Overview concepts

<img width="1312" height="1199" alt="AWS RDS DevOps Interview Infographic" src="https://github.com/user-attachments/assets/5db9aa3a-3bab-4f2c-b096-cc61013e8665" />

## AWS Document DB

This is Non SQL DB managed by AWS, serverless and autoscaled by AWS. It work on Mongo DB framework.
Aurora --> Serverless SQL DB managed by AWS

<img width="1536" height="1024" alt="ChatGPT Image 7 Oct 2026, 11_58_34" src="https://github.com/user-attachments/assets/eda7aa55-b6b8-4159-a2ce-22d3855b5cdf" />

## API Gateway

API Gateway acts as a "front door" for applications to access data, business logic, or functionality from backend services, such as AWS Lambda functions, Amazon EC2 instances, or any publicly accessible HTTP endpoint

<img width="934" height="396" alt="image" src="https://github.com/user-attachments/assets/edc04f7d-d655-478e-889d-5da046afd01c" />

Advantage of using API Gateway

<img width="804" height="241" alt="image" src="https://github.com/user-attachments/assets/ceb13c47-6bb0-43b1-b7e8-ae50884b83be" />

## AWS Cognito

Amazon Cognito is a fully managed cloud service that provides secure user authentication, authorization, and management for web and mobile applications

<img width="1536" height="1024" alt="AWS Cognito Authentication Flow Infographic" src="https://github.com/user-attachments/assets/238b79b3-2488-41b3-9391-79eed95221be" />

## Layers in Lambda

If you're writing a python code for Lambda function and if you're importing multiple libaries on code, Lambda need those libraries to execute that code, that's why we create layer and upload those packages as zipped file from local to layer's, during code run time those packages will be utilized.

<img width="589" height="252" alt="image" src="https://github.com/user-attachments/assets/ca73845f-3628-4649-855a-82a260e15b84" />

## AWS Bedrock

One of the example on how bedrock is used

<img width="1089" height="612" alt="image" src="https://github.com/user-attachments/assets/06850752-3d59-4ecb-9b07-86cb4d4070a9" />

## How EKS PODs are communicating with S3

We need a secure connectivity between EKS to S3, hence we're using VPC endpoints.

<img width="1672" height="941" alt="EKS" src="https://github.com/user-attachments/assets/1cfb8b50-e2bf-4c03-b669-102783137098" />

## Why can't we use NAT Gateway for the above scenerio ???

<img width="1672" height="941" alt="EKS 2" src="https://github.com/user-attachments/assets/273de12f-d7e0-4687-8ed0-6b76aea834f4" />

## How PODs running in private subnet will communicate with External APIs 

Create a NAT gateway in public gateway, update the route table, so the POD will send the traffic to NAT --> IG --> External API

<img width="1672" height="941" alt="EKS 3" src="https://github.com/user-attachments/assets/fd39d378-0077-484d-ab75-c7614cfcf877" />

## SAST, DAST and SCA

We've to implement SAST (Sonarqube) to check our code quality, any bugs, SCA (Trivy) for dependency issues check and DAST (OWASP ZAP) for attaching from UI and checks the API quality and everything.

SAST — SonarQube: examines source code without running the application. It can find insecure code patterns, hard-coded secrets, bugs, and maintainability issues.

SCA — Trivy (example): examines dependency manifests and lockfiles, and can scan built container images for known vulnerabilities in direct and transitive libraries or OS packages. Other SCA options include Snyk, Mend, and OWASP Dependency-Check.

DAST — OWASP ZAP: tests a running web application in a reachable test or staging environment. It can find exposed endpoints, some authentication or access-control problems, and runtime web vulnerabilities. An authenticated scan needs test credentials.

<img width="1672" height="941" alt="SAST" src="https://github.com/user-attachments/assets/e3af0ab4-0037-4e2b-9147-dfc93d8304b4" />

## Split DNS

Split DNS is a DNS configuration that returns different IP addresses for the same domain name depending on whether the request comes from an internal or external network.
Let's understand it using your AWS/Azure application architecture.

1. What is Split DNS?
2. 
Suppose your application is hosted at:
app.example.com
You want:
- External customers: Access the application through WAF.
- Internal customers: Access the application directly through a private load balancer, bypassing WAF.
Instead of creating different application URLs, you can use Split DNS to resolve the same hostname differently.

Architecture


                   Application
                app.example.com
                       |
             +---------+---------+
             |                   |
             v                   v
      External Customer    Internal Customer
             |                   |
             v                   v
        Public DNS          Private DNS
             |                   |
             v                   v
       Public WAF           Internal ALB
             |                   |
             v                   v
       Public ALB           Private Backend
             |                   |
             +---------+---------+
                       |
                       v
                  Application


## AWS Config

AWS Config is an AWS service that continuously records supported resource configurations and evaluates them against rules to determine whether resources comply with your organization's policies.

https://www.youtube.com/watch?v=ig1KxKgN8ls

Think of it as a configuration compliance and change-tracking service for AWS infrastructure.

For example, your company requires that:
- Every S3 bucket must block public access.
- Every EBS volume must be encrypted.
- All production EC2 instances must have specific tags.
- Security groups must not expose SSH (port 22) to the entire internet.
AWS Config helps you detect resources that violate these requirements and track configuration changes over time.

<img width="698" height="490" alt="image" src="https://github.com/user-attachments/assets/1edbf7c9-e9e5-412e-a62f-97bd9ffcbd31" />

## Site to Site VPN, Direct Connect and Private Link

Site to Site -> Provides connection between On prem to AWS through internet
Direct Connect --> Provides connection between On prem to AWS through dedicated network

<img width="719" height="555" alt="image" src="https://github.com/user-attachments/assets/7aaa97f5-be15-41e7-acfd-aeaec3ae2dd4" />

## AWS Shield, Guard duty, Inspector and WAF

<img width="1536" height="1024" alt="AWS Security Services Integration Guide" src="https://github.com/user-attachments/assets/9e893683-ebc9-4a15-be2a-451dce8ad139" />

## Diff b/w Gateway vs Interface endpoints

<img width="1536" height="1024" alt="AWS Endpoints_ Gateway vs Interface" src="https://github.com/user-attachments/assets/6f1eeab9-353d-4005-a02e-f23e73e4b36a" />

## Cross account IAM role

User from account-A will access the bucket on Account-B on pic

Below pic might be confusing, see the below youtube video for better understanding

https://youtu.be/RdZnO_11A9g?si=IEBgzwkp5RY6n53_

<img width="1536" height="1024" alt="Cross-Account IAM Role Access Flow" src="https://github.com/user-attachments/assets/13ad215e-d529-4455-bc8d-f726b4b9623f" />

## AWS backup

<img width="1536" height="1024" alt="AWS Backup Architecture Infographic" src="https://github.com/user-attachments/assets/e862dd9b-a0ae-4d97-8770-f5acc2f34d11" />

## S3 cross region replication

<img width="1536" height="1024" alt="AWS S3 Cross-Region Replication Guide" src="https://github.com/user-attachments/assets/6630b893-498f-41cb-9a8d-5024f46544c8" />




