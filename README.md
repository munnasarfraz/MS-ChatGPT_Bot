# CSV Comparison Tool

## 🚀 Overview
This tool compares two sets of CSV files stored in AWS S3 buckets. It:

- Downloads `.zip` files containing CSVs from two different S3 prefixes
- Extracts and reads the CSVs
- Compares CSV files based on defined primary key columns and selected fields
- Generates a clean, human-readable HTML report with discrepancies, stats, and insights

## 🛠️ Prerequisites

Make sure Python 3.6+ is installed. Then install dependencies:

```bash
pip install boto3 pandas tqdm
```

## ⚙️ Configuration

Create a `config.ini` file in the same directory as `run.py`.

### Example `config.ini`

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

### 🔍 Key Config Details

- `primary_key_columns`: Used to uniquely identify rows for comparison.
- `columns`: List of columns to compare. Leave blank to compare all.
- `use_multithreading_*`: Use threads to boost performance.
- `output_file`: Auto-appends a timestamp to prevent overwriting.

## ▶️ How to Run

Run the comparison by executing the following:

```bash
python run.py
```

This script will:
1. Download and unzip files from both sources in S3
2. Normalize filenames (removes timestamps)
3. Match files by name
4. Compare matched CSVs row-by-row and field-by-field
5. Generate a full HTML report

## 📂 Output

- HTML report is saved to the `output_dir` with timestamped filename
- Includes:
  - Summary stats per file
  - Missing or extra rows
  - Column-level mismatches
  - Pass/Fail percentages
  - Missing files between sources (optional)

## ✅ Example Output Log

```
---------------- CSV Comparison Started ----------------
Reading zip files from source1...
Reading zip files from source2...
Comparing 3 matched CSV files...
Generating report...
---------------- CSV Comparison Finished ----------------
```

## 🧩 Troubleshooting

- **AWS Access**: Make sure you’ve configured credentials with `aws configure` or via `~/.aws/credentials`.
- **File Matching**: Ensure files in both prefixes have consistent names (except for timestamps).
- **Config Errors**: Double-check your `config.ini` values if anything breaks.

## 📄 License

This project is open for internal or personal use. Modify and adapt as needed.
```

---

Let me know if you’d also like a starter `run.py` included in the repo layout!
