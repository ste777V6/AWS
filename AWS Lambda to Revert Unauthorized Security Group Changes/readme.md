Using AWS Lambda to Revert Unauthorized Security Group Changes
Introduction
In this blog, we will demonstrate how to proactively monitor a web server EC2 security group for any traffic rule changes using Lambda along with a few other services. If we detect any unauthorized changes, we will then undo them using Lambda. There will also be an SNS notification sent out to the admins, which is us.

In this activity we will be working with the following AWS services:

Lambda

SNS

CloudTrail

CloudWatch

CloudFormation

EC2

VPC

S3

This scenario is a working example of how we can use Lambda to perform various operational tasks within our account, especially ones that maintain our security posture.

Architecture
The below diagram illustrates the high-level architecture and the steps to be followed in sequence.

#image

### Prerequisites
- An AWS account
- An existing VPC such as the default VPC

### Steps
Create Your CloudTrail Trail
Navigate to the CloudTrail service. From the left-hand menu, access Trails and click on Create trail.


Specify your desired name for the trail

For Storage location section, choose New S3 bucket and keep the default generated name. This will create an S3 bucket to load API event trail logs.

Enable CloudWatch Logs to create a log group to which the trail will send events.

Choose Event type as Management events

Choose API activity to log both Read and Write activities.

Leave other settings as is and finally click Create trail.

Deploy the CloudFormation Template
The CloudFormation template will create several resources:

A web security group that is monitored for any changes

An IAM role attached to the Lambda function as an execution role with permissions to remove any changes to the security group inbound rules.

A Lambda Permission which allows CloudWatch Events to invoke the Lambda function

A Lambda function that revokes any changes to the web security group and publishes a message to an SNS topic

A CloudWatch Event Rule that captures security group change events and triggers the Lambda function (Note: CloudWatch Events is now Amazon EventBridge)

SNS topic with an email subscription to send an email notification when the Lambda function is invoked

Follow the below steps to deploy these resources:

Using your preferred code editor, create a .yaml file with the below CloudFormation template.
