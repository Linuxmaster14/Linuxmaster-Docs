# Foundations of Cloud Computing

## Understanding Cloud Computing

### What is cloud computing?

Cloud computing is the delivery of computing services over the internet.

* **Compute:** EC2, Lamba
* **Networking:** VPC, Direct Connect
* **Storage:** S3, EBS
* **Analytics:** Athena, Redshift
* **Development:** Cloud9, CodeCommi
* **Security:** IAM, Macie
* **Database:** RDS, DynamoDB

### Virtual Machines

Virtualization lets you divide hardware resources on a single physical server into smaller units.

### Usage

Your pay only when you access it and only for what you use.

* **On-Demand:** No long-term commitments or upfront payments.
* **Pay as You Go:** Pay by the hour or the second for only what you use.

## Exploring the Advantages of Cloud Computing

### Six advantages of cloud computing

* **Trade fixed expense for variable expense:** Instead of having to invest heavily in data centers and servers before you know how you’re going to use them, you can pay only when you consume computing resources, and pay only for how much you consume.
* **Benefit from massive economies of scale:** By using cloud computing, you can achieve a lower variable cost than you can get on your own. Because usage from hundreds of thousands of customers is aggregated in the cloud, providers such as AWS can achieve higher economies of scale, which translates into lower pay-as-you-go prices.
* **Stop guessing capacity:** Eliminate guessing on your infrastructure capacity needs. When you make a capacity decision prior to deploying an application, you often end up either sitting on expensive idle resources or dealing with limited capacity. With cloud computing, these problems go away. You can access as much or as little capacity as you need, and scale up and down as required with only a few minutes’ notice.
* **Increase speed and agility:** In a cloud computing environment, new IT resources are only a click away, which means that you reduce the time to make those resources available to your developers from weeks to just minutes. This results in a dramatic increase in agility for the organization since the cost and time it takes to experiment and develop is significantly lower.
* **Stop spending money running and maintaining data centers:** Focus on projects that differentiate your business, not the infrastructure. Cloud computing lets you focus on your own customers, rather than on the heavy lifting of racking, stacking, and powering servers.
* **Go global in minutes:** Easily deploy your application in multiple regions around the world with just a few clicks. This means you can provide lower latency and a better experience for your customers at minimal cost.

### Benefits of cloud computing

* **High Availability:** High-available systems are designed to operate continuously without failure for a long time. loss of service by reducing or managing failures.
* **Elasticity:** With elasticity, you don't have to plan ahead of time how much capacity you need. You can provision only what you need, and then grow and shrink based on demand.
* **Agility:** The cloud gives you increased agility. All the services you have access to help you innovate faster, giving your speed to market.
* **Durability:** Durability is all about long-term data protection. This means your data will remain intact without corruption.

### CapEx vs. OpEx

* **Capital expenditures (CapEx):** Capital expenditures are upfront purchases toward fixed assets.
* **Operating Expenses (OpEx):** Operating expenses are funds used to run day-to-day operations.

## Reviewing Cloud Computing and Deployment Models

### Cloud Computing Models

The are 3 common cloud computing models.

**1. Infrastructure as a Service (IaaS)**:

* **Building Blocks:** Fundamental building blocks that can be rented.
* **Web Hosting:** Monthly subscription to have a hosting company serve your website.

**2. Software as s Service (SaaS):**

* **Complete Application:** Using a complete application, on-demand, that someone offers to users.
* **Email Provider:** Your personal email that you access through a web browser is SaaS.

**3. Platform as a Service (PaaS):**

* **Used by Developers:** Develop software using web-based tools without worrying about the underlying infrastructure.
* **Storefront Website:** Tools provided to build a storefront application that runs on another company's server.

### Cloud Deployment Models

There are 3 common cloud deployment models

**1. Private Cloud:**

* Also called "on-premises"
* Exists in your internal data center
* Doesn't offer the advantages of cloud computing

**2. Public Cloud:**

* Offered by AWS
* You aren't responsible for the physical hardware
* Provides all the advantages of cloud computing

**3. Hybrid Cloud:**

* Sample architecture for a hybrid solution
* Highly sensitive data stored locally
* Web application run on AWS infrastructure
* AWS provides tools so they talk to each other

## Leveraging the AWS Global Infrastructure

### Regions

A Region is a **physical location**.

* How does AWS group Regions? AWS logically groups its Regions into geographic locations.

### Availability Zones

Availability Zones (AZs) consist of one or more physically separated data centers, each with redundant power, networking, and connectivity, housed in separate facilities.

### Edge Locations

Edge locations cache content for fast delivery to your users.

* **Latency:** Latency is the time that passes between a user request and the resulting response.

## Exploring Your Amazon Web Services (AWS) Account

### The AWS Management Console

The AWS Management Console allows you to access your AWS account and manage applications running in your account from a web browser.

### AWS Command Line Interface (CLI)

The AWS Command Line Interface (CLI) allows you to access your AWS account through a terminal or command window.
