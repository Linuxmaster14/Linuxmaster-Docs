# Auditing, Monitoring, and Logging Services

{% hint style="success" %}
**AWS Overview Links**

* [CloudWatch](https://aws.amazon.com/cloudwatch/)
* [CloudTrail](https://aws.amazon.com/cloudtrail/)
{% endhint %}

These services give you insight into your systems' performance and help you proactively find and resolve errors.

## CloudWatch

CloudWatch is a collection of services that help you monitor and observe your cloud resources.

* Collects metrics, logs, and events
* Detect anomalies in your environment
* Set alarms
* Visualize logs

### CloudWatch Services

* **CloudWatch Alarms:** Set high-resolution alarms
* **CloudWatch Logs:** Monitor application logs
* **CloudWatch Metrics:** Visualize time-series data
* **CloudWatch Events:** Trigger an event based on a condition

### CloudWatch in the real world

Provide real-time monitoring on EC2 instances.

CloudWatch Alarms can notify you if an EC2 instance goes into the stopped state or usage goes above a specific utilization.

Receive a notification when root user activity is detected in your account.

Create a CloudWatch event rule to notify you when root user API calls are detected in your account indicating root user activity.

## CloudTrail

CloudTrail tracks user activity and API calls within your account.

* Log and retain account activity
* Track activity through the console, SDKs, and CLI
* Identify which user made changes
* Detect unusual activity in your account

### CloudTrail in the real world

Track the time a particular event occurred in your account.

You can troubleshoot events over the past 90 days using the CloudTrail event history log to find the specific time an event occurred per region. You can create a custom trail to extend past 90 days.

Things you can track with CloudTrail:

* Username
* Event time and name
* IP address
* Access key
* Region
* Error code
