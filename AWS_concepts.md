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


## IRSA on EKS

This picture shows the complete IRSA setup from scratch, in the order you would configure it. Follow the six numbered steps along the bottom.
The easiest way to remember the order is:
1. Enable OIDC for your EKS cluster.
2. Create an IAM role with a trust policy that identifies the allowed ServiceAccount.
3. Attach a permissions policy that allows access to the required S3 bucket.
4. Create a Kubernetes ServiceAccount and annotate it with the IAM role ARN.
5. Configure your pod or Deployment to use that ServiceAccount.
6. Verify the identity using aws sts get-caller-identity, then test S3 access.

<img width="1536" height="1024" alt="IRSA on EKS_ Complete Setup Flow" src="https://github.com/user-attachments/assets/b4fc5c29-7433-43cf-b40c-8cb136f98009" />

## Providing EKS access to Dev and QA Teams

# Provide Amazon EKS Access to Dev and QA Teams

This guide shows one way to give Dev and QA teams different permissions in a shared Amazon EKS cluster using:

- AWS IAM roles (ideally assumed through AWS IAM Identity Center / AWS SSO)
- EKS access entries
- Kubernetes namespaces
- Kubernetes `Role` and `RoleBinding`

The example gives the Dev team workload-management permissions in the `dev` namespace and the QA team read-only permissions in the `qa` namespace.

> **Important:** Replace example account IDs, role ARNs, cluster names, and region with your actual values. Apply Kubernetes RBAC manifests using an administrator identity that already has permission to manage RBAC.

## 1. Access design

```text
Dev users -> assume DevTeamEKSRole -> EKS access entry -> Kubernetes group dev-team
                                                        -> RoleBinding in namespace dev

QA users  -> assume QATeamEKSRole  -> EKS access entry -> Kubernetes group qa-team
                                                        -> RoleBinding in namespace qa
```

Authentication and authorization are separate:

- **AWS IAM / IAM Identity Center** authenticates each human user and lets them assume an approved IAM role.
- **EKS access entries** map each IAM role to a Kubernetes username/group.
- **Kubernetes RBAC** decides what that group can do in a namespace.

Avoid shared human credentials. Use individually identifiable users through your organization's SSO process.

## 2. Prerequisites

- An EKS cluster with the `API` or `API_AND_CONFIG_MAP` authentication mode, so EKS access entries are supported.
- AWS CLI configured for an administrator who can manage EKS access entries.
- `kubectl` configured with administrative access to the cluster.
- Two IAM roles that team members can assume through your organization's identity system:
  - `DevTeamEKSRole`
  - `QATeamEKSRole`
- The IAM role used by each team needs permission to call `eks:DescribeCluster` on the target cluster so `aws eks update-kubeconfig` can retrieve cluster connection details.
- The role trust/SSO configuration must be managed according to your organization's IAM standards.

Set example variables in your shell:

```bash
export AWS_REGION="ap-south-1"
export CLUSTER_NAME="production-eks"
export AWS_ACCOUNT_ID="123456789012"
export DEV_ROLE_ARN="arn:aws:iam::${AWS_ACCOUNT_ID}:role/DevTeamEKSRole"
export QA_ROLE_ARN="arn:aws:iam::${AWS_ACCOUNT_ID}:role/QATeamEKSRole"
```

Check the cluster authentication mode:

```bash
aws eks describe-cluster \
  --name "$CLUSTER_NAME" \
  --region "$AWS_REGION" \
  --query 'cluster.accessConfig.authenticationMode' \
  --output text
```

If access entries are not enabled, plan and perform a supported authentication-mode migration before proceeding. Check current AWS EKS documentation and your organization's change process; do not change production authentication settings without review.

## 3. Create namespaces

Run as a Kubernetes administrator:

```bash
kubectl create namespace dev
kubectl create namespace qa
```

If they already exist, verify them instead:

```bash
kubectl get namespace dev qa
```

Namespaces provide resource scoping, but are not a complete security boundary on their own.

## 4. Create EKS access entries and map groups

Create an access entry for the Dev IAM role:

```bash
aws eks create-access-entry \
  --cluster-name "$CLUSTER_NAME" \
  --principal-arn "$DEV_ROLE_ARN" \
  --type STANDARD \
  --region "$AWS_REGION"
```

Map that role to the Kubernetes group `dev-team`:

```bash
aws eks update-access-entry \
  --cluster-name "$CLUSTER_NAME" \
  --principal-arn "$DEV_ROLE_ARN" \
  --kubernetes-groups dev-team \
  --region "$AWS_REGION"
```

Create an access entry for the QA IAM role:

```bash
aws eks create-access-entry \
  --cluster-name "$CLUSTER_NAME" \
  --principal-arn "$QA_ROLE_ARN" \
  --type STANDARD \
  --region "$AWS_REGION"
```

Map that role to `qa-team`:

```bash
aws eks update-access-entry \
  --cluster-name "$CLUSTER_NAME" \
  --principal-arn "$QA_ROLE_ARN" \
  --kubernetes-groups qa-team \
  --region "$AWS_REGION"
```

If an access entry already exists, inspect it with `describe-access-entry` and update it instead of trying to create a duplicate.

Verify the entries:

