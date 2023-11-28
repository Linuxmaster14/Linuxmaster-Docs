# Deployment and Infrastructure Management Service

{% hint style="success" %}
**AWS Overview Links**

* [CloudFormation](https://aws.amazon.com/cloudformation/)
* [Elastic Beanstalk](https://aws.amazon.com/elasticbeanstalk/)
* [OpsWorks](https://aws.amazon.com/opsworks/)
{% endhint %}

These services help you quickly stand up new applications, automate the management of infrastructure, and provide real-time visibility into system health.

### IAC (Infrastructure as Code)

IaC allows you to write a script to provision AWS resources. The benefit is that you provision resources in a reproducible manner that saves time.

## CloudFormation

CloudFormation allows you to provision AWS resources using Infrastructure as Code (IaC).

* Provides a repeatable process for provisioning resources.
* Works with most AWS services
* Create templates for the resources you want to provision

### CloudFormation in the real world

Automate the infrastructure-provisioning process for EC2 servers.

You can use CloudFormation to automate the creation of EC2 instances in your AWS account.

## Elastic Beanstalk

Elastic Beanstalk allows you to deploy your web applications and web services to AWS.

* Orchestration service that provisions resources
* Automatically handles the deployment
* Monitors application health via a health dashboard

### Elastic Beanstalk in the real world

Quickly deploy a scalable Java-based web application to AWS.

After you upload your Java code, Elastic Beanstalk deploys it and handles capacity provisioning, load balancing, and Auto Scaling. Elastic Beanstalk even monitors the health of your application.

## OpsWorks

OpsWorks allows you to use Chef or Puppet to automate the configuration of your servers and deploy code.

* Deploy code and manage applications
* Manage on-premises servers or EC2 instances in AWS Cloud
* Works with Chef and Puppet automation platforms

### OpsWork in the real world

Automate software configuration and infrastructure management for your application.

OpsWorks allows you to define software installation scripts and automate configuration for your application servers.
