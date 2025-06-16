# 📦 Serverless Data Processing Pipeline with AWS

## 📘 Overview
This project implements a serverless, event-driven architecture using AWS services to process large CSV files. It leverages:

- **Amazon S3**: For storing input and output CSV files
- **AWS Lambda**: To process CSV files and handle business logic
- **Amazon SNS**: To publish notifications about processing events
- **Amazon SQS**: To queue messages for downstream processing

## 🎯 Objectives
- Efficiently process large CSV files uploaded to S3
- Implement a decoupled architecture using SNS and SQS
- Ensure scalability and fault tolerance
- Provide clear logging and monitoring through AWS CloudWatch

## 🏗️ System Architecture
```
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
```
## 🛠️ Implementation Details
### 1. Amazon S3
- **Input Bucket**: Stores uploaded CSV files
- **Output Bucket**: Stores processed results

### 2. AWS Lambda
- **CSV Processor Function**:
  - Triggered by S3 upload events
  - Reads and processes CSV files
  - Publishes messages to SNS topic upon completion
- **SQS Consumer Function**:
  - Triggered by messages in SQS queue
  - Performs additional processing/notifications

### 3. Amazon SNS
- **Topic**: Receives messages from CSV Processor Lambda
- **Subscription**: Forwards messages to SQS queue

### 4. Amazon SQS
- **Queue**: Receives messages from SNS topic
- **Dead Letter Queue (DLQ)**: Captures unprocessable messages

## 📂 Project Structure
```
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
```
## 🚀 Deployment Steps
1. **Set Up AWS Resources**:
   - Create S3 buckets (input/output)
   - Create SNS topic and SQS queue
   - Subscribe SQS queue to SNS topic
   
2. **Configure IAM Roles**:
   - Assign permissions to Lambda functions for S3/SNS/SQS access
   
3. **Deploy Lambda Functions**:
   - Package and deploy using AWS SAM or AWS CLI
   
4. **Set Up Triggers**:
   - Configure S3 to trigger `csv_processor` Lambda
   - Configure SQS to trigger `sqs_consumer` Lambda

## 🧪 Testing the Pipeline
1. **Upload CSV File**: Place test file in input bucket
2. **Monitor Processing**: Check CloudWatch logs for `csv_processor`
3. **Verify SNS/SQS**: Confirm message flow to SNS → SQS
4. **Check SQS Consumer**: Verify `sqs_consumer` processed message

## 📈 Monitoring and Logging
- **CloudWatch Logs**: Monitor Lambda execution logs
- **CloudWatch Metrics**: Track invocation counts, durations, errors
- **Alarms**: Set up for failed invocations or DLQ messages

## 🧰 Tools and Technologies
- **Languages**: Python 3.x
- **AWS Services**:
  - S3, Lambda, SNS, SQS, CloudWatch
- **Infrastructure as Code**: AWS SAM/CloudFormation

---

# 🚀 GitHub Actions Workflow for AWS SAM Deployment

## 📁 Directory Structure
```
project-root/
├── lambda_functions/
│   ├── csv_processor/
│   │   └── handler.py
│   └── sqs_consumer/
│       └── handler.py
├── template.yaml
├── requirements.txt
└── .github/
    └── workflows/
        └── deploy.yml
```
## 🔐 GitHub Secrets Configuration
Add these secrets in GitHub (Settings > Secrets > Actions):
- `AWS_ACCESS_KEY_ID`
- `AWS_SECRET_ACCESS_KEY`
- `AWS_REGION` (e.g., `us-east-1`)
- `S3_BUCKET` (for deployment artifacts)

## 🧾 Sample `deploy.yml` Workflow
```yaml
name: Deploy AWS SAM Application

on:
  push:
    branches: [main]

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v3
        
      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.9'
          
      - name: Install AWS SAM CLI
        uses: aws-actions/setup-sam@v2
        
      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ${{ secrets.AWS_REGION }}
          
      - name: Install Dependencies
        run: pip install -r requirements.txt
        
      - name: Build SAM Application
        run: sam build
        
      - name: Deploy SAM Application
        run: |
          sam deploy --no-confirm-changeset --no-fail-on-empty-changeset \
            --stack-name csv-processing-pipeline \
            --s3-bucket ${{ secrets.S3_BUCKET }} \
            --capabilities CAPABILITY_IAM \
            --region ${{ secrets.AWS_REGION }}



Workflow Actions:
- Triggers on main branch pushes

- Checks out repository code

- Sets up Python environment

- Installs AWS SAM CLI

- Configures AWS credentials

- Installs project dependencies

- Builds SAM application

- Deploys to AWS
