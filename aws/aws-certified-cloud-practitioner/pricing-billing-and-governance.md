# Pricing, Billing, and Governance

## AWS Pricing

### Pricing

1. **Compute:** Hourly from launch to termination
2. **Storage:** The data you store in the cloud
3. **Outbound data transfer:** Data in flight moving between systems

### Free Offer Types

1. **12 months free:** 12 months free usage following your initial sign-up date to AWS
2. **Always free**: Offers do not expire and are available to all AWS customers
3. **Trials:** Short-term free trials staring from the date you activate a particular service

### EC2 Pricing

1. **On-Demand:** You pay by the hour or by the second without pre-paying
2. **Saving Plan:** Commit to compute usage measured per hour for 1- or 3-year term
3. **Reserved Instances:** Commit to use for 1 or 3 years; pay regardless of usage
4. **Spot Instances:** Instances only launch if spare capacity is available
5. **Dedicated Hosts:** An entire physical server just for you

### Lambda Pricing

1. **Number of requests:** Include test invokes from the console
2. **Code execution time:** From execution start, in response to events, to stop
3. **Always free:** 1 million requests per month

### S3 Pricing

1. **Storage class:** Various storage classes
2. **Storage:** Number and size of objects
3. **Data transfer:** Data transferred out of S3 Region
4. **Request and data retrieval:** Request made for data and amount of requests

### RDS Pricing

1. Running clock hours
2. Type of database
3. Storage
4. Purchase type
5. Database count
6. API requests
7. Deployment type
8. Data transfer

### Total Cost of Ownership (TCO)

TCO is a financial estimate that helps you underestand both the direct and indirect costs of AWS.

### Application Discovery Service

Application Discovery Service helps you plan migration projects to the AWS Cloud.

* Plan migration projects
* Used to estimate TCO
* Works with other services to migrate servers

### A Few Ways to Reduce Your TCO Using AWS

1. **Minimize capital expenditures:** AWS helps you minimize large capital expenditures, which reduces your TCO.
2. **Utilize Reserved Instances:** AWS provides Reserved Instances to help you lock in saving and reduce your TCO.
3. **Right size your resources:** AWS helps you match the provisioning of resources to your usage needs to reduce your TCO.

### AWS Price List API

The Price List API allows you to query the price of AWS services.

* Query using JSON or HTML
* Receive price alerts when prices change

## Billing Services

### Budgets

Budgets allow you to set a custom budget that alerts you when your costs or usage exceed your budgeted amount.

* Improve planning and cost control
* Cost, usage, and reservation budgets
* Budget alerts

### Budget Types

1. Cost Budgets: Plan how much you want to spend on a service.
2. Usage Budgets: Plan how much you want to use on one or more services.
3. Reservation Budgets: Set RIs or Saving Plans utilization or coverage targets.

#### Budgets in the Real World

* Monitor Free Tier usage so you don’t incur unwanted costs: You can monitor Free Tier usage to ensure you don’t accidentally exceed Free Tier limits and incur unwanted costs. You can set up an alert notification for when your account is approaching a particular dollar amount.

### Cost and Usage Report

The Cost and Usage Report contains the most comprehensive set of cost and usage data.

* A downloadable detailed and comprehensive report
* List usage for each service category
* Aggregate usage data on a daily, hourly, or monthly level

#### Cost and Usage Report in the Real World

* View the most granular data about your AWS bill: The Cost and Usage Report gives you the ability to do a deep dive into your AWS cost and usage data. Once set up, you can download the report using the Amazon S3 console.

### Cost Explorer

Cost Explorer allows you to visualize and forecast your costs and usage over time.

* Visualize costs over time
* View the past 12 months
* Forecast for up to 12 months

#### Cost Explorer in the Real World

* Analyze your EC2 usage over the past 7, 30, or 60 days: If you considering your options for Saving Plans, AWS Cost Explorer can analyze your EC2 usage over the past 7, 30, or 60 days.

### Cost Allocation Tags

Tags are useful for tracking spending.

1. Tags allow you to label resources using a key and value pair.
2. Tags allow you to track costs via the cost allocation report.

## Governance Services

### Organizations

Organizations allow you to centrally manage multiple AWS accounts under one umbrella.

* Group multiple accounts
* Single payment for all accounts
* Automate account creation
* Allocate resources and apply policies across accounts

#### What are the benefits of Organizations?

1. Consolidated Billing: The advantage of consolidated billing is that you receive one bill for multiple accounts.
2. Cost Saving: You’ll receive volume discounts since usage is combined across accounts.
3. Account Governance: You have a quick and automated way to create accounts or invite existing accounts.

#### Organizations in the Real World

* Reduce costs by sharing resources across accounts: Organizations allow you to save money using Reserved Instance (RI) sharing. RI sharing allows all accounts in the organization to receive the hourly cost-benefit of RIs purchased by any other account. You can always turn off RI sharing using the master payer (or root) organization.

### Control Tower

Control Tower helps you ensure your accounts conform to company-wise policies.

* Helps set up new accounts using a multi-account strategy
* Works directly with AWS Organizations
* Enforce the best use of services across accounts
* Provides a dashboard to manage accounts

#### Control Tower in the Real World

* Disallow public write access to all S3 buckets across your accounts: Control Tower allows you to govern your multi-account environment by enabling cross-account security audits or preventing or detecting security issues through mandatory or optional guardrails.

### Systems Manager

Systems Manager gives you visibility and control over your AWS resources.

* Automate operational tasks on your resources
* Group resources and take action
* Patch and run commands on multiple EC2 instances or manage RDS instances

