# Managing and Integration Services

{% hint style="success" %}
**AWS Overview Links**

* [SQS](https://aws.amazon.com/sqs/)
* [SNS](https://aws.amazon.com/sns/)
* [SES](https://aws.amazon.com/ses/)
{% endhint %}

Coupling defines the interdependencies or connections between components of a system. Loose coupling helps reduce the risk of cascading failures between components.

* **Tight coupling:** Tightly coupled components are highly dependent on each other. (**Monolithic Application**)
* **Loose coupling:** Loosly coupled components are connected but not dependent on each other. (**Microservices**)

Queue are used to implement loosely coupled systems.

## Simple Queue Service (SQS)

SQS is a message quering service that allows you to build loosely coupled systems.

* Allows component-to-component communication using messages
* Multiple components (or producers) can add messages to the queue
* Messages are processed in an asynchronous manner

There are often times that users of your applications need to notified when certain events happen.

## Simple Notification Service (SNS)

SNS allows you to send emails and text messages from your applications.

* Send emails and messages
* Publish messages to a topic
* Subscribers receive messages

### SNS in the real world

Send an email when the CPU utilization of an EC2 instance goes above 80%.

SNS works with CloudWatch when an alarm’s metric threshold is breached to send an email.

## Simple Email Service (SES)

SES is an email service that allows you to send richly formatted HTML emails from your applications.

* Ideal choice for marketing campaigns or professional emails
* Unlike SNS, SES sends HTML emails

### SES in the real world

Send a marketing email and track open or click-through rates.

SES allows you to send richly formatted HTML emails in bulk and gain valuable insights about the effectiveness of your campaign.