```bash
aws eks describe-access-entry \
  --cluster-name "$CLUSTER_NAME" \
  --principal-arn "$DEV_ROLE_ARN" \
  --region "$AWS_REGION"

aws eks describe-access-entry \
  --cluster-name "$CLUSTER_NAME" \
  --principal-arn "$QA_ROLE_ARN" \
  --region "$AWS_REGION"
```

The groups in these access entries are Kubernetes RBAC groups. They are not AWS IAM groups, and you do not create Kubernetes `Group` objects for them.

> **Authorization note:** Do not also associate broad EKS access policies with these roles if the intention is to enforce only the namespace-scoped permissions below. EKS access policies and Kubernetes RBAC can both grant access; evaluate all assigned permissions.

## 5. Create namespace-scoped RBAC

Save the following as `dev-qa-rbac.yaml`. It creates a Dev Role and RoleBinding in `dev`, and a QA Role and RoleBinding in `qa`.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: dev-workload-manager
  namespace: dev
rules:
  - apiGroups: [""]
    resources: ["pods", "services", "configmaps"]
    verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
  - apiGroups: ["apps"]
    resources: ["deployments", "replicasets"]
    verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: dev-team-binding
  namespace: dev
subjects:
  - kind: Group
    name: dev-team
    apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: dev-workload-manager
  apiGroup: rbac.authorization.k8s.io
---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: qa-readonly
  namespace: qa
rules:
  - apiGroups: [""]
    resources: ["pods", "services", "configmaps"]
    verbs: ["get", "list", "watch"]
  - apiGroups: [""]
    resources: ["pods/log"]
    verbs: ["get"]
  - apiGroups: ["apps"]
    resources: ["deployments", "replicasets"]
    verbs: ["get", "list", "watch"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: qa-team-binding
  namespace: qa
subjects:
  - kind: Group
    name: qa-team
    apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: qa-readonly
  apiGroup: rbac.authorization.k8s.io
```

Apply the manifest as a cluster administrator:

```bash
kubectl apply -f dev-qa-rbac.yaml
```

### What these permissions mean

- **Dev:** Can create, view, update, and delete the listed pods, services, ConfigMaps, Deployments, and ReplicaSets in `dev`.
- **QA:** Can view the listed resources and read pod logs in `qa`; cannot create or delete those resources through these permissions.
- Neither RoleBinding grants permissions in the other team's namespace.

This is an example policy, not a complete production role. Add only the resource types and actions the teams actually require.

### Security cautions

- Do not grant `secrets` access unless required. Kubernetes Secrets may contain credentials.
- Permission to create or modify pods and Deployments can indirectly expose sensitive information or service-account permissions. Apply Pod Security standards/admission controls and appropriate workload restrictions.
- If Dev must deploy via CI/CD, consider having the pipeline use a dedicated deployment identity rather than giving all human developers broad write access.
- Namespace RBAC does not isolate network traffic. Use NetworkPolicy and cloud/network controls where needed.
- Validate whether other bindings, EKS access policies, or cluster-admin grants provide additional access.

## 6. Configure a team member's local kubeconfig

The team member signs in using the organization's approved identity process (for example, AWS IAM Identity Center), then runs:

```bash
aws eks update-kubeconfig \
  --name "$CLUSTER_NAME" \
  --region "$AWS_REGION"
```

The AWS identity active in their terminal must be able to assume the correct team role and call `eks:DescribeCluster`.

Check the active identity:

```bash
aws sts get-caller-identity
```

Then test access.

Dev user:

```bash
kubectl get pods -n dev
kubectl auth can-i create deployments -n dev
kubectl auth can-i get pods -n qa
```

Expected results for the example policy:
- `get pods -n dev`: allowed
- `create deployments -n dev`: allowed
- `get pods -n qa`: denied, assuming no other permission grants access

QA user:

```bash
kubectl get pods -n qa
kubectl auth can-i get pods/log -n qa
kubectl auth can-i create deployments -n qa
kubectl auth can-i get pods -n dev
```

Expected results for the example policy:
- `get pods -n qa`: allowed
- `get pods/log -n qa`: allowed
- `create deployments -n qa`: denied, assuming no other permission grants access
- `get pods -n dev`: denied, assuming no other permission grants access

`kubectl auth can-i` checks authorization, but the commands should be run with the actual team's credentials to verify the complete authentication and authorization path. Administrator-side impersonation checks can be useful, but do not replace testing with the real user identity.

## 7. Troubleshooting checklist

If a team member gets `Unauthorized`:
- Confirm the active AWS identity with `aws sts get-caller-identity`.
- Confirm the user can assume the intended IAM role.
- Confirm the IAM role ARN has an EKS access entry.
- Confirm the cluster supports access entries.
- Check AWS CLI and kubeconfig context/region/cluster name.

If a team member gets `Forbidden`:
- Authentication likely succeeded, but authorization did not permit the requested action.
- Confirm the access entry maps to the expected group.
- Confirm the RoleBinding's group name exactly matches the mapped group.
- Confirm the Role and RoleBinding are in the intended namespace.
- Check for missing verbs/resources and for other EKS access policies or RBAC bindings.

<img width="1536" height="1024" alt="EKS Dev and QA Access Flow Guide" src="https://github.com/user-attachments/assets/0c0e6b96-7892-4583-9e46-b82880a0991c" />



