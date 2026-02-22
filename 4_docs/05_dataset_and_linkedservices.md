# Datasets and Linked Services

## Overview
This section describes the datasets and linked services used in the ADF Bronze layer ingestion pipeline. All datasets are **parameterized** to support multiple tables and schemas dynamically.

## Datasets

### 1. Azure SQL Dataset
- **Purpose:** Source dataset to connect to Azure SQL Database.  
- **Parameters:**  
  - `schema` – database schema  
  - `table` – table name  
- **Linked Service:** `ls_sqldatabase`  
- **Format:** Tabular (SQL)

### 2. ADLS2 Parquet Dataset
- **Purpose:** Sink dataset for Bronze layer storage.  
- **Parameters:**  
  - `container` – e.g., `bronze`  
  - `folder` – e.g., `data_parquet`  
  - `file` – dynamically generated file name: `<table>_<current_utc>`  
- **Linked Service:** `ls_adls2`  
- **Format:** Parquet

### 3. JSON CDC Dataset
- **Purpose:** Store and read the last processed CDC value (`cdc.json`).  
- **Parameters:**  
  - `container` – `bronze`  
  - `folder` – `cdc`  
  - `file` – `cdc.json`  
- **Linked Service:** `ls_adls2`  
- **Format:** JSON

## Linked Services

### 1. ls_sqldatabase
- **Type:** Azure SQL Database  
- **Purpose:** Connect to source SQL server  
- **Authentication:** SQL Auth or Managed Identity  
- **Supports:** Incremental, Backfill, Full load

### 2. ls_adls2
- **Type:** Azure Data Lake Storage Gen2  
- **Purpose:** Sink for Bronze layer and CDC JSON  
- **Authentication:** Managed Identity / Service Principal  
- **Supports:** Parquet and JSON formats
