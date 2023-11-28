# Utilizing Databases

{% hint style="success" %}
**AWS Overview Links**

* [RDS](https://aws.amazon.com/rds/)
* [Aurora](https://aws.amazon.com/rds/aurora/)
* [DynamoDB](https://aws.amazon.com/dynamodb/)
* [DocumentDB](https://aws.amazon.com/documentdb/)
* [ElastiCache](https://aws.amazon.com/elasticache/)
* [Neptune](https://aws.amazon.com/neptune/)
{% endhint %}

### Database Key Takeaways

* Databases allow us to collect, store, retrieve, sort, graph, and manipulate data.
* A database is an organized collection of various forms of data.
* Databases are used by many applications: web, mobile, services, and more.
* Databases are necessary to persist data through runs of an application.
* You access the data stored in a database by querying it.
* Databases are typically controlled by a database management system.

In the AWS ecosystem, there are many different types of databases that support different use cases.

## Amazon Relational Database Service (RDS)

RDS is a service that makes it easy to launch and manage relational databases.

* Support popular database engines. like Amazon Aurora, PostgreSQL, MySQL, MariaDB, Oracle, SQL Server
* Offers high availability and fault tolerance using Multi-AZ deployment option
* AWS manages the database with automatic software patching, automated backups, operating system maintenance, and more.
* Launch read replicas across Regions in order to provide enhanced performance and durabilty.

## Amazon Aurora

Aurora is a relational database compatible with MySQL and PostgreSQL that was created by AWS.

* Supports MySQL and PostgreSQL database engines
* 5x faster than normal MySQL and 3x faster that normal PostgreSQL
* Scales automatically while providing durability and high availability
* Managed by RDS

## Amazon DynamoDB

DynamoDB is a fully managed NoSQL key-value and document database.

* NoSQL key-value database
* Fully managed and serverless
* Non-relational
* Scales automatically to massive workloads with fast performance

## Amazon DocumentDB

DocumentDB is a fully managed document database that supports MongoDB

* Document database
* MongoDB compatible
* Fully managed and serverless
* Non-relational

## Amazon ElastiCache

ElastiCache is a fully managed in-memory datastore compatible with Redis or Memcahced.

* In-memory datastore
* Compatible with Redis or Memcached engines
* Data can be lost
* Offers high performance and low latency

## Amazon Neptune

Neptune is a fully managed graph database that supports highly connected datasets.

* Graph database service
* Supports highly connected datasets like social media networks
* Fully managed and serverless
* Fast and reliable
