Great catch! Here's your **updated full README**, now instructing users to install dependencies via `requirements.txt` instead of listing the packages individually.

You can copy-paste the following into your `README.md`:

---

```markdown
# 📊 CSV Comparison Tool

## 🚀 Overview
This tool is designed to compare two sets of CSV files stored in AWS S3 buckets. It:

- Reads `.zip` files directly from two different S3 prefixes into memory  
- Extracts and loads CSVs inside the zip files without saving them locally  
- Compares CSV files based on defined primary key columns and selected fields  
- Generates a clean, human-readable HTML report with discrepancies, stats, and insights

Use this tool for validating data pipelines, comparing environments, or spotting data drift over time.

---

## 🛠️ Prerequisites

Make sure Python 3.6+ is installed.

Install dependencies using `requirements.txt`:

```bash
pip install -r requirements.txt
```

---

## ⚙️ Configuration

Create a `config.ini` file in the root directory.

### Example: `config.ini`

```ini
[settings]
project_name = CSV Comparator
project_logo = path/to/logo.png

[report]
output_dir = ./reports
output_file = comparison_report.html

[keys]
primary_key_columns = id,serial_number
columns = name,price,quantity

[aws]
bucket_name = your-bucket-name
source_1_prefix = source1/
source_2_prefix = source2/

[threading]
use_multithreading_reading = true
use_multithreading_comparision = true

[report_custom]
include_passed = true
include_missing_files = true
```

### 🔍 Explanation of Config Fields

- `primary_key_columns`: Comma-separated keys used to match rows between files
- `columns`: Specific columns to compare (optional). Leave blank to compare all
- `bucket_name`: Name of your S3 bucket
- `source_1_prefix` / `source_2_prefix`: Folder paths inside the bucket
- `use_multithreading_reading`: Load files in parallel (True/False)
- `use_multithreading_comparision`: Speed up comparison (True/False)
- `include_passed`: Show passing rows in the report
- `include_missing_files`: Highlight missing CSVs between sources

---

## ▶️ How to Run

Run the comparison using:

```bash
python run.py
```

The script will:

1. Read `.zip` files directly from S3 (no local downloads)
2. Extract CSVs and load them into memory
3. Normalize filenames by stripping timestamps
4. Compare matched files by key columns and selected fields
5. Generate an HTML report in your configured output directory

---

## 📂 Output

- A timestamped HTML report (e.g., `comparison_report_20250415_1530.html`)
- Includes:
  - Total files compared
  - Pass/fail percentages
  - Field-level mismatches
  - Missing/extra rows or columns
  - Missing files between S3 folders

---

## ✅ Example Console Output

```
---------------- CSV Comparison Started ----------------
Reading zip files from source1...
Reading zip files from source2...
Comparing 3 matched CSV files...
Generating report...
Report saved to ./reports/comparison_report_20250415_1530.html
---------------- CSV Comparison Finished ----------------
```

---

## 🧩 Troubleshooting

- **S3 Access Issues**: Make sure AWS credentials are properly configured (e.g., using `aws configure` or environment variables)
- **Missing Files**: Ensure both S3 prefixes contain comparable zip files
- **Primary Key Errors**: Double-check `primary_key_columns` — empty or incorrect keys can cause mismatches

---

## 🧱 Project Structure

```
project/
│
├── run.py              # Main script to execute comparison
├── config.ini          # Your configuration file
├── requirements.txt    # Python dependencies
├── csv_comparision.py  # Python source code
├── /reports            # Folder where reports will be saved
└── README.md           # You're here!
```

---

## 📄 License

This tool is provided as-is for internal or personal use. Feel free to fork, extend, or modify it as needed.
```

---

If you need the contents of `requirements.txt` to go with this, here's a simple version you can add:

### ✅ `requirements.txt`

```
boto3
pandas
tqdm
```

Let me know if you’d like to include `configparser` or anything else in it!
