📦 Serverless Data Processing Pipeline with AWS
📘 Overview
This project implements a serverless, event-driven architecture using AWS services to process large CSV files. It leverages:

Amazon S3: For storing input and output CSV files.

AWS Lambda: To process CSV files and handle business logic.

Amazon SNS (Simple Notification Service): To publish notifications about processing events.

Amazon SQS (Simple Queue Service): To queue messages for downstream processing.

🎯 Objectives
Efficiently process large CSV files uploaded to S3.

Implement a decoupled architecture using SNS and SQS.

Ensure scalability and fault tolerance.

Provide clear logging and monitoring through AWS CloudWatch.
aws.amazon.com

🏗️ System Architecture
plaintext
Copy
Edit
+----------------+        +----------------+        +----------------+        +----------------+
|                |        |                |        |                |        |                |
|    Amazon S3   +------->+  AWS Lambda    +------->+     Amazon SNS +------->+     Amazon SQS  |
|  (CSV Upload)  |        | (CSV Processor)|        | (Notification) |        |  (Message Queue)|
|                |        |                |        |                |        |                |
+----------------+        +----------------+        +----------------+        +----------------+
                                                                                         |
                                                                                         v
                                                                                 +----------------+
                                                                                 |                |
                                                                                 |  AWS Lambda    |
                                                                                 | (SQS Consumer) |
                                                                                 |                |
                                                                                 +----------------+
🛠️ Implementation Details
Amazon S3
Input Bucket: Stores the uploaded CSV files.

Output Bucket: Stores the processed results.
exam-labs.com
+8
aws.amazon.com
+8
awsfundamentals.com
+8

2. AWS Lambda
CSV Processor Function:

Triggered by S3 upload events.

Reads and processes the CSV file.

Publishes a message to an SNS topic upon completion.

SQS Consumer Function:

Triggered by messages in the SQS queue.

Performs additional processing or notifications based on the message content.

3. Amazon SNS
Topic: Receives messages from the CSV Processor Lambda function.

Subscription: Forwards messages to the SQS queue.

4. Amazon SQS
Queue: Receives messages from the SNS topic.

Dead Letter Queue (DLQ): Captures messages that couldn't be processed successfully after multiple attempts.

📂 Project Structure
plaintext
Copy
Edit
project-root/
├── lambda_functions/
│   ├── csv_processor/
│   │   └── handler.py
│   └── sqs_consumer/
│       └── handler.py
├── infrastructure/
│   └── template.yaml
├── requirements.txt
└── README.md
🚀 Deployment Steps
Set Up AWS Resources:

Create S3 buckets for input and output.

Create an SNS topic and an SQS queue.

Subscribe the SQS queue to the SNS topic.
christiangiacomi.com

Configure IAM Roles:

Assign necessary permissions to Lambda functions for accessing S3, SNS, and SQS.

Deploy Lambda Functions:

Package and deploy the csv_processor and sqs_consumer functions using AWS SAM or the AWS CLI.

Set Up Triggers:

Configure S3 to trigger the csv_processor Lambda on object creation.

Configure the SQS queue to trigger the sqs_consumer Lambda.

🧪 Testing the Pipeline
Upload a CSV File:

Place a test CSV file into the input S3 bucket.

Monitor Processing:

Check CloudWatch logs for the csv_processor Lambda to verify processing.

Verify SNS and SQS:

Ensure that a message was published to the SNS topic and received by the SQS queue.

Check SQS Consumer:

Verify that the sqs_consumer Lambda processed the message from the SQS queue.

📈 Monitoring and Logging
CloudWatch Logs:

Monitor logs for both Lambda functions to troubleshoot issues.

CloudWatch Metrics:

Track metrics such as invocation counts, durations, and errors.

Alarms:

Set up alarms for failed Lambda invocations or messages in the DLQ.

🧰 Tools and Technologies
Languages: Python 3.x

AWS Services:

Amazon S3

AWS Lambda

Amazon SNS

Amazon SQS

AWS CloudWatch

Infrastructure as Code: AWS SAM or CloudFormation
