# AWS Bedrock Knowledge Base with Aurora Serverless

This project sets up an AWS Bedrock Knowledge Base integrated with an Aurora Serverless PostgreSQL database. It also includes scripts for database setup and file upload to S3.

## Table of Contents

1. [Project Overview](#project-overview)
2. [Prerequisites](#prerequisites)
3. [Project Structure](#project-structure)
4. [Deployment Steps](#deployment-steps)
5. [Using the Scripts](#using-the-scripts)
6. [Customization](#customization)
7. [Troubleshooting](#troubleshooting)

## Project Overview

This project consists of several components:

1. Stack 1 - Terraform configuration for creating:
   - A VPC
   - An Aurora Serverless PostgreSQL cluster
   - s3 Bucket to hold documents
   - Necessary IAM roles and policies

2. Stack 2 - Terraform configuration for creating:
   - A Bedrock Knowledge Base
   - Necessary IAM roles and policies

3. A set of SQL queries to prepare the Postgres database for vector storage
4. A Python script for uploading files to an s3 bucket

The goal is to create a Bedrock Knowledge Base that can leverage data stored in an Aurora Serverless database, with the ability to easily upload supporting documents to S3. This will allow us to ask the LLM for information from the documentation.

## Libraries and Tools

- boto3: This is the Amazon Web Services (AWS) Software Development Kit (SDK) for Python. It allows Python developers to write software that makes use of services like Amazon S3, Amazon EC2, and Amazon DynamoDB. Boto3 provides an easy-to-use, object-oriented API as well as low-level access to AWS services. It is a powerful tool for interacting with AWS resources programmatically.
- streamlit: This is an open-source app framework specifically designed for creating and sharing data applications. Streamlit allows you to build interactive web applications quickly and easily, using only Python scripts. It is particularly popular in the data science community for creating dashboards and visualizations, as it simplifies the process of turning data scripts into shareable web apps without requiring extensive web development knowledge.

## Prerequisites

Before you begin, ensure you have the following:

- AWS CLI installed and configured with appropriate credentials
- Terraform installed (version 0.12 or later)
- Python 3.10 or later
- pip (Python package manager)

## Project Structure

```
project-root/
│
├── stack1
|   ├── main.tf
|   ├── outputs.tf
|   └── variables.tf
|
├── stack2
|   ├── main.tf
|   ├── outputs.tf
|   └── variables.tf
|
├── modules/
│   ├── aurora_serverless/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── outputs.tf
│   └── bedrock_kb/
│       ├── main.tf
│       ├── variables.tf
│       └── outputs.tf
│
├── scripts/
│   ├── aurora_sql.sql
│   └── upload_to_s3.py
│
├── spec-sheets/
│   └── machine_files.pdf
│
└── README.md
```

## Deployment Steps

1. Clone this repository to your local machine.

2. Navigate to the project Stack 1. This stack includes VPC, Aurora servlerless and S3

3. Initialize Terraform:
   ```
   terraform init
   ```

4. Review and modify the Terraform variables in `main.tf` as needed, particularly:
   - AWS region
   - VPC CIDR block
   - Aurora Serverless configuration
   - s3 bucket

5. Deploy the infrastructure:
   ```
   terraform apply
   ```
   Review the planned changes and type "yes" to confirm.

6. After the Terraform deployment is complete, note the outputs, particularly the Aurora cluster endpoint.

7. Prepare the Aurora Postgres database. This is done by running the sql queries in the script/ folder. This can be done through Amazon RDS console and the Query Editor.

8. Navigate to the project Stack 2. This stack includes Bedrock Knowledgebase

9. Initialize Terraform:
   ```
   terraform init
   ```

10. Use the values outputs of the stack 1 to modify the values in `main.tf` as needed:
     - Bedrock Knowledgebase configuration

11. Deploy the infrastructure:
      ```
      terraform apply
      ```
      - Review the planned changes and type "yes" to confirm.


12. Upload pdf files to S3, place your files in the `spec-sheets` folder and run:
      ```
      python scripts/upload_to_s3.py
      ```
      - Make sure to update the S3 bucket name in the script before running.

13. Sync the data source in the knowledgebase to make it available to the LLM.

## Using the Scripts

### S3 Upload Script

The `upload_to_s3.py` script does the following:
- Uploads all files from the `spec-sheets` folder to a specified S3 bucket
- Maintains the folder structure in S3

To use it:
1. Update the `bucket_name` variable in the script with your S3 bucket name.
2. Optionally, update the `prefix` variable if you want to upload to a specific path in the bucket.
3. Run `python scripts/upload_to_s3.py`.

## Complete chat app

### Complete invoke model and knoweldge base code
- Open the bedrock_utils.py file and the following functions:
  - query_knowledge_base
  - generate_response

### Complete the prompt validation function
- Open the bedrock_utils.py file and the following function:
  - valid_prompt

  Hint: categorize the user prompt

## Troubleshooting

- If you encounter permissions issues, ensure your AWS credentials have the necessary permissions for creating all the resources.
- For database connection issues, check that the security group allows incoming connections on port 5432 from your IP address.
- If S3 uploads fail, verify that your AWS credentials have permission to write to the specified bucket.
- For any Terraform errors, ensure you're using a compatible version and that all module sources are correctly specified.

For more detailed troubleshooting, refer to the error messages and logs provided by Terraform and the Python scripts.

Temperature and top_p are crucial parameters in AI language models that influence response generation.

### Model Parameters: explanation of how temperature and top_p affect AI responses

## Temperature
**Temperature** controls the randomness of the model's outputs. Lower values (0-0.3) yield more deterministic and coherent responses, ideal for factual tasks. Medium values (0.3-0.7) balance creativity and coherence, while higher values (0.7-1) promote diverse and imaginative outputs but may sacrifice clarity.

## Top_p
**Top_p**, or nucleus sampling, determines the range of tokens considered during generation. A low top_p (e.g., 0.5) restricts choices to the most probable tokens, enhancing focus, whereas a high top_p (e.g., 0.9) allows for a broader selection, increasing diversity in responses. 

Together, these parameters enable fine-tuning of AI outputs for various applications, from precise answers to creative storytelling.
