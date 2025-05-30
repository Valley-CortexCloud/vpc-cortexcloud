# vpc-cortexcloud

## Existing S3 Bucket for Flow Logs See Below:
What you need to do if using an existing S3 bucket:

If you set CreateNewBucket to No and provide ExistingBucketARN, you will need to manually configure the S3 event notification on that existing bucket to send s3:ObjectCreated:* events to the SQS queue created by this CloudFormation stack.

Steps to manually configure S3 Event Notification:

1. Go to the AWS S3 console.
2. Navigate to your existing S3 bucket.
Go to the Properties tab.
Scroll down to the Event notifications section.
Click Create event notification.
Event name: Give it a meaningful name (e.g., CortexXSIAMFlowLogs).
Prefix (optional): If your VPC Flow Logs are stored under a specific prefix (e.g., AWSLogs/123456789012/vpcflowlogs/), you can specify it here.
Suffix (optional): Usually not needed for flow logs.
Events types: Select All object create events (s3:ObjectCreated:*).
Destination:
Choose SQS Queue.
Select Choose from your SQS queues.
In the "SQS queue" dropdown, find and select the SQS queue created by your CloudFormation stack. Its name will be something like vpc-flow-log-sqs-us-east-1 (or whatever your QueueNameFromUser and region are). You can get its ARN from the CloudFormation stack's Outputs: SQSQueueARN.
Click Save changes.
Once you manually configure this, S3 will start sending notifications to the SQS queue, and Cortex XSIAM will be able to ingest the logs.
