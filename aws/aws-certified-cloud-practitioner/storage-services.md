# Storage Services

{% hint style="success" %}
**AWS Overview Links**

* [S3](https://aws.amazon.com/s3/)
* [EBS](https://aws.amazon.com/ebs/)
* [EFS](https://aws.amazon.com/efs/)
* [Storage Gateway](https://aws.amazon.com/storagegateway/)
* [Backup](https://aws.amazon.com/backup/)
{% endhint %}

Companies today need to collect, store, and analyze the data they’ve accumulated over the years on a massive scale. Storage services in the cloud provide a place for companies to store data.

## Amazon Simple Storage Services (Amazon S3)

S3 is an object storage service for the cloud that is highly available.

* Objects (or files) are stored in buckets (or directories).
* Essentially unlimited storage that can hold millions of objects per bucket.
* Objects can upload objects via the console, the CLI, or programmatically from within code using SKDs.

You can set security at the bucket level or individual object level using access control lists (ACLs), bucket policies, or access point policies.

You can enable versioning to create multiple versions of your file in order to protect against accidental deletion and to use a previous version.

You can use S3 access logs to track access to your buckets and objects.

S3 is a regional service, but bucket names must be globally unique.

### Data Accessibility

Durability and availability are 2 very different aspects of data accessibility.

* **Durability:** Durability is important so your objects are never lost or compromised. (Amazon S3 Standard is designed for 99.999999999% of durability.
* **Availability:** Availability is important so you cam access your data quickly when you need it. (Amazon S3 Standard is designed for 99.99% availability.)

### S3 Storage Classes

Amazon S3 offers several storage classes designed for different use cases.

#### 1. S3 Standard

* General-purpose storage
* Data stored across multiple Available Zones
* Low Latency and high throughput
* Recommended for “Frequently accessed data”
* Durability: 99.999999999%
* Availability: 99.99%

#### 2. S3 Intelligent-Tiering

* Automatically moves your data to the most cost-effective storage class
* Automatic cost saving
* No retrieval fees
* Data stored across multiple Availability Zones
* Recommended for “Data with unknown or changing access pattern”
* Durability: 99.999999999%
* Availability: 99.9%

#### 3. S3 Standard-Infrequent Access (IA)

* Data is accessed less frequently but requires rapid access
* Data stored across multiple Availablity Zones
* Cheaper than S3 Standard
* Recommended for “Long-lived data, Infrequently accessed, Milisecond access when needed”
* Durability: 99.999999999%
* Availability: 99.9%

#### 4. S3 One Zone-Infrequent Access (IA)

* Like S3 Standard-IA but data stored in a single AZ
* Costs 20% less than S3 Standard-IA
* Data stored in this storage class can be lost
* Recommended for “Re-creatable data, Infrequently accessed with millisecond access, Availability, and durability no essential”
* Durability: 99.999999999%
* Availability: 99.95%

#### 5. S3 Glacier

* Long-term data storage and archival for lower costs
* Data retrieval takes longer
* 3 retrieval options: 1-5 minutes, 3-5 hours, or 5-12 hours
* Data stored across multiple AZ
* Recommended for “Long-term backups, Cheaper storage options”
* Durability: 99.999999999%

#### 6. S3 Glacier Deep Archive

* Like S3 Glacier but longer access time
* 2 retrieval options: 12 hours or 48 hours
* Cheapest of all S3 options
* Data stored across multiple AZ
* Recommended for “Long-term data archival accessed once or twice a year, Retaining data for regulatory compliance requirements”
* Durability: 99.999999999%

#### 7. S3 Outposts

* Provides object storage on-premises
* A single storage class
* Store data across multiple devices and servers
* Recommended for “Data that need to be kept local, Demanding application performance needs”

### S3 in the Real World

1. **Static Websites:** Deploy static websites to S3 and use CloudFront for global distribution.
2. **Data archive:** Archive data using Amazon Glacier as a storage option for Amazon S3.
3. **Analytics systems:** Store data in Amazon S3 for use with analytics services like Redshift and Athena.
4. **Mobile applications:** Mobile applications users can upload files to Amazon S3 buckets.

## Additional Storage Services

### Amazon Elastic Block Store (EBS)

EBS is storage device (called a volume) that can be attache to (or removed from) your instance.

* Data persists when the instance is not running.
* Can only be attached to one instance in the same AZ
* Tied to one AZ
* Recommended for “Quickly accessible data, Running a database on a instance, Long-term data storage”

### EC2 Instance Store

An instance store is local storage that is physically attached to the host computer and cannot be removed.

* Storage on disks physically attached to an instance
* Storage is temporary since data loss occurs when the EC2 instance is stopped
* Faster with higher I/O speeds
* Recommended for “Temporary storage needs, Data replicated across multiple instances”

### Amazon Elastic File System (EFS)

EFS is a serverless network file system for sharing files.

* Only supports the Linux file system
* Accessible across different AZ in the same Region
* More expensive than EBS
* Recommended for “Main directories for business-critical apps, Lift-and-shift existing enterprise apps”

### Storage Gateway

Storage Gateway is a hybrid storage service.

* Connect on-premises and cloud data
* Support a hybrid model
* Recommended for “Moving backups to the cloud, Reducing costs for hybrid cloud storage, Low latency access to data”

### AWS Backup

AWS Backup helps you manage data backups across multiple AWS services.

* Integrates with resources like EC2, EBS, EFS, and more
* Create a backup plan that includes frequency and retention
