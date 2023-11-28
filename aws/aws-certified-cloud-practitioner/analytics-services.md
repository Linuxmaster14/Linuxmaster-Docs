# Analytics Services

{% hint style="success" %}
**AWS Overview Links**

* [Redshift](https://aws.amazon.com/redshift/)
* [Athena](https://aws.amazon.com/athena/)
* [Glue](https://aws.amazon.com/glue/)
* [Kinesis](https://aws.amazon.com/kinesis/)
* [EMR](https://aws.amazon.com/emr/)
* [Data Pipeline](https://aws.amazon.com/datapipeline/)
* [QuickSight](https://aws.amazon.com/quicksight/)
{% endhint %}

## Amazon Redshift

Redshift is a scalable data warehouse solution.

* Data warehousing solution
* Improves speed and efficiency
* Handles exabyte-scale data

### Redshift in the real world

1. **Data consolidation:** When you need to consolidate multiple data resources for reporting
2. **Relational databases:** When you want to run a database that doesn’t require real-time transaction processing (insert, update, and delete)

Analytics is the act of querying or processing your data.

There are several services that allow you to gain deeper insights, enhance decision-making, and act in real-time to what your data is telling you.

## Athena

Athena is a query service for Amazon S3.

* Query service
* Analyze S3 data using SQL
* Pay per query
* Considered serverless

## Glue

Gle prepares your data for analytics.

* Extract, transform, load (ETL) service
* Prepare and load data
* Helps to better understand your data

## Kinesis

Kinesis allows you to analyze data and video streams in real-time.

* Analyze real-time, streaming data
* Supports video, audio, application logs, website clickstreams, and IoT

## Elastic MapReduce (EMR)

EMR helps you process large amounts of data.

* Process big data
* Analyze data using Hadoop
* Works with big data frameworks

## Data Pipeline

Data Pipeline helps you move data between compute and storage services running either on AWS or on-premises

* Moves data at specific intervals
* Moves data based on conditions
* Sends notifications on success or failure

## QuickSight

QuickSight helps you visualize your data.

* Build interactive dashboards
* Embed dashboards in your applications

### Analytics in the real world

1. **Search data in S3:** Athena helps you query historical data stored in S3 as if they were relational data using standard SQL.
2. **Log analytics:** Kinesis helps you analyze logs in near real-time for application monitoring or fraud detection.
