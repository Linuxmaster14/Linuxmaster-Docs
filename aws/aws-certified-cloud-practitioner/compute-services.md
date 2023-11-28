# Compute Services

{% hint style="success" %}
**AWS Overview Links**

* [EC2](https://aws.amazon.com/ec2/)
* [Lambda](https://aws.amazon.com/lambda/)
* [Fargate](https://aws.amazon.com/fargate/)
* [Lightsail](https://aws.amazon.com/lightsail/)
* [Outposts](https://aws.amazon.com/outposts/)
* [Batch](https://aws.amazon.com/batch/)
{% endhint %}

## EC2

EC2 (Elastic Compute Cloud) allows you to rent and merge virtual servers in the cloud.

EC2 is a foundational service used for managing your virtual instances.

1. You can provision an EC2 instance at the click of a button.
2. You can use a preconfigured template called an Amazon Machine Image (AMI) to launch your instance.
3. You can deploy your applications directly to EC2 instances.
4. You receive 750 compute hours per month on the [Free Tier](https://aws.amazon.com/free/) plan.

### Methods to access an EC2 instance

There are several ways to access your EC2 instances.

* AWS Management Console: You can configure and manage your instances via a web browser.
* Secure Shell (SSH): SSH lets you establish a secure connection to your instance from your local laptop.
* EC2 Instance Connect (EIC): EIC allows you to use IAM policies to control SSH access to your instances, removing the need to manage SSH keys.
* AWS Systems Manager: Systems Manager allows you to manage your EC2 instances via a web browser or the AWS CLI.

### EC2 Pricing Options

There are several pricing options to choose from for your EC2 instances.

#### 1. On-Demand

A fixed price in which you are billed down to the second based on the instance type. There is no contract, and you pay only for what you use.

**Use On-Demand instances when:**

* You care about low cost without any upfront payment or long-term commitment.
* Your applications have unpredictable workloads that can’t be interrupted.
* Your applications are under development.
* Your workloads will not run longer than a year.

You can reserve capacity using On-Demand Capacity Reservations.

The EC2 capacity is held for you whether or not you run the instance.

#### 2. Spot

Spot instances let you take advantage of unused EC2 capacity. Your request is fulfilled only if capacity is available.

**Use Spot instances when:**

* You are not concerned about the start or stop time of your application.
* Your workload can be interrupted.
* Your application is only feasible at very low compute prices.

You can save up to **90%** off On-Demand prices.

You pay the spot price that’s in effect at the beginning of each hour.

#### 3. Reserved Instances (RIs)

RIs allow you to commit to a specific instance type in a particular Region for 1 or 3 hours.

**Use Reserved Instances when:**

* Your application has steady-state usage, and you can commit to 1 or 3 years.
* You can pay monthly upfront in order to receive a discount on On-Demand prices.
* Your application requires a capacity reservation.

You can save up to **75%** off On-Demand prices.

You are required to sign a contract.

You can reserve capacity in an Availability Zone for any duration.

You can pay All Upfront, Partial Upfront, or No Upfront.

All Upfront for the max term earns the highest discount.

Provides convertible types at a **54%** discount.

#### 4. Dedicated Hosts

Dedicated Hosts allow you to pay for a physical server that is fully dedicated to running your instances.

**Use Dedicated Hosts when:**

* You want to bring your own server-bound software license from vendors like Microsoft or Oracle.
* You have regulatory or corporate compliance requirements around the tenancy model.

You can save up **70%** off On-Demand prices.

You bring your existing per-socket, per-core, or per-VM software licenses.

There is no multi-tenancy, meaning the server is not shared with other customers.

A Dedicated Host is a physical server, whereas a Dedicated Instance runs on the host.

#### 5. Saving Plan

Saving plan allows you to commit to compute usage (measured per hour)

**Use Saving Plans when:**

* You want to lower your bill across multiple compute services.
* You want the flexibility to change compute services, instance types, operating systems, or Regions.

You can save up to 72% off On-Demand prices.

You are not making a commitment to a Dedicated Host, just compute usage.

Saving can be shared across various compute services like EC2, Fargate, and Lambda.

This does not provide a capacity reservation.

### Features

EC2 instances offer load balancing and Auto Scaling.

#### Elastic Load Balancing

Automatically distributes your **incoming application traffic** across multiple EC2 instances.

#### EC2 Auto Scaling

Adds or replaces EC2 instances automatically across AZs, **based on need and changing demand**.

## Lambda

Lambda is a serverless computing service that lets you run code without managing servers.

Lambda allows developers to focus on core business logic for the apps they are developing instead of worrying about managing servers.

### Features

1. Supports popular programming languages like Java, Go, Powershell, Node.js, C#, Python, and Ruby.
2. You another code using your favorite development environment or via the console.
3. Lambda can execute your code in response to events.
4. Lambda functions have a 15-minute timeout.

### Pricing Model

You are charged based on the duration and number of requests.

1. **Compute time:** Pay only for compute time used. There is no charge if your code is not running.
2. **Request count:** A request is counted each time it starts execution. Test invokes in the console count as well.
3. **Always free:** The free usage tier includes 1 million free requests each month.

## Additional Compute Services

### AWS Fargate

Fargate is a serverless computing engine for containers.

* Fargate allows you to manage containers, like Docker.
* Scanles automatically
* Serverless means you don’t worry about provisioning, configuring, or scaling servers.

### Amazon Lightsail

Lightsail allows you to quickly launch all the resources you need for small projects.

* Deploy pre-configured applications, like Wordpress websites, at the click of a button
* Simple screens for people with no cloud experience
* Includes a virtual machine, SSD-bashed storage, data transfer, DNS Management, and a static IP
* Provides a low, predictable monthly fee, as low as $3.50

### AWS Outposts

Outposts allow you to run cloud services in your internal data center.

* Supports workloads that need to remain on-premises due to latency or data sovereignty needs
* AWS delivers and installs servers in your internal datacenter
* Use for a hybrid experience
* Have access to the cloud services and APIs to develop apps on-premises

### AWS Batch

Batch allows you to process large workloads in smaller chunks (or batches).

* Runs hundreds and thousands of smaller batch processing jobs
* Dynamically provisions instances based on volume

