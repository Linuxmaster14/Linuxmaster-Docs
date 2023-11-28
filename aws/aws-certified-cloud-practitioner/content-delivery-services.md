# Content Delivery Services

{% hint style="success" %}
**AWS Overview Links**

* [CloudFront](https://aws.amazon.com/cloudfront/)
* [Global Accelerator](https://aws.amazon.com/global-accelerator/)
* [S3 Transfer Accelerator](https://aws.amazon.com/s3/transfer-acceleration/)
{% endhint %}

#### Content Delivery Network (CDN)

A CDN is a mechanism to deliver content quickly and efficiently based on geographic location.

Latency simply means the time it takes to respond to a request, and a CND provides low latency.

## Amazon CloudFront

CloudFront is a CDN that delivers data and applications globally with low latency

* Makes content available globally or restricts it based on location
* Speeds up delivery of static and dynamic web content
* Uses edge locations to cache content

#### CloudFront in the real world

1. **S3 static websites:** CloudFront is often used with S3 to deploy content globally.
2. **Prevent attacks:** CloudFront can stop certain web attacks, like DDoS.
3. **IP address blocking:** Geo-restriction prevents users in certain countries from accessing content.

## Amazon Global Accelerator

Global Accelerator sends your users through the AWS global network when accessing your content, speeding up delivery.

* Improves latency and availability of single-Region applications
* Sends traffic through the AWS global network infrastructure
* 60% performance boost
* Automatically re-routes traffic to healthy available regional endpoints

## Amazon S3 Transfer Acceleration

Amazon S3 Transfer Acceleration improves content uploads and downloads to and from S3 buckets.

* Fast transfer of files over long distances
* Uses CloudFront’s globally distributed edge locations
* Customers around the world can upload to a central bucket
