CSV Comparison Tool
Overview
This tool compares two sets of CSV files from AWS S3 buckets, identifies discrepancies in the data, and generates a comprehensive HTML report summarizing the comparison results. The comparison includes checking for:

Missing columns in either file

Missing rows or extra rows in either file

Duplicate rows

Field-level discrepancies

The comparison process is optimized with multithreading for faster execution.

Prerequisites
Before running the tool, ensure you have the following installed:

Python 3.6+

boto3 (for AWS S3 integration)

pandas (for CSV data manipulation)

tqdm (for progress bars)

configparser (for configuration management)

To install the necessary dependencies, run:

bash
Copy
Edit
pip install boto3 pandas tqdm
Configuration
The tool reads configuration settings from a config.ini file. Here’s an example configuration:

Example config.ini
ini
Copy
Edit
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
Ensure to modify the paths and AWS configuration to match your setup.

Explanation of config.ini fields:
[settings]

project_name: Name of the project for the report.

project_logo: Path to the logo image for the report.

[report]

output_dir: Directory where the generated report will be saved.

output_file: Name of the output report file.

[keys]

primary_key_columns: A comma-separated list of columns that serve as the primary key for comparison.

columns: A comma-separated list of columns to compare. If None, all columns will be compared.

[aws]

bucket_name: The name of your AWS S3 bucket.

source_1_prefix & source_2_prefix: Prefixes for the two sources of CSV files in the S3 bucket.

[threading]

use_multithreading_reading: Enable multithreading for reading files from S3.

use_multithreading_comparision: Enable multithreading for comparing CSV files.

[report_custom]

include_passed: Include passed comparisons in the report.

include_missing_files: Include missing files in the report.

Running the Tool
To start the comparison process, run the run.py script. This script will:

Download CSV files from two S3 sources.

Compare the CSVs based on primary keys and selected columns.

Generate a detailed HTML report summarizing the differences.

Run the script by executing the following command:

bash
Copy
Edit
python run.py
Expected Output
The tool will output a report file (e.g., comparison_report.html) in the directory specified under output_dir.

The report will include:

A summary of the comparison results.

Detailed discrepancies, such as missing rows, extra rows, duplicate rows, and field-level mismatches.

An overall pass/fail percentage for the comparison.

Example Output
sql
Copy
Edit
---------------- CSV Comparison Started ----------------
Reading zip files from source1: 5 files processed
Reading zip files from source2: 5 files processed
Comparing CSV files: 4 files compared
Generating report...
---------------- CSV Comparison Finished ----------------
Customizing the Comparison
You can modify the columns to compare by editing the columns setting in the config.ini file.

You can change the primary key columns by modifying the primary_key_columns setting.

Troubleshooting
If you encounter any issues, check the following:

AWS credentials: Ensure that your AWS credentials are correctly configured (e.g., using aws configure).

Bucket access: Make sure the specified S3 bucket and files are accessible.

For detailed logs, you can add additional logging in the run.py file.

License
This tool is provided as-is. Feel free to modify and extend it according to your needs.
