# VPC Flow Log Enablement for Cortex Cloud + XSIAM ☁️

This guide outlines the steps to enable VPC Flow Log ingestion into Cortex XSIAM using AWS CloudFormation. You can choose to create a new S3 bucket for your VPC Flow Logs or use an existing one.

---

## 🚀 Option 1: New S3 Bucket for VPC Flow Logs

Follow these steps if you want to create a new S3 bucket to store your VPC Flow Logs.

### Running the CloudFormation Template

1.  Navigate to the AWS CloudFormation console.
2.  Click on **Create stack** (with new resources).
3.  Upload or specify the template file.
4.  Provide the following **Stack Name**:
    * `CortexCloudVPCFlowLogs`
5.  Configure the **Parameters** as follows:
    * **Create New S3 Bucket**: `Yes`
    * **ARN of Existing S3 Bucket**: Leave this field **blank**.
    * **New S3 Bucket Name (if creating new)**: Provide a unique S3 bucket name (e.g., `my-cortex-vpc-flow-logs-unique-name`).
    * **SQS Queue Name**: Provide a unique name for the SQS queue (e.g., `CortexVPCFlowLogsQueue`).
    * **Cortex XSIAM AWS Account ID**: Leave the default value unless you are using a **FEDERAL TENANT**.
    * **External ID from Cortex XSIAM**: Generate a random UUID v4 using a tool like [https://www.uuidgenerator.net/version4](https://www.uuidgenerator.net/version4) and paste it here.

6.  Proceed through the remaining CloudFormation stack creation steps and create the stack.

---

## 📂 Option 2: Existing S3 Bucket for VPC Flow Logs

Follow these steps if you already have an S3 bucket where your VPC Flow Logs are being delivered.

### Running the CloudFormation Template

1.  Navigate to the AWS CloudFormation console.
2.  Click on **Create stack** (with new resources).
3.  Upload or specify the template file.
4.  Provide the following **Stack Name**:
    * `CortexCloudVPCFlowLogs`
5.  Configure the **Parameters** as follows:
    * **Create New S3 Bucket**: `No`
    * **ARN of Existing S3 Bucket**: Enter the ARN of your S3 bucket that houses the Flow Logs (e.g., `arn:aws:s3:::your-existing-flow-log-bucket`). **Ensure you have the correct format.**
    * **New S3 Bucket Name (if creating new)**: Leave this field **BLANK**.
    * **SQS Queue Name**: Provide a unique name for the SQS queue (e.g., `CortexVPCFlowLogsQueue`).
    * **Cortex XSIAM AWS Account ID**: Leave the default value unless you are using a **FEDERAL TENANT**.
    * **External ID from Cortex XSIAM**: Generate a random UUID v4 using a tool like [https://www.uuidgenerator.net/version4](https://www.uuidgenerator.net/version4) and paste it here.

6.  Proceed through the remaining CloudFormation stack creation steps and create the stack.

---

## ⚠️ Important: Manual Configuration for Existing S3 Bucket

If you selected **No** for `Create New S3 Bucket` and provided an `ARN of Existing S3 Bucket`, you **must manually configure** the S3 event notification on that existing bucket. This allows the bucket to send `s3:ObjectCreated:*` events to the SQS queue created by the CloudFormation stack.

### Steps to Manually Configure S3 Event Notification:

1.  **Navigate to S3**: Go to the AWS S3 console.
2.  **Select Bucket**: Navigate to your existing S3 bucket that stores the VPC Flow Logs.
3.  **Properties Tab**: Go to the **Properties** tab of the selected bucket.
4.  **Event Notifications**: Scroll down to the **Event notifications** section.
5.  **Create Event Notification**: Click **Create event notification**.
    * **Event name**: Give it a meaningful name (e.g., `CortexXSIAMFlowLogsNotification`).
    * **Prefix (optional)**: If your VPC Flow Logs are stored under a specific prefix (e.g., `AWSLogs/123456789012/vpcflowlogs/`), specify it here. This helps ensure that only relevant object creation events trigger the notification.
    * **Suffix (optional)**: Usually not needed for flow logs.
    * **Event types**: Select **All object create events** (which corresponds to `s3:ObjectCreated:*`).
    * **Destination**:
        * Choose **SQS Queue**.
        * Select **Choose from your SQS queues**.
        * In the **SQS queue** dropdown, find and select the SQS queue created by your CloudFormation stack.
            * The queue name will typically be in the format `vpc-flow-log-sqs-<region>` or based on the `SQS Queue Name` you provided (e.g., `CortexVPCFlowLogsQueue`).
            * You can retrieve the exact SQS Queue ARN from the **Outputs** tab of your CloudFormation stack (look for `SQSQueueARN`).
6.  **Save Changes**: Click **Save changes**.

✅ **Completion**: Once this manual S3 event notification is configured, S3 will start sending notifications to the SQS queue, enabling Cortex XSIAM to ingest the VPC Flow Logs from your existing bucket.

---
