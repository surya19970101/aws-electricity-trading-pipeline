# End-to-End AWS Data Engineering Project: US Electricity Market Analysis

## Project Overview

This project is a complete, end-to-end data engineering pipeline built on Amazon Web Services (AWS) to analyze the U.S. wholesale electricity market. The goal is to extract public trading data from the seven Independent System Operators (ISOs) and Regional Transmission Organizations (RTOs) in the United States, transform it into an analytics-ready format, and load it into a data warehouse.

The final dataset is designed for a trader who needs to analyze electricity prices, demand, and volatility to make informed investment decisions. The project showcases a modern, scalable, and automated data architecture.

## Key Features

*   **Multi-Source Data Ingestion**: Extracts data from all 7 U.S. ISOs/RTOs.
*   **Scalable ETL Processing**: Uses PySpark and AWS Glue to perform complex transformations, feature engineering, and data enrichment.
*   **Enriched Datasets**: Integrates market data with electricity outage data to provide deeper context for price fluctuations.
*   **Cloud-Native Architecture**: Fully built on serverless and managed AWS services for scalability, reliability, and cost-efficiency.
*   **Automated & Orchestrated**: The entire pipeline is automated and scheduled using AWS Step Functions, ensuring fresh data is delivered consistently.
*   **Analytics-Ready Data Warehouse**: The final data is structured in Amazon Redshift, optimized for complex analytical queries and BI dashboards.

## Architecture Diagram

*To be created*

```
[S3 Data Lake] <- (Raw Data) - [AWS Lambda] <- [ISO/RTO APIs]
       |
       | (Transformed Data)
       V
[AWS Glue (PySpark)] -> [Amazon Redshift Warehouse] -> [BI Dashboard (QuickSight/Tableau)]
       ^
       | (Orchestration)
[AWS Step Functions]
```

## Data Sources

*   **Market Data**: Public data portals of the 7 U.S. ISOs/RTOs:
    *   CAISO, ERCOT, ISO-NE, MISO, NYISO, PJM, SPP
*   **Outage Data**: Publicly available datasets from sources like PowerOutage.us and the U.S. Department of Energy.

## Technology Stack

*   **Cloud**: AWS
*   **Data Lake**: Amazon S3
*   **Data Extraction**: AWS Lambda, Python
*   **Data Transformation**: AWS Glue, PySpark
*   **Data Warehouse**: Amazon Redshift
*   **Orchestration**: AWS Step Functions
*   **Infrastructure as Code**: AWS CloudFormation / Terraform
*   **Version Control**: Git / GitHub

## How to Run

*(This section will be filled in as you develop the project)*

1.  **Prerequisites**: AWS CLI, Python 3.9+, Terraform/CloudFormation installed.
2.  **Deploy Infrastructure**: `terraform apply`
3.  **Trigger Pipeline**: Manually trigger the Step Functions workflow via the AWS Console or wait for the scheduled run.

