# Security and Compliance

## Shared Responsibility Model

In the public cloud, there is shared security responsibility between you and AWS.

* AWS responsibility: Security of the Cloud
* Your responsibility: Security in the Cloud

### Security of the Cloud

AWS is responsible for protecting and securing its infrastructure.

* AWS Global Infrastructure: AWS is responsible for its global infrastructure elements: Regions, edge locations, and Availability Zones.
* Building Security: AWS controls access to its data centers where your data resides.
* Networking Components: AWS maintains networking components: generators, uninterruptible power supply (UPS) systems, computer rooms, air conditioning (CRAC) units, fire suppression systems, and more.
* Software: AWS is responsible for any managed service like RDS, S3, ECS, or Lambda, patching of host operating systems, and data access endpoints.

### Security in the Cloud

You are responsible for implementing the services and managing your application data.

* **Application Data:** You are responsible for managing your application data, which includes encryption options.
* **Security Configuration:** You are responsible for securing your account and API calls, rotating credentials, restricting internet access from your VPCs, and more.
* **Patching:** You are responsible for the guest operating system (OS), which includes updates and security patches.
* **Identity and Access Management:** You are responsible for application security and identity and access management.
* **Network Traffic:** You are responsible for network traffic protection, which includes security group firewall configuration.
* **Installed Software:** You are responsible for your application code, installed software, and more. It would help if you frequently scan for and patch code vulnerabilities.

#### EC2 Shared Responsibility Model

| Your responsibility                     | AWS responsibility                     |
| --------------------------------------- | -------------------------------------- |
| Installed applications                  | EC2 service                            |
| Patching the **guest operating** system | Patching the **host operating** system |
| Security controls                       | Security of the physical server        |

#### Lambda Shared Responsibility Model

| Your responsibility       | AWS responsibility         |
| ------------------------- | -------------------------- |
| Security of the code      | Lambda service             |
| Storage of sensitive data | Upgrading Lambda languages |
| IAM for permissions       | Lambda endpoints           |
|                           | Operaing system            |
|                           | Underlying infrastructure  |
|                           | Software dependencies      |

#### Which security responsibilities are shared?

<table><thead><tr><th width="265.3333333333333"></th><th>Your responsibility</th><th>AWS responsibility</th></tr></thead><tbody><tr><td><strong>Patch Management</strong></td><td>Patching guest OS and applications</td><td>Patching infrastructure</td></tr><tr><td><strong>Configuration Management</strong></td><td>Configuring databases and applications</td><td>Configure infrastructure devices</td></tr><tr><td><strong>Awareness and Training</strong></td><td>Your employees</td><td>AWS employees</td></tr></tbody></table>

## Well-Architected Framework

The AWS Well-Architected Framework is a collection of best practices and guidelines for designing and operating reliable, secure, efficient, and cost-effective systems in the cloud.

### **Six pillars of AWS Well-Architected Framework**

#### **1.** Operational Excellence

This pillar focuses on creating applications that effectively support production workloads.

* Plans for and anticipate failure
* Deploy smaller, reversible changes
* Script operations as code
* Learn from failure and refine

#### 2. Security

This pillar focuses on putting mechanisms in place that help protect your systems and data.

* Automate security tasks
* Encrypt data in transit and at rest
* Assign only the least privileges required
* Track who did what and when
* Ensure security at all application layers

#### 3. Reliability

This pillar focuses on designing systems that work consistently and recover quickly.

* Recover from failure automatically
* Scale horizontally for resilience
* Stop guessing capacity
* Manage change through automation
* Test recovery procedures

#### 4. Performance Efficiency

This pillar focuses on the effective use of computing resources to meet system and business requirements while removing bottlenecks.

* Use serverless architectures first
* Use multi-region deployments
* Delegate tasks to a cloud vendor
* Experiment with virtual resources

#### 5. Cost Optimization

This pillar focuses on delivering optimum and resilient solutions at the least cost to the user.

* Utilize consumption-based pricing
* Implement Cloud Financial Management
* Measure overall efficiency
* Pay only for resources your application requires

#### 6. Sustainability

This pillar focuses on environmental impacts, especially energy consumption and efficiency.

* Understand your impact
* Establish sustainability goals
* Maximize utilization
* Use managed services
* Reduce downstream impact

### 6 Pillars in the Real World

1. Operational Excellence (**CodeCommit**): You can use AWS CodeCommit for version control to enable tracking of code changes and to version-control CloadFormation templates of your infrastructure.
2. Security (**CloudTrail**): You can configure central logging of all actions performed in your account using CloudTrail.
3. Reliability (**RDS**): You can use Multi-AZ deployments for enhanced availability and reliability of RDS databases.
4. Performance Efficiency (**Lambda**): You can use AWS Lambda to run code with zero administration.
5. Cost Optimization (**S3**): You can use S3 Intelligent-Tiering to automatically move your data between access tiers based on usage patterns.
6. Sustainability (**Auto Scaling**): You can use EC2 Auto Scaling to ensure you are maximizing utilization.

