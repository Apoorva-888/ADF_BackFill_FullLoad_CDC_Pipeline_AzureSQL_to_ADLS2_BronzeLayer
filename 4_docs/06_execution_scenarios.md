# Execution Scenarios

## Overview
The pipeline supports three load scenarios: Incremental, Backfill, and Full Load. Each scenario uses the same pipeline but behaves differently based on parameters.

## Scenario 1 – Incremental Load
- **Parameters:**  
  - `load_type = incremental`  
  - Uses `cdc.json` for last processed value  
- **Behavior:**  
  - Copies only new/updated rows  
  - Updates `cdc.json` after successful load  
  - Output folder: `bronze/data_parquet/<table>_<current_utc>/`  
- **Use Case:** Daily or hourly ingestion of new data

## Scenario 2 – Backfill Load
- **Parameters:**  
  - `load_type = backfill`  
  - `backfill_start` / `backfill_end` – historical range  
- **Behavior:**  
  - Loads data within the specified range  
  - Does not update `cdc.json`  
- **Use Case:** Recover historical data or late-arriving records

## Scenario 3 – Full Load
- **Parameters:**  
  - `load_type = full`  
- **Behavior:**  
  - Loads entire table  
  - Does not update `cdc.json`  
- **Use Case:** Initial table onboarding or rebuild
