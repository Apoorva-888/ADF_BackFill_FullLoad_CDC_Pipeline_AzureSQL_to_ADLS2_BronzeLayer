# Project Overview

**Project Name:** ADF Backfill & Full Load Pipeline AzureSQL → ADLS2 Bronze

**Description:**  
This project implements a metadata-driven, safe historical and full table ingestion pattern using Azure Data Factory (ADF). Data is ingested from Azure SQL Database into ADLS2 Bronze layer in **Parquet format**, supporting **Backfill** (specific historical date ranges) and **Full Load** (entire table reload) modes. Unlike incremental pipelines, the CDC watermark is **not updated**, ensuring safe reprocessing of data without affecting incremental loads.

**Key Features:**  
- Backfill load: supports reprocessing specific historical ranges via parameters (`backfill_start`, `backfill_end`).  
- Full Load: reloads the entire table for rebuild or onboarding scenarios.  
- Parameterized pipeline: supports multiple tables and schemas.  
- Safe for production: does not overwrite incremental CDC watermark.  
- Bronze layer storage in **Parquet format** for analytics efficiency.  
- Metadata-driven configuration and dynamic SQL generation.  

**Folder Structure:**  
- `pipelines/` – ADF pipeline JSONs for Backfill and Full Load  
- `datasets/` – Source, sink, and parameterized datasets  
- `linked_services/` – Azure SQL & ADLS2 linked services  
- `cdc_tracking/` – CDC JSON file (not modified for Backfill or Full Load)  
- `templates/` – ARM templates for deployment  
- `scripts/` – Helper scripts for validation or testing  
- `docs/` – Documentation, execution scenarios, and architecture diagrams  