## IAM Users

Identify and Access Management (IAM)

IAM allows you to control access to your AWS services and resources.

* Helps you **secure** your cloud resources
* You define **who** has access
* You define **what** they can do
* A free **global** service

### Identities vs. Access

#### **Identities**

**Who** can access your resources

* Root user
* Individual users
* Groups
* Roles

#### Access

**What** resources they can access

* Policies
* AWS managed policies
* Customer managed policies
* Permissions boundaries

### Authentication (Who) vs. Authorization (What)

* **Authentication:** Authentication is where you present your identify (**username**) and provide verification (**password**).
* **Authorization:** Authorization determines which services and resources the authenticated identity has **access** to.

## Users

Users are entities you create in IAM to represent the person or application needing to access your AWS resources.

### ROOT User

The root user is created when you first open your AWS account.

What can only the root user do?

* Close your account
* Change email address
* Modify your support plan

### Individual Users

Individual users are created in IAM and are used for everyday tasks.

What can individual users do?

* Perform administrative tasks
* Access application code
* Launch EC2 instances
* Configure databases

### Applications

You’ll create a user in IAM so you can generate access keys for an application running on-premises that needs access to your cloud resources.

#### Users in the Real World

* Create access keys for an IAM user that needs access to the AWS CLI: The AWS Command Line Interface (CLI) allows you to access resources in your AWS account through a terminal or command window. Access keys are needed when using the CLI and can be generated using IAM.

## Groups

A group is a collection of IAM users that helps you apply common access control to all group members.

* Used to group users that perform similar tasks
* Access permissions apply to all members of the group.
* Access is assigned using policies and roles.

Do not confuse security groups for EC2 with IAM groups. EC2 security groups act as firewalls, while IAM groups are collections of users.

* **Administrators:** Administrators perform administrative tasks such as creating new users.
* **Developers:** Developers use computing and database services to build applications.
* **Analysts:** Analysts run to budget and usage reports.

#### Groups in the Real World

* Apply the same access control to a large set of users: Groups save you time by allowing you to apply the same access permissions to more than one user at once. When a user no longer needs access, they can be removed from the group.

## IAM Permissions

### Roles

Roles define access permissions and are temporarily assumed by an IAM user or service.

* You assume a role to perform a task in a single session.
* Assumed by any user or service that needs it.
* Access is assigned using policies.
* You grant users in one AWS account access to resources in another AWS account.

**Examples:**

* User with “DevOps-Engineer Role”: Create code branch in CodeCommit, List pipelines in CodePipeline
* Lambda with “Lambda-Execution Role”: List contents of S3 bucket, Query DynamoDB

#### Roles in the Real World

* Attach a role to an EC2 instance for access to S3: You can attach a role to an instance that provides privileges (e.g. uploading files to S3) to applications running on the instance. Roles help you avoid sharing long-term credentials like access keys and protect your instances from unauthorized access.

### Policies

You manage permissions for IAM users, groups, and roles by creating a policy document in JSON format and attaching it.

**Sample JSON Policy**

```bash
{
  "Version": …,
  "Statement": [
    {
      "Action": rds.*,
      "Effect": "Allow",
      "Resource": […]
    }
  ]
}
```

#### Policies in the Real World

* You can limit access to an Amazon S3 bucket to specific users: You can add a bucket access policy to an Amazon S3 bucket to grant IAM users access permissions for the bucket and the objects in it.

### IAM Best Practices

There are several recommended best practices for IAM.

1. **Enable MFA for privileged users:** You should enable multi-factor authentication (MFA) for the root user and other administrative users.
2. **Implement strong password policies:** You should require IAM users to change their password after a specified period of time, prevent users from reusing previous passwords, and rotate security credentials regularly.
3. **Create individual users instead of using root:** You shouldn’t use the root user for daily tasks.
4. **Use roles for Amazon EC2 instances:** You should use roles for applications that run on EC2 instances instead of long-term credentials like access keys.

### IAM Credential Report

The IAM credential report lists all users in your account and the status of their various credentials.

* List all users and the status of passwords, access keys, and MFA devices
* Used for auditing and compliance

## Security

### Web Application Firewall (WAF)

WAF helps protect your web applications against common web attacks.

* Protects apps against common attack patterns
* Protects against SQL injection
* Protects against cross-site scripting

#### WAF in the Real World

