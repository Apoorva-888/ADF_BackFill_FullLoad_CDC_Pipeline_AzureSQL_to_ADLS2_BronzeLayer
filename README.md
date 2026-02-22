# ADF_BackFill_and_FullLoad_CDC_Pipeline_AzureSQL_to_ADLS2_BronzeLayer
Implements a metadata-driven, controlled historical reprocessing and full table ingestion pattern using Azure Data Factory (ADF) to copy data from Azure SQL Database into ADLS2.

## Overview
- This project implements metadata-driven Backfill and Full Load ingestion patterns using Azure Data Factory (ADF).
- The pipeline supports extracting either a specific historical date range (Backfill) or the entire table (Full Load) from Azure SQL Database and loading it into Azure Data Lake Storage Gen2 (ADLS2) Bronze layer.
- Unlike incremental pipelines, this framework does NOT modify the CDC watermark and is safe for reprocessing and full table reloads.

### Architecture
- Source: Azure SQL Database  
- Orchestration: Azure Data Factory  
- Target: ADLS Gen2 (Bronze Layer)  
- Format: Parquet  
- Load Strategy: Backfill (Date-Range Based) and Full Load (Complete Table Extraction)
### Key Features
- Metadata-driven configuration
- Dynamic query generation
- Parameterized datasets
- Controlled historical reprocessing
- Complete table reload capability
- Does not impact incremental CDC watermark tracking
- Optimized Parquet storage for analytics workloads
### Load Flows

### Backfill Load Flow
- Provide backfill_start and backfill_end parameters.
- Generate dynamic SQL using a BETWEEN condition.
- Extract only records within the specified date range.
- Write data to ADLS2 in Parquet format.
- Do not update the CDC watermark JSON file.
This ensures safe historical reprocessing without corrupting incremental logic.

### Full Load Flow
- Set load_type = full.
- Generate dynamic SQL without filtering conditions.
- Extract the entire table from the source.
- Write the full dataset to ADLS2 in Parquet format.
- Do not update the CDC watermark JSON file.
This enables complete dataset rebuilds while preserving incremental ingestion integrity

### Project Structure
```
ADF_Backfill_FullLoad_Pipeline_AzureSQL_to_ADLS2_Bronze/
├── 1_pipelines/
│   └── p1_backfill_full_ingestion_bronze.json
│
├── 2_datasets/
│   ├── dataset_azuresql.json
│   ├── dataset_json_dynamic_adls2.json
│   └── parquet_dynamic_adls2.json
│
├── 3_linked_services/
│   ├── ls_adls2.json
│   └── ls_sqldatabase.json
│
├── 4_docs/
│   ├── 01_project_overview.md
│   ├── 02_pipeline_activities.md
│   ├── 03_backfill_concept.md
│   ├── 04_full_load_concept.md
│   ├── 05_dataset_and_linkedservices.md
│   ├── 06_execution_scenarios.md
│   ├── 07_production_readiness.md
│   ├── 08_code_snippets.md
│
└── 5_activity_visuals/
    ├── backfill_copy_output.png
    ├── full_load_copy_output.png
    └── p1_backfill_full_ingestion_bronze.png
```

### Why This Approach?
- Enables controlled historical data reloads
- Supports complete dataset rebuild scenarios
- Prevents accidental CDC watermark corruption
- Scales across multiple tables
- Forms the foundation for a robust Bronze layer ingestion strategy

## Outcome
A production-ready Backfill and Full Load ingestion framework aligned with enterprise Azure data platform standards.
