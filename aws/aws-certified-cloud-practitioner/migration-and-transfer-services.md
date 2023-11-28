# Migration and Transfer Services

{% hint style="success" %}
**AWS Overview Links**

* [DMS](https://aws.amazon.com/dms/)
* [SMS](https://aws.amazon.com/server-migration-service/)
* [Snow Family](https://aws.amazon.com/snow/)
* [Snowcone](https://aws.amazon.com/snowcone/)
* [Snowball](https://aws.amazon.com/snowball/)
* [Snowmobile](https://aws.amazon.com/snowmobile/)
* [DataSync](https://aws.amazon.com/datasync/)
{% endhint %}

## Database Migration Service (DMS)

DMS helps you migrate databases to or within AWS.

* Migrate on-premises databases to AWS
* Continuous data replication
* Supports homogeneous and heterogeneous migrations
* Virtually no downtime

DMS in the real world

1. **Oracle to Aurora MySQL:** Migrate an on-premises Oracle database to Aurora MySQL
2. **Oracle to Oracle:** Migrate an on-premises Oracle database to Oracle on EC2
3. **RDS Oracle to Aurora MySQL:** Migrate an RDS Oracle database to Aurora MySQL

## Server Migration Service (SMS)

SMS allows you to migrate on-premises servers to AWS.

* Migrates on-premises servers to AWS
* Servers saved as a new Amazon Machine Image (AMI)
* Use AMI to launch servers as EC2 instances

## Snow Family

The Snow Family allows you to transfer large amounts of on-premises data to AWS using a physical device.

### Snowcone

* The smallest member of data transport devices
* 8 terabytes of usable storage
* Offline shipping
* Online with DataSync

### Snowball and Snowball Edge

* Petabyte-scale data transport solution
* Transfer data in and out
* Cheaper than Internet transfer
* Snowball Edge supports EC2 and Lambda

### Snowmobile

* Multi-petabyte or exabyte scale
* Data loaded to S3
* Securely transport

## DataSync

DataSync allows for online data transfer from on-premises to AWS storage services like S3 or EFS.

* Migrates data from on-premises to AWS
* Copy data over Direct Connect or the internet
* Copy data between AWS storage services
* Replicate data cross-Region or cross-account