* Protect your web application from cross-site scripting attacks: You can deploy a web application directly to an EC2 instance and protect it from cross-site scripting attacks using WAF. You can even deploy WAF on CloudFront as part of your CDN solution to block malicious traffic.

### Distributed Denial of Service (DDoS)

A DDoS attack causes a traffic jam on a website or web application in an attempt to cause it to crash.

### Shield

Shield is a managed Distributed Denial of Service (DDoS) protection service.

* Always-on detection
* Shield Standard is free
* Shield Advanced is a paid service

1. **Shield Standard:** Provides free protection against common and frequently occurring attacks
2. **Shield Advanced:** Provides enhanced protections and 24/7 access to AWS experts for a fee

DDoS protection via **Shield Advanced** is supported on several services.

1. CloudFront
2. Route 53
3. Elastic Load Balancing
4. AWS Global Accelerator

#### Shield in the Real World

* Receive real-time notifications of suspected DDoS incidents and assistance from AWS during the attack: Shield Advanced will give you notifications of DDoS attacks via CloudWatch metrics. Additionally, with Shield Advanced, you have 24/7 access to AWS experts to assist during an attack.

### Macie

Macie helps you discover and protect sensitive data.

* Use machine learning
* Evaluates S3 environment
* Uncovers personally identifiable information (PII)

#### Macie in the Real World

* Discover passport numbers stored on S3: Macie can be used to find sensitive data like passport numbers, social security numbers, and credit card numbers on S3.

### Config

Config allows you to assess, audit, and evaluate the configurations of your resources.

* Track configuration changes over time
* Delivers configuration history file to S3
* Notifications via Simple Notification Service (SNS) of every configuration change

#### Config in the Real World

* Identify system-level configuration changes made to your EC2 instances: Config allows you to record configuration changes within your EC2 instances. You can view network, software, and operating system (OS) configuration changes, system-level updates, and more.

### GuardDuty

GuardDuty is an intelligent threat detection system that uncovers unauthorized behavior.

* Use machine learning
* Built-in detection for EC2, S3, and IAM
* Reviews CloudTrail, VPC, Flow Logs, and DNS logs

#### GuardDuty in the Real World

* Detect unusual API calls in your account: GuardDuty’s anomaly detection feature evaluates all API requests in your account and identifies events that are associated with common techniques used by attackers.

### Inspector

Inspector works with EC2 instances to uncover and report vulnerabilities,

* Agent installed on EC2 instance
* Reports vulnerabilities found
* Checks access from the internet, remote root login, vulnerable software version, etc.

#### Inspector in the Real World

* Identify unintended network access to an EC2 instance via a detailed report of security findings: Inspector has several built-in rules to access your EC2 instances to find vulnerabilities and report them prioritized by level of security.

### Artifact

Artifact offers on-demand access to AWS security and compliance reports.

* Central repository for compliance reports from third-party auditors
* Service Organization Controls (SOC) reports
* Payment Card Industry (PCI) reports

#### Artifact in the Real World

* You need to access AWS certification for ISO compliance: Artifact provides a central repository for AWS security and compliance repots via a self-service portal.

### Cognito

Cognito helps you control access to mobile and web applications.

* Provides authentication and authorization
* Helps you manage users
* Assists with user sign-up and sign-in

#### Cognito in the Real World

* You need to add a social media sign-in to your web application: Cognito provides functionality that allows your users to sign in to your application through social media accounts like Facebook and Google.

## Encryption and Secret Management

Understand the difference between data in flight vs. data at rest

* **Data in Flight:** Data that is moving from one location to another
* **Data at Rest:** Data that is inactive or stored for later use

### Key Management Service (KMS)

KMS allows you to generate and store encryption keys.

* Key generator
* Store and control keys
* AWS manages encryption keys
* Automatically enabled for certain services

#### &#x20;KMS in the Real World

* Create encrypted Amazon EBS volumes: When you create an encrypted Amazon EBS volume, you’re able to specify a KMS customer master key.

### CloudHSM

CloudHSM is a hardware security module (HSM) used to generate encryption keys.

* Dedicated hardware for security
* Generate and manage your own encryption keys
* AWS does not have access to your keys

#### CloudHSM in the Real World

* Meet compliance requirements for data security by using dedicated hardware: CloudHSM allows you to meet corporate, contractual, and regulatory compliance requirements for data security by using dedicated hardware in the cloud.

### Secret Manager

Secret Manager allows you to manage and retrieve secrets (passwords or keys).

* Rotate, manage, and retrieve secrets
* Encrypt secrets at rest
* Integrates with services like RDS, Redshift, and DocumentDB

#### Secret Manager in the Real World

* Retrieve database credentials needed for your application code: Secret Manager allows you to retrieve database credentials with a call to Secrets Manager APIs, removing the need to hardcode sensitive information in plain text within your application code.
