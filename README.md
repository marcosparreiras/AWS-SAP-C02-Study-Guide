# AWS SAP-CO2 Study Guide
The following guide is my attempt at helping myself and possibly others to pass the AWS Certified Solutions Architect Professional Exam.  I'd recommend taking Stephane Maarek's course [Ultimate AWS Certified Solutions Architect Professional 2023](https://www.udemy.com/course/aws-solutions-architect-professional/) and taking a few practice exams from [AWS Certified Solutions Architect Professional Practice Exam](https://www.udemy.com/course/aws-certified-solutions-architect-professional-aws-practice-exams/) before moving onto the exam.  

Note: The author makes no promises or guarantees on this guide as this is as stated, a guide used by myself, nothing more.  This is a work in progress and I haven't passed the test, yet.  

## Table of Contents
1. <a href="#introduction">Introduction</a>
2. <a href="#organizational-unit-ou">Organizational Unit (OU)</a>
3. <a href="#identity-and-access-management-iam">Identity and Access Management (IAM)</a>
4. <a href="#ec2">EC2</a>
5. <a href="#containers">Containers</a>
6. <a href="#logging-and-events">Logging and Events</a>
7. <a href="#configurations-and-security">Configurations and Security</a>
8. <a href="#vpc">VPC</a>
9. <a href="#storage">Storage</a>
10. <a href="#database">Database</a>
11. <a href="#analytics">Analytics</a>
12. <a href="#miscellaneous">Miscellaneous</a>
13. <a href="#acronyms">Acronyms</a>

## Introduction
<a href="https://d1.awsstatic.com/training-and-certification/docs-sa-pro/AWS-Certified-Solutions-Architect-Professional_Exam-Guide.pdf">AWS Certified Solutions Architect - Professional (SAP-C02) Exam Guide</a>
### Exam Content Breakdown:

| Domain  | % of Exam |
| ------------- | ------------- |
| Domain 1: Design Solutions for Organizational Complexity  | 26%  |
| Domain 2: Design for New Solutions  | 29%  |
| Domain 3: Continuous Improvement for Existing Solutions | 25% |
| Domain 4: Accelerate Workload Migration and Modernization | 20% |
| **Total** | **100%** |

## Identity and Access Management (IAM)

Allow vs Deny: If any denial in policy is present, the resource is denied regardless of allow statement(s).  The default behavior is to deny resource(s) and resource(s) need allow statements to be allowed.  

LDAP: software protocol for enabling the location of data about organizations, individuals and other resources in a network.  

Identity federation: a system of trust between two parties for the purpose of authenticating users and conveying information needed to authorize their access to resources.

User groups can only contain users

S3 Bucket Policies vs Access permissions:
  * Used to add or deny permissions across some or all S3 objects in a bucket, enabling central management of permissions
  * Can grant users within an AWS account or other AWS accounts to S3 resources
  * Can restrict based on request time (Date condition), request sent using SSL (Boolean condition), requester IP Address (Ip address condition) using policy keys
  * User access to S3 => IAM permissions
  * Instance (EC2) access => IAM role
  * Public access to S3 => bucket policy

| Type of Access Control | Account Level Control | User Level Control |
| ------------- | ------------- | ------------- |
| IAM Policies | No | Yes |
| ACLs | Yes | No |
| Bucket Policies | Yes | Yes |

### IAM Credentials Report: IAM security tool that lists all your AWS accounts, IAM users and the status of their various credentials; good for auditing permissions at the **account level**

### IAM Access Advisor: shows the service permissions granted to a user and when those services were last used; can use this information to revise policies at the **user level**

### AWS Policy Simulator: used to test and troubleshoot IAM policies that are attached to users, user groups, or resources.  

###  Identity and Access Management Access Analyzer:

### Identity and Access Management Policy Evaulation Logic:
![IAM Policy Evaulation Logic](https://docs.aws.amazon.com/images/IAM/latest/UserGuide/images/PolicyEvaluationHorizontal111621.png)

### Amazon Cognito:
  * Web Identity federation service/identity broker handling interations between application(s)/resource(s) and Web IdPs.
  * Capable of synchronizing data from multiple devices by means of SNS to send notifications to all devices associated to a given user upon data deltas (IAM policy can be tethered to user ids possibly).
  * User pool: user based; handling user registration, authentication and account recovery.
    * Compatible IdPs: Facebook, Amazon, Google, Apple, OpenID Connect providers, SAML
  * Identity pool: receives authentication token to authorize access to resources directly or through the API GW.
    * Maps to IAM role(s)
    * default IAM role(s) for authenticated/guest users

### AWS Resource Access Manager (RAM):
  * Share AWS resources that you own with other AWS accounts (within OU or any account)
  * Aids in avoiding resource duplication by sharing thing such as:
    * VPC subnets (owner can share +1 subnets with other accounts in the same OU):
      * Allows all resources (EC2, etc.) launched in the same VPC
      * Must be from the same OU
      * Can't share SGs and default VPC
      * Users can manage own resources, but can't modify, view, or delete other's resources
      * VPC by itself can't be shared
    * AWS Transit Gateway
    * Route 53 Resolver Rules
    * Licence Manager Configurations accross accounts using Private IP(s)

## Organizational Unit (OU)

### AWS Account Organizational Unit Migration:
  * Remove the member account from the former organization [need root or IAM access to said member account and master account(s)]
  * Send invite to the member account from the prospective organization
  * Accept the invite from the prospective organization upon the member account
  * Ensure the OrganizationAccountAccessRole is added to the member account

### AWS Control Tower:
  * Easy way to setup and govern a secure and compliant multi-account AWS environment based on best practices and using AWS OU to create accounts
  * automates setup of environments in a few clicks
  * automates ongoing policy managment using guard rails:
    * SCP: preventative
    * AWS Config: detective
  * detects policy violations and remediates them
  * monitor compliance through dashboards

### Service Control Policies (SCP):
  * Policies for OUs to manage permissions within, helping accounts stay within control setting limits/guard rails
  * No permissions are granted by SCP, still IAM, but the effective permissions are the intersection of IAM, SCP, and IAM permissions boundaries allowing access
  * OU must have all features enabled to utilize.
  * Affects member accounts and attached users and roles within including the root user(s), not management accounts.
  * Doesn't affect resource based policies directly.
  * Doesn't affect service linked roles, which enable other AWS services to integrate with AWS OUs.
  * If disabled at the root account, all SCPs are automatically detached from OU under that root account.  If re-enabled all accounts there under are reverted to full AWS Access (default)
  * Must have an explicit allow (nothing allowed by default like IAM) which is similar to IAM permissions boundary (if not in boundary => deny)

## EC2

### EC2 On-Demand

### EC2 Savings Plan Instance

### EC2 Reserved Instance

### EC2 Convertible Reserved Instance

### EC2 Capacity Reserved Intance

### EC2 Dedicated Instance

### EC2 Dedicated Host Instance

### EC2 Capacity Reserved Intance

### EC2 Spot Instance

### EC2 Spot Fleets

### EC2 Spot Blocks (aka Spot Duration)

### EC2 SG configurations:
  * source (inbound rules) or destination (outbound rules) for network traffic specified via the following options:
    * Single ipv4 (/32 CIDR) or ipv6 (/128 CIDR)
    * Range of ipv4/ipv6 address (CIDR block notation)
    * Prefix List ID for the AWS service(s) via Gatway Endpoints, (eg: p1-1a2b3c4d)
    * Another SG, allowing instances within one SG to access instances within another.  Choosing this option doesn't add rules from the source SG, to the 'linked' SG.  You can specify one of the following:
      * Current SG
      * Different SG for the the same VPC
      * Different SG for a peer VPC in VPC peering connection

## Containers

## Logging and Events

## Configurations and Security

### AWS Certificate Manager (ACM):
  * Service to provision , manage, import, and deploy public and private SSL/TLS certificates for use with AWS services and internally connected resources
  * Removes time consuming manual process of purchasing, uploading, and renewing SSL/TLS certificates
  * Manages certificate lifecyle centrally
  * Private Certificates use is charged a monthly fee
  * Automatically renews certificates issued by ACM

### Systems Manager (SSM) Parameter Store:
  * Can be used to store secrets
  * Each time you edit the value of a parameter a new version is created via built-in version tracking and previous version committed historically
  * Optional seamless encryption with KMS
  * Serverless, scalable, durable, easy SDK
  * Security through IAM
  * Notifications via Eventbridge
  * Can assign TTL to a parameter to force update(s) or deletion (via Policies)

|  | Standard | Advanced |
| ------------- | ------------- | ------------- |
| Total # of parameters per AWS Account-Region | 10,000 | 100,000 |
| Max Size of Parmater | 4 KB | 8 KB |
| Parameter Policies | No | Yes |
| Cost | No | Charges Apply |
| Storage Pricing | Free | .05 per advanced parameter per month |


### AWS Secrets Manager:
  * Helps manage, retrieve and rotate DB credentials, API keys, and other secrets throughout lifecycles
  * Automatic generation of secrets on rotation (uses a λ)
  * Encrypted at rest possible (via KMS)
  * Integrates with RDS
  * Auditable via CloudTrail, Cloudwatch, and SNS
  * Multi-Region Secrets - read replicas in sync with Primary Secret
  * Able to promote read replica secret to standalone secret (eg: multi-region application DB distaster recovery)
  * SSM parameter store can be utilized if wanting to track versions/values of secrets

### Security Groups (SGs):
  * Stateful connection, allowing inbound traffic to the necessary ports, thus enabling the connection
  * If adding an Internet Gateway, ensure the SG allows traffic in
  * SG => EC2 instances level, LBs, EFS, DBs, Elasticache
  * Allow rules only

### NACL Groups:
  * Stateless, thus a source port inbound will become the outbound port (or possibly taking the defined port and responding via an ephemeral port)
  * Great way of allowing/blocking ip addresses at the subnet level
  * Like a firewall controlling to/from subnet traffic
  * One NACL per subnet
  * New Subnet automatically set to default NACL which denies all inbound/outbound traffic
  * Do not modify default NACL, instead create custom NACL(s)
  * If accepting internet traffic routed via internet gateway
  * If accepting vpn or AWS Direct Connect traffic routed via Virtual Private Gateway
  * NACL rules:
    * Range from 1-32766, with a higher precedence placed on lower numbers
    * Allow and Deny rules
    * First rule match drives acceptance/denial
    * Last rule match is a catch all (\*) and denies a request in case no rules match
    * AWS recommends adding rules by an increment of 100

## VPC

### Launch Configuration (tenancy vs VPC tenancy):
  * Part of the instance config launching instances distributed across physical hardware
  * Tenancy (instance placement) default is null and the instance tenancy is controlled by the tenancy attribute of the VPC
  * Shared (default): multiple AWS accounts may share the same physical hardware
  * Dedicated Instance (dedicated): single tenant hardware used
  * Dedicated Host (host): instances run on a physical server with EC2 instance fully dedicated to a given client's use (not available for launch template configuration)

| Launch Configuration Tenancy | VPC tenancy=default | VPC tenancy=dedicated |
| ------------- | ------------- | ------------- |
| null | shared | dedicated |
| default | shared | dedicated |
| dedicated | dedicated | dedicated |

## Storage

### AWS FSx

### AWS Storage Gateway:
   * Hybrid cloud storage service that provides on-prem access to virtual cloud storage
   * Most recently used date cached in gateway
   * Volume backend up by EBS snapshots
   * Tape backed up by S3, S3 (Glacier), and other software
   * S3 access supported: STD, STD IA, One Zone IA, Intelligent tiering
   * SMB/NTFS access able to integrate with Windows AD
   * Conducted and connected with AWS by: VM or physical hardware device

| Gateway | Protocol(s) |
| ------------- | ------------- |
| S3 (File) | NFS/SMB |
| FSx | SMB/NTFS |
| Tape (interface) | iSCSI |
| Volume (interface) | iSCSI |

### S3

#### S3 Batch Replication:
  * Provides a way to replicate objects that existed before a replication configuration was in place, objects that have previously been replicated, and those that failed replication
  * Differs from live replication
  * Can't use AWS S3 console to configure cross-region replication for existing objects.
  * By default, replication only supports copying new S3 objects after enabled by AWS S3 console
  * To enable live replication of existing objects, an AWS support ticket is needed to ensure replication is configured correctly
  * Can be used to copy large amounts of S3 data between regions or within regions

#### S3 Sync Command:
  * Uses copy object APIs to copy objects between S3 buckets
  * Lists source and target buckets to identify objects found in the former and not the latter as well as last modified dates that are different.
  * On a versioned bucket copies only the current version (previous copies are not copied)
  * If operation fails, can run the command again without duplicating previously copied objects
  * Can be used to copy large amounts of S3 data between regions

#### Origin Access Control (OAC):
  * Restricts access to S3 preventing public availablity, ensures access through intended Cloudfront distribution(s) [no direct access]
  * Is Replacing OAI
  * Supports all S3 buckets in all regions
  * Supports AWS KMS (SSE-KMS)
  * Dynamic requests (PUT, POST, etc.) to S3
  * Can be used to only allow authenticated access (via Cloudfront configuration), not done via OAC directly
  
#### Origin Access Identity (OAI):
  * Restricts access to S3 preventing public availablity, ensures access through intended Cloudfront distribution(s) [no direct access]
  * Is being replaced by OAC
  * Can be used to only allow authenticated access (via Cloudfront configuration), not done via OAI directly

## Database

### Relational

### NoSQL

#### Amazon DocumentDB:
  * Effectively the AWS "Aurora" version of MongoDB
  * Used to store query and index JSON data
  * Similar "deployment concepts" as Aurora
  * Fully managed, highly available with replication across 3 AZs
  * Storage automatically grows in increments of 10 GB, up to 64 TB
  * Automatically scales to workloads with millions of requests per secon
  * Anything related to MongoDB => DocumentDB
  * Doesn't have an in-memory caching layer => consider DynamoDB (DAX) for a NoSQL approach

#### DynamoDB


## Analytics

### Amazon Kinesis

### Amazon Kinesis Data Streams

### Amazon Kinesis Data Firehose

### Amazon Kinesis Analytics

### Amazon Managed Streaming for Apache Kafka (MSK)

### Amazon Sagemaker:
  * Fully managed service for development/data science to build ML models
  * Typically, difficult to do all the process in one place and provision server(s)
    * label data
    * train and tunde model(s)
    * serve api traffic against the model(s)

## Miscellaneous

### AWS Wavelength: extend vpc and it's resources via desired subnets to include a wavelength zone, embedding within 5G networks providing ultra low latency

### AWS Data Exchange: service in AWS for customers to find, subscribe to, and use third-party data in the AWS Cloud

### AWS Elastic Transcoder: highly scalable/cost efficient service to convert (or 'transcode') media files (video/audio) to format(s) that will be playable on devices, tablets, desktops, etc. The transacation takes place between a source and destination S3 bucket.  

### Amazon Personalize:
  * Fully managed ML service to build realtime personalized recommendations applications
  * Increments in days, not months (no need to train, build ML models)
  * Service ingests via S3 (read data) and/or Amazon Personalize API (realtime data integration)

### Amazon Comprehend (Medical):
  * Serverless NLP service harnessing ML to uncover valuable insights and connections in text
  * Medical version detects PHI via DetectPHI API

### Amazon Transcribe:
  * Automatically convert speech to text
  * Uses Deep Learning - Automatic Speech Recognition (ASR)
  * Use cases:
    * Transcribe customer calls
    * Automate close capitioning/subtitles
    * Generate metadata for media assets to create full scaleable architecture
  * Can remove PII using redaction
  * Supports automatic language identification for multi-lingual audio

## Acronyms

| Acronym  | Definition |
| ------------- | ------------- |
| ACL | Access Control List |
| ACM | AWS Certificate Manager |
| AD | Active Directory |
| ADFS | Active Directory Federation Services |
| API | Application Programming Interface |
| ARN | Amazon Resource Name |
| AWS | Amazon Web Services |
| AZ | Availability Zones |
| DB | Database |
| EFS | Elastic File System |
| GW | Gateway |
| IAM |  Identity and Access Management |
| IdP | Identity Provider |
| LB | Load Balancer |
| LDAP | Lightweight Directory Access Protocol |
| KMS | Key Management Service |
| ML | Machine Learning |
| MSK | Managed Streaming Kafka |
| NLP | Natural Language Processing |
| OAC | Origin Access Control |
| OAI | Origin Access Identity |
| OU | Organizational Unit |
| PHI | Protected Health Information |
| PII | Personally Identifiable Information |
| RDS | Relational Database |
| SAML | Security Assertion Markup Language |
| SG | Security Group |
| SNS | Simple Notification Service |
| SQS | Simple Queue Service |
| SSM | Systems Manager |
| STS | Security Token Service |
| SCP | Service Control Policies  |
| S3 | Simple Storage Service |
| VPC | Virtual Private Cloud |
