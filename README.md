# VPC Flow Log Enablement for Cortex Cloud 

## New S3 Bucket for VPC Flow Logs
Run the Cloudformation template
1. Provide a Stack Name: CortexCloudVPCFlowLogs
2. Parameters
   - Create New S3 Bucket: Yes
   - ARN of Existing S3 Bucket: Leave Blank
   - New S3 Bucket Name (if creating new): Any name
   - SQS Queue Name: Any name
   - Cortex XSIAM AWS Account ID: Leave default (unless FEDERAL TENANT)
   - External ID from Cortex XSIAM: Random https://www.uuidgenerator.net/version4

## Existing S3 Bucket for Flow Logs See Below:
Run the Cloudformation template

1. Provide a Stack Name: CortexCloudVPCFlowLogs
2. Parameters
   - Create New S3 Bucket: No
   - ARN of Existing S3 Bucket: ARN of your S3 Bucket that houses the Flow Logs (ensure you have the correct format)
   - New S3 Bucket Name (if creating new): LEAVE BLANK
   - SQS Queue Name: Any name
   - Cortex XSIAM AWS Account ID: Leave default (unless FEDERAL TENANT)
   - External ID from Cortex XSIAM: Random https://www.uuidgenerator.net/version4



What you need to do if using an existing S3 bucket:

If you set CreateNewBucket to No and provide ExistingBucketARN, you will need to manually configure the S3 event notification on that existing bucket to send s3:ObjectCreated:* events to the SQS queue created by this CloudFormation stack.

Steps to manually configure S3 Event Notification:

1. Go to the AWS S3 console.
2. Navigate to your existing S3 bucket.
3. Go to the Properties tab.
4. Scroll down to the Event notifications section.
5. Click Create event notification.
6. Event name: Give it a meaningful name (e.g., CortexXSIAMFlowLogs).
7. Prefix (optional): If your VPC Flow Logs are stored under a specific prefix (e.g., AWSLogs/123456789012/vpcflowlogs/), you can specify it here.
8. Suffix (optional): Usually not needed for flow logs.
9. Events types: Select All object create events (s3:ObjectCreated:*).
10. Destination:
11. Choose SQS Queue.
12. Select Choose from your SQS queues.
13. In the "SQS queue" dropdown, find and select the SQS queue created by your CloudFormation stack. Its name will be something like vpc-flow-log-sqs-us-east-1 (or whatever your QueueNameFromUser and region are). You can get its ARN from the CloudFormation stack's Outputs: SQSQueueARN.
14. Click Save changes.

Once you manually configure this, S3 will start sending notifications to the SQS queue, and Cortex XSIAM will be able to ingest the logs.