#### Systems Manager in the Real World

* Deploy operating system and software patches automatically across a large group of instances: Systems Manager allows you to auto-patch software running on EC2 instances according to a schedule.

### Trusted Advisor

Trusted Advisor provides real-time guidance to help you provision your resources following AWS best practices.

* Checks your account and makes recommendations
* Helps you see service limits
* Helps you understand best practices

#### A few popular recommendations made by Trusted Advisor

1. Checks for unrestricted access for specific ports on EC2 instances (free)
2. Checks S3 buckets permissions to determine if public access (free)
3. Checks for MFA on root account (free)
4. Checks IAM password policy (non-free, Enterprise or Business Support required)
5. Checks for RDS public snapshots (free)
6. Checks for service usage greater than 80% over service limit (non-free, Enterprise or Business Support required)
7. Checks for exposed access keys (non-free, Enterprise or Business Support required)
8. Checks for CloudFront content delivery optimization (non-free, Enterprise or Business Support required)

#### Trusted Advisor in the Real World

* Check read and write capacity service limits for DynamoDB: Trusted Advisor helps reduce your overall costs by monitoring service limits.

### License Manager

License Manager helps you manage software licenses.

* Manage on-premises and AWS licenses
* Track licenses for Oracle, Microsoft, SAP, and more

### Certificate Manager

Certificate Manager helps you provision and manage SSL/TLS certificates.

* Provides public and private certificates for free
* Integrates with Elastic Load Balancing, API Gateways, and more

## Management Services

### Managed Services

Managed Services helps you efficiently operate your AWS infrastructure.

* Augments your internal staff
* Provides ongoing management of your infrastructure
* Reduces operational risks and overhead

#### Managed Services in the Real World

* Develop application-specific health monitoring using CloudWatch: Managed Services can increase your operational efficiency by helping you develop application-specific health monitoring using CloudWatch.

Professional Services

Professional Services helps enterprise customers move to a cloud-based operating model.

* Proposes solutions
* Architects solutions
* Implements solutions

### Professional Services in the Real World

* Get helps with evaluating an application for migration to the cloud: You can quickly move on-premises applications to the cloud using AWS Professional Services.

#### AWS Partner Network

APN is a global community of approved partners that offer software solutions and consulting services for AWS.

* Offers technology partners that provide software solutions
* Provides consulting partners that offer professional services
* Find approved vendors with deep AWS expertise

#### APN in the Real World

* You need help designing and building a new application: If your team lacks the technical expertise to build and deploy cloud applications, the APN cloud helps you get up and running quickly.

### Marketplace

Marketplace is a digital catalog or prebuilt solutions you can purchase or license. You may also sell your own solutions to others via Marketplace.

* Buy third-party software
* Sell solutions to AWS customers
* Search the catalog of software listings and install them with the click of a button

#### Marketplace in the Real World

* Try out an application before making a long-term commitment: Some products listed on Marketplace offer free trials. The free trial allows you to try the software before you buy it.

### Personal Health Dashboard

Personal Health Dashboard alerts you to events that might impact your AWS environment.

* Provides troubleshooting guidance
* Feedback tailored to your specific environment

### Support Plans&#x20;

There are 4 support plans.

1. **Basic:** Basic support is included for free for all AWS accounts.
2. **Developer:** Developer support starts at $29 a month and is recommended for testing and development.
3. **Business:** Business support starts at $100 a month and is recommended for production workloads.
4. **Enterprise:** Enterprise support starts at $15,000 a month and is recommended for business or mission-critical production workloads.

### Support Case Types

There are 3 types of support cases you can open with AWS Support.

1. **Account and billing:** Account-related and billing cases can be opened by customers.
2. **Service limit increases:** Default service quota (or limit) increases can be opened by all customers.
3. **Technical support:** Technical support cases can only be opened by customers on the Developer, Business, or Enterprise plans.

AWS Support does not allow cases for code development, debugging custom software, or performing system administration tasks.

#### Basic Support Plan

Basic Support is included for free for all AWS accounts.

* Access to account and billing support cases
* Access to service limit increases
* Customer Services: 24/7 access via email only
* Access to discussion forums

#### Developer Support Plan

Developer Support starts at $29 a month and is recommended for testing and development.

* Access to account and billing support cases
* Access to service limit increases
* Access to technical support
* Allowed to 1 primary contact
* Access to open unlimited support cases
* Cloud Support Associate: Business-hours access via email only
* Response Times: <24 hours general guidance and <12 hours system impaired

#### Business Support Plan

Business Support starts at $100 a month and is recommended for production workloads.

* Access to account and billing support cases
* Access to service limit increases
* Access to technical support
* Allowed unlimited contact
* Access to open unlimited support cases
* Access to a Full Set of Trusted Advisor Checks
* Cloud Support Engineers: 24/7 access via email, phone, or chat
* Response Times: <24 hours general guidance, <4 hours production system impaired, <12 system impaired, < 1-hour production system down

#### Enterprise Support Plan

Enterprise Support starts at $15,000 a month and is recommended for business or mission-critical production workloads.

* Access to account and billing support cases
* Access to service limit increases
* Access to technical support
* Allowed unlimited contact
* Access to open unlimited support cases
* Access to Technical Account Manager (TAM)
* Access to Concierge Support Team
* Access to Infrastructure Event Manager
* Access to a Full Set of Trusted Advisor Checks
* Cloud Support Engineers: 24/7 access via email, phone, or chat
* Response Times: <24 hours general guidance, <4 hours production system impaired, <12 system impaired, < 1-hour production system down, <15 minutes business-critical system down
