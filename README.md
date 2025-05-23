Sure! Here's the full `README.md` content in one block — ready for copy-paste:

---

````markdown
# Database Refresh Copy Script 📜

This Python script (`db_refresh_copy.py`) automates the process of copying database dumps between AWS EC2 instances using AWS Systems Manager (SSM) and Amazon S3. It supports multi-threaded execution, file compression, splitting large files, and service management (start/stop) on source and destination instances.

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Features](#-features)
- [Prerequisites](#-prerequisites)
- [Installation](#-installation)
- [Usage](#-usage)
- [Input File Format](#-input-file-format)
- [Output File Format](#-output-file-format)
- [How It Works](#-how-it-works)
- [Logging](#-logging)
- [Error Handling](#-error-handling)
- [Contributing](#-contributing)
- [License](#-license)

---

## 📖 Overview

The `db_refresh_copy.py` script facilitates the transfer of database dumps between AWS EC2 instances. It:

- Uses AWS SSM to execute commands remotely
- Compresses and optionally splits large dumps
- Uploads them to S3 and downloads/extracts on the target
- Supports dry-run mode and multi-threaded execution

---

## ✨ Features

- **Multi-threaded Execution** 🚀 — Concurrent processing of destination instances
- **File Compression and Splitting** 🗜️ — Efficiently handles large dumps
- **Service Management** ⚙️ — Stops/starts services on instances via SSM
- **Dry-run Mode** 🔍 — Simulates actions without making changes
- **Disk Space Validation** 💾 — Prevents execution if space is insufficient
- **Size Verification** ✅ — Ensures source and target file size match
- **Robust Error Handling** 🛑 — Handles AWS SSO, SSM, and S3 errors gracefully

---

## 🛠️ Prerequisites

- **Python**: 3.8+
- **AWS CLI** with SSO profile configured
- **Python Libraries**:
  ```bash
  pip install boto3
````

* **AWS Permissions**:

  * SSM (`AWS-RunShellScript`)
  * S3 read/write
  * EC2 control permissions

* **Tools on EC2**:

  * `tar`, `pigz`
  * `aws` CLI

* **Input File**: JSON (`event.json`) describing services and instances

---

## ⚙️ Installation

```bash
git clone https://github.com/your-repo/db-refresh-copy.git
cd db-refresh-copy
pip install -r requirements.txt
aws configure  # ensure your AWS CLI is properly configured
```

Place your `event.json` file inside the `event` directory.

---

## 🚀 Usage

1. Prepare your `event.json` file (see format below)
2. Run the script:

```bash
python db_refresh_copy.py
```

**Dry-run Mode**: Set `"dryRun": true` in the input JSON to simulate actions without executing them.

---

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
        },
        {
          "environment": "ppj",
          "instanceId": "i-0591295a1783a221a",
          "path": "/opt/atpco/engine/db/neo4j/chgdetroutings/Routings/"
        }
      ]
    }
  ],
  "s3Bucket": "ppj-transfer-bucket",
  "dryRun": true
}
```

---

## 📤 Output File Format

### Dry-run Example

```json
{
  "status": "done",
  "results": [
    {
      "name": "Routings Engine",
      "status": "Success",
      "upload_result": {
        "status": "Success",
        "message": "Dry-run: Upload skipped"
      },
      "download_results": [
        {
          "instanceId": "i-057d31b7dfd13887d",
          "status": "Success",
          "message": "Dry-run: Download skipped"
        },
        {
          "instanceId": "i-0591295a1783a221a",
          "status": "Success",
          "message": "Dry-run: Download skipped"
        }
      ],
      "message": "All destinations completed"
    }
  ]
}
```

### Normal Execution

```json
{
  "status": "done",
  "results": [
    {
      "name": "Routings Engine",
      "status": "Success",
      "upload_result": {
        "status": "Success",
        "message": "Upload completed",
        "duration_seconds": 271.87
      },
      "download_results": [
        {
          "instanceId": "i-057d31b7dfd13887d",
          "status": "Success",
          "message": "Download and extraction completed",
          "duration_seconds": 209.64
        },
        {
          "instanceId": "i-0591295a1783a221a",
          "status": "Success",
          "message": "Download and extraction completed",
          "duration_seconds": 267.83
        }
      ],
      "message": "All destinations completed"
    }
  ]
}
```

---

## 🔄 How It Works

### Initialization

* Reads `event.json`
* Sets up logging and AWS session

### For Each Service:

#### Source Instance

* Stops service
* Validates disk space
* Compresses and uploads to S3
* Restarts service

#### Destination Instances

* Stops service
* Downloads from S3
* Extracts and verifies file size
* Restarts service

### Output

* JSON file with status and logs
* Detailed logging for errors, execution time, and progress

---

## 📜 Logging

The script uses Python's `logging` module at the `INFO` level.

Logged actions include:

* ✅ Start/End of execution
* 🔄 Service-specific operations
* 📤 Upload/📥 Download activities
* ❌ Errors and warnings

---

## 🚨 Error Handling

* **SSO Expiry**: Prompts to run `aws sso login`
* **Disk Issues**: Warns if space is insufficient
* **SSM Failures**: Captures and logs command errors
* **S3 Failures**: Logs detailed error messages
* **General Exceptions**: Stack traces are logged for debugging

---

## 🤝 Contributing

Contributions are welcome! Follow these steps:

```bash
# 1. Fork the repo
# 2. Create a new branch
git checkout -b feature/YourFeature

# 3. Make changes and commit
git commit -m "Add YourFeature"

# 4. Push and open a pull request
git push origin feature/YourFeature
```

---

## 📄 License

This project is licensed under the [MIT License](LICENSE).

```

---

You can paste the above directly into your `README.md` file. Let me know if you want me to generate the same with badges or GitHub Actions instructions.
```