---
```

***

### Detailed Project Plan and Phases

Here is a strategic, phase-by-phase plan to guide your project from start to finish.

#### Phase 1: Setup and Initial Data Extraction (ISO-NE)
*   **Goal**: Establish the project foundation and build the extraction pipeline for a single ISO.
*   **Details**:
    1.  **Project Setup**: Create a GitHub repository and initialize it with the `README.md` file. Set up your AWS account and configure your local development environment (AWS CLI, Python).
    2.  **Infrastructure as Code**: Using Terraform or CloudFormation, define your first S3 bucket (for raw data) and an IAM role with the necessary permissions.
    3.  **Extraction Script**: Write a Python script to fetch key data (e.g., real-time energy prices, demand) from the ISO New England (ISO-NE) public data portal.
    4.  **Lambda Function**: Package the script into an AWS Lambda function. Configure it to run on a schedule (e.g., every 15 minutes) and save the raw JSON or CSV data into your S3 "raw" layer.

#### Phase 2: Transformation with PySpark and AWS Glue (ISO-NE)
*   **Goal**: Process the raw data from Phase 1 and create a clean, structured dataset.
*   **Details**:
    1.  **Glue Job Setup**: Create an AWS Glue ETL job. Choose PySpark as the engine.
    2.  **PySpark Script**: Write a PySpark script that:
        *   Reads the raw ISO-NE data from the "raw" S3 bucket.
        *   Performs the transformations discussed earlier: standardizes timestamps, cleans data, and engineers features like rolling averages and lag features.
        *   Writes the transformed data back to a different S3 path (e.g., a "processed" or "transformed" layer) in an efficient file format like Parquet.
    3.  **Testing**: Thoroughly test your PySpark script locally (if possible) and within the AWS Glue environment to ensure it runs correctly.

#### Phase 3: Data Warehousing and Orchestration (ISO-NE)
*   **Goal**: Load the transformed data into a data warehouse and automate the full pipeline.
*   **Details**:
    1.  **Redshift Cluster**: Use Infrastructure as Code to deploy a small Amazon Redshift cluster. Define the table schema for your cleaned electricity data.
    2.  **Load Data**: Add a step to your Glue job (or a new job) that uses the `COPY` command to efficiently load the transformed Parquet data from S3 into your Redshift table.
    3.  **Orchestration**: Create an AWS Step Functions workflow that chains these steps together:
        *   Trigger the Lambda extraction function.
        *   On success, start the AWS Glue transformation job.
        *   On success, run the Redshift `COPY` command.
        *   Include error handling and notifications (e.g., using Amazon SNS).

#### Phase 4: Scaling to All Seven ISOs
*   **Goal**: Expand the pipeline to ingest and process data from the remaining six ISOs.
*   **Details**:
    1.  **Refactor for Scalability**: Modify your Lambda and PySpark scripts to be configurable. They should be able to handle different data formats and endpoints by passing parameters (e.g., the name of the ISO).
    2.  **Deploy for Each ISO**: Create parallel extraction and transformation workflows for each of the other six ISOs. You can reuse most of your code and infrastructure definitions.
    3.  **Union Data**: In your final loading step, ensure that data from all seven ISOs is combined into a single, unified table in Redshift, with a column identifying the source ISO.

#### Phase 5: Integrating Outage and Weather Data
*   **Goal**: Enrich the market data with external datasets to add analytical depth.
*   **Details**:
    1.  **New Extraction Pipeline**: Build a separate extraction pipeline (Lambda + S3) to fetch outage data and (optionally) weather data for the relevant regions.
    2.  **Enrichment in Glue**: Add a new stage to your PySpark job that joins the market data with the outage and weather data based on timestamp and region.
    3.  **Update Warehouse Schema**: Add new columns to your Redshift table to store this enriched information (e.g., `outage_megawatts`, `temperature_celsius`).

#### Phase 6: Analytics and Visualization
*   **Goal**: Demonstrate the value of the final dataset by building a simple dashboard.
*   **Details**:
    1.  **Connect a BI Tool**: Connect Amazon QuickSight (or another BI tool) to your Redshift data warehouse.
    2.  **Build a Dashboard**: Create a simple dashboard for your "trader" persona with visualizations for:
        *   Real-time electricity prices across all 7 ISOs.
        *   Price volatility over time.
        *   Demand vs. price for a specific region.
        *   Price spreads between two neighboring ISOs.

#### Phase 7: Finalization and Documentation
*   **Goal**: Polish the project for your portfolio.
*   **Details**:
    1.  **Clean Up Code**: Refactor and comment your code for clarity.
    2.  **Finalize `README.md`**: Update the `README.md` with detailed instructions, the final architecture diagram, and screenshots of your BI dashboard.
    3.  **Write a Blog Post**: Consider writing a short article on Medium or your personal blog explaining the project. This is a great way to showcase your expertise to recruiters.
