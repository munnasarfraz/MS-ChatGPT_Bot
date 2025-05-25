# Database Refresh Copy Script 📜

This Python script (`db_refresh_copy.py`) automates the process of copying database dumps between AWS EC2 instances using AWS Systems Manager (SSM) and Amazon S3. It supports multi-threaded execution, file compression, splitting large files, and service management (start/stop) on source and destination instances.

## 📋 Table of Contents
- [Overview](#overview)
- [Features](#features)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Usage](#usage)
- [Input File Format](#input-file-format)
- [Output File Format](#output-file-format)
- [How It Works](#how-it-works)
- [Logging](#logging)
- [Error Handling](#error-handling)
- [Contributing](#contributing)
- [License](#license)

## 📖 Overview
The `db_refresh_copy.py` script is designed to facilitate the transfer of database dumps between AWS EC2 instances. It uses AWS SSM to execute commands on instances, compresses and optionally splits database dumps, uploads them to an S3 bucket, and downloads/extracts them on destination instances. The script supports dry-run mode for testing and multi-threaded processing for efficiency.

## ✨ Features
- **Multi-threaded Execution** 🚀: Processes multiple destination instances concurrently to reduce execution time
- **File Compression and Splitting** 🗜️: Compresses database dumps and splits large files (>5GB) into smaller parts for efficient transfer
- **Service Management** ⚙️: Stops and starts services on source and destination instances using SSM
- **Dry-run Mode** 🔍: Simulates the process without making changes for testing purposes
- **Disk Space Validation** 💾: Checks available disk space before processing to prevent failures
- **Size Verification** ✅: Compares source and destination file sizes to ensure data integrity
- **Error Handling** 🛑: Robust error handling for AWS SSO, SSM, and S3 operations

## 🛠️ Prerequisites
- **Python 3.8+** 🐍
- **AWS CLI** configured with appropriate profiles and SSO authentication
- **Required Python libraries**:
  ```bash
  pip install boto3 pathlib
  ```
- **AWS Permissions** 🔑:
  - Access to AWS SSM (`AWS-RunShellScript` document)
  - Read/write permissions for the specified S3 bucket
  - Permissions to manage EC2 instances and services
- **Tools on EC2 Instances**:
  - `tar` and `pigz` for compression
  - `aws` CLI installed for S3 operations
- **Input File**: A JSON file (`event.json`) specifying services, instances, and paths

## ⚙️ Installation
1. Clone the repository:
   ```bash
   git clone https://github.com/your-repo/db-refresh-copy.git
   cd db-refresh-copy
   ```
2. Install required Python packages:
   ```bash
   pip install -r requirements.txt
   ```
3. Configure AWS CLI:
   ```bash
   aws configure
   ```
4. Place the input `event.json` file in the `event` directory

## 🚀 Usage
1. Prepare the `event.json` file (see [Input File Format](#input-file-format))
2. Run the script:
   ```bash
   python db_refresh_copy.py
   ```
3. The script will generate an output file in the `output` directory

**Dry-run Mode**: Set `"dryRun": true` in `event.json` to simulate without changes

## 📥 Input File Format
```json
{
    "services": [
        {
            "enabled": true,
            "name": "Routings Engine",
            "source": {
                "environment": "engu",
                "instanceId": "i-0b1b77f5c6948d082",
                "path": "/opt/atpco/engine/db/neo4j/chgdetroutings/Routings"
            },
            "destinations": [
                {
                    "environment": "ppj",
                    "instanceId": "i-057d31b7dfd13887d",
                    "path": "/opt/atpco/engine/db/neo4j/chgdetroutings/Routings"
                }
            ]
        }
    ],
    "s3Bucket": "ppj-transfer-bucket",
    "dryRun": true
}
```


- **services**: List of services to process
  - `enabled`: Boolean to enable/disable processing of the service
  - `name`: Service name (e.g., "Routings Engine")
  - `source`: Source instance details (environment, instance ID, path)
  - `destinations`: List of destination instances (environment, instance ID, path)
- **s3Bucket**: S3 bucket for temporary storage
- **dryRun**: Boolean to enable dry-run mode

## 📤 Output File Format
The script generates a JSON output file in the `output` directory (e.g., `db_copy_output_YYYYMMDD_HHMMSS.json`) with the following structure:

**Dry-run Mode**:
```json
{
  "status": "done",
  "results": [
    {
      "name": "Routings Engine",
      "status": "Success",
      "upload_result": {
        "status": "Success",
        "service": "Routings Engine",
        "message": "Dry-run: Upload skipped"
      },
      "download_results": [
        {
          "environment": "ppj",
          "instanceId": "i-057d31b7dfd13887d",
          "path": "/opt/atpco/engine/db/neo4j/chgdetroutings/Routings",
          "status": "Success",
          "service": "Routings Engine",
          "message": "Dry-run: Download skipped"
        },
        {
          "environment": "ppj",
          "instanceId": "i-0591295a1783a221a",
          "path": "/opt/atpco/engine/db/neo4j/chgdetroutings/Routings/",
          "status": "Success",
          "service": "Routings Engine",
          "message": "Dry-run: Download skipped"
        }
      ],
      "message": "All destinations completed"
    }
  ]
}
```

**Normal Execution**:
```json
{
  "status": "done",
  "results": [
    {
      "name": "Routings Engine",
      "status": "Success",
      "upload_result": {
        "status": "Success",
        "service": "Routings Engine",
        "message": "Upload completed",
        "duration_seconds": 271.87160444259644
      },
      "download_results": [
        {
          "environment": "ppj",
          "instanceId": "i-057d31b7dfd13887d",
          "path": "/opt/atpco/engine/db/neo4j/chgdetroutings/Routings",
          "status": "Success",
          "service": "Routings Engine",
          "message": "Download and extraction completed for Routings Engine",
          "details": [
            "Download and extraction completed successfully"
          ],
          "duration_seconds": 209.64332580566406
        },
        {
          "environment": "ppj",
          "instanceId": "i-0591295a1783a221a",
          "path": "/opt/atpco/engine/db/neo4j/chgdetroutings/Routings/",
          "status": "Success",
          "service": "Routings Engine",
          "message": "Download and extraction completed for Routings Engine",
          "details": [
            "Download and extraction completed successfully"
          ],
          "duration_seconds": 267.83109521865845
        }
      ],
      "message": "All destinations completed"
    }
  ]
}
```

- **status**: Overall execution status ("done")
- **results**: List of service results, including:
  - `name`: Service name
  - `status`: Success or Error
  - `upload_result`: Details of the upload process (status, message, duration)
  - `download_results`: List of download results for each destination instance
  - `message`: Summary message

## 🔄 How It Works
1. **Initialization**:
   - Loads the input `event.json` file
   - Configures logging and AWS sessions

2. **Service Processing**:
   - Iterates through enabled services in the input file
   - For each service:
     - **Source Instance**:
       - Stops the service using SSM
       - Checks available disk space
       - Compresses the database dump (splits if >5GB)
       - Uploads the compressed file(s) to S3
       - Restarts the service
     - **Destination Instances**:
       - Stops the service
       - Downloads and extracts the file(s) from S3
       - Restarts the service
       - Verifies file sizes to ensure data integrity

3. **Output**:
   - Generates a JSON output file with execution details
   - Logs all actions and errors

## 📜 Logging
- The script uses Python's `logging` module with INFO level by default
- Logs include:
  - Start/end of script execution 📜
  - Service processing status 🔄
  - Upload/download progress 📤📥
  - Errors and warnings ❌⚠️
- Logs are prefixed with emojis for clarity (e.g., ✅ for success, ❌ for errors)

## 🚨 Error Handling
- **AWS SSO Errors**: Prompts to run `aws sso login --profile env` if the SSO session expires
- **Disk Space Issues**: Checks available disk space and logs the required cleanup size if insufficient
- **SSM Command Failures**: Captures and logs SSM command errors
- **S3 Upload/Download Failures**: Logs specific errors and marks the service status as "Error"
- **General Exceptions**: Logs full stack traces for debugging



## 📄 S3-Bucket Policy
**S3 policy**
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::985663031727:root"
            },
            "Action": [
                "s3:PutObject",
                "s3:ListBucket",
                "s3:GetBucketLocation"
            ],
            "Resource": [
                "arn:aws:s3:::ppj-transfer-bucket",
                "arn:aws:s3:::ppj-transfer-bucket/*"
            ]
        },
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::985663031727:root"
            },
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::ppj-transfer-bucket/*"
        }
    ]
}
```

