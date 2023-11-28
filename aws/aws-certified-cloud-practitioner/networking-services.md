# Networking Services

{% hint style="success" %}
**AWS Overview Links**

* [VPC](https://aws.amazon.com/vpc/)
* [Route 53](https://aws.amazon.com/route53/)
* [Direct Connect](https://aws.amazon.com/directconnect/)
* [VPN](https://aws.amazon.com/vpn/)
* [API Gateway](https://aws.amazon.com/api-gateway/)
{% endhint %}

Networking connects computers together and allows for the sharing of data and applications, around the globe, in a secure manner using virtual routers, firewalls, and network management services.

## Amazon Virtual Private Cloud (VPC)

VPC is a foundational service that allows you to create a secure private network in the AWS cloud where you launch your resources.

* Private virtual network
* Launch resources like EC2 instances inside the VPC
* Isolate and protect resources
* A VPC spans AZs in a Region

VPS peering allows you to connect 2 VPCs together.

Peering facilitates the transfer of data in a secure manner.

## Additional Network Services

* **What is DNS?** DNS stands for Domain Name System and directs internet traffic by connecting domain names with web servers.

### Amazon Route 53

Route 53 is a DNS service that routes users to applications.

* Domain name registration
* Performs health checks on AWS resources
* Supports hybrid cloud architectures

### AWS Direct Connect

Direct Connect is a dedicated physical network connection from your on-premises data center to AWS.

* Dedicated physical network connection
* Connects your on-premises data center to AWS
* Data travels over a private network
* Supports a hybrid environment

#### Direct Connect in the real world

1. **Large datasets:** Transfer large datasets to AWS
2. **Business-critical data:** Transfer internal data directly to AWS bypassing your internet service provider
3. **Hybrid model:** Build hybrid environments

### AWS VPN

Site-to-Site VPN creates a secure connection between your internal networks and your AWS VPCs.

* Similar to Direct Connect, but data travels over the public internet
* Data is automatically encrypted
* Connects your on-premises data center to AWS
* Supports a hybrid environment

### API Gateway

API Gateway allows you to build and manage APIs.

* Share data between systems
* Integrate with services like Lambda

