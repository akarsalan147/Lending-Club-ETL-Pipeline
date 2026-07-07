# Lending Club ETL Pipeline

## Project Overview

This project is an end-to-end ETL pipeline developed using Apache Spark (PySpark) to process the Lending Club loan dataset.

The pipeline ingests a large raw CSV dataset containing customer, loan, repayment, and default information. The raw dataset is divided into multiple logical datasets based on business requirements. Each dataset is then cleaned, transformed, validated, and stored in both CSV and Parquet formats for downstream analytics.

The project demonstrates various Data Engineering concepts including:

- Dataset normalization
- Schema enforcement
- Data cleaning
- Data transformation
- Data validation
- Null handling
- Duplicate removal
- Spark SQL
- PySpark DataFrame API

---

# Project Workflow

```
                     Raw Lending Club Dataset
                               │
                               ▼
                     Dataset Creation Layer
                               │
       ┌──────────────┬───────────────┬────────────────┬────────────────┐
       │              │               │                │
       ▼              ▼               ▼                ▼
 Customers Data    Loans Data   Loan Repayments   Loan Defaulters
       │              │               │                │
       └──────────────┴───────────────┴────────────────┘
                               │
                               ▼
                Cleaning & Transformation Layer
                               │
       ┌──────────────┬───────────────┬────────────────┬────────────────┐
       │              │               │                │
       ▼              ▼               ▼                ▼
 Customers      Loans Cleaning   Repayment      Defaulters
 Cleaning                         Cleaning        Cleaning
                               │
                               ▼
                  Cleaned CSV & Parquet Files
```

---

# Input Dataset

The input consists of a single raw Lending Club CSV file containing information related to borrowers, loans, repayments, and defaulters.

---

# Dataset Creation

The raw dataset is divided into four business datasets.

## 1. Customers Dataset

Contains borrower-related information.

Columns selected:

- member_id
- emp_title
- emp_length
- home_ownership
- annual_inc
- addr_state
- zip_code
- country
- grade
- sub_grade
- verification_status
- tot_hi_cred_lim
- application_type
- annual_inc_joint
- verification_status_joint

### Unique Customer ID Generation

The original dataset contains null values in the member_id column.

Therefore, a new unique identifier called **emp_id** is generated using SHA-256 hashing over the following columns:

- emp_title
- emp_length
- home_ownership
- annual_inc
- zip_code
- addr_state
- grade
- sub_grade
- verification_status

This generated hash is used as the customer identifier throughout the project.

---

## 2. Loans Dataset

Contains loan information.

Columns selected:

- loan_id
- member_id
- loan_amount
- funded_amount
- term
- interest_rate
- installment
- issue_date
- loan_status
- loan_purpose
- loan_title

---

## 3. Loan Repayments Dataset

Contains repayment history.

Columns selected:

- loan_id
- total_principal_received
- total_interest_received
- total_late_fee_received
- total_payment_received
- last_payment_amount
- last_payment_date
- next_payment_date

---

## 4. Loan Defaulters Dataset

Contains customer delinquency and public record information.

Columns selected:

- member_id
- delinq_2yrs
- delinq_amnt
- pub_rec
- pub_rec_bankruptcies
- inq_last_6mths
- total_rec_late_fee
- mths_since_last_delinq
- mths_since_last_record

---

# Data Cleaning

Each dataset is cleaned independently before storing the final output.

---

## Customers Data Cleaning

The following operations are performed:

- Define explicit schema
- Rename columns
- Add ingest_date timestamp
- Remove invalid records
- Convert employment length to integer
- Replace missing employment length with average value
- Standardize state codes
- Store cleaned data in CSV and Parquet

---

## Loans Data Cleaning

The following operations are performed:

- Define explicit schema
- Rename columns
- Add ingest_date timestamp
- Remove records with mandatory null values
- Convert loan term from months to years
- Standardize loan purpose values
- Store cleaned data in CSV and Parquet

---

## Loan Repayments Cleaning

The following operations are performed:

- Define explicit schema
- Rename columns
- Add ingest_date timestamp
- Remove incomplete repayment records
- Correct total payment amount using business logic
- Remove zero-payment records
- Replace invalid payment dates with null
- Store cleaned data in CSV and Parquet

---

## Loan Defaulters Cleaning

The following operations are performed:

- Define explicit schema
- Convert delinquency columns to numeric types
- Replace missing delinquency values with zero
- Split data into two separate business datasets:
  - Delinquency Details
  - Public Records & Enquiries
- Store cleaned data in CSV and Parquet

---

# Technologies Used

- Apache Spark
- PySpark
- Spark SQL
- DataFrame API
- CSV
- Parquet
- SHA-256 Hashing

---

# Output Structure

```
output/
│
├── customers/
│   ├── csv/
│   └── parquet/
│
├── loans/
│   ├── csv/
│   └── parquet/
│
├── loan_repayments/
│   ├── csv/
│   └── parquet/
│
└── loan_defaulters/
    ├── delinquency/
    ├── public_records/
    ├── csv/
    └── parquet/
```

---

# Business Rules

- Generate a unique customer identifier using SHA-256 hashing.
- Remove records containing mandatory null values.
- Convert employment experience into numeric values.
- Convert loan duration from months to years.
- Standardize loan purposes.
- Correct repayment amounts using business logic.
- Separate delinquency data from public record data.
- Store processed datasets in both CSV and Parquet formats.

---

# Conclusion

The ETL pipeline transforms a single raw Lending Club dataset into multiple domain-specific datasets through dataset creation, cleaning, validation, and transformation processes. The final output is stored in optimized formats suitable for reporting, analytics, and downstream data processing.
