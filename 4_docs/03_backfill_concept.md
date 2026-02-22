# Backfill Load Concept

**Purpose:**  
The Backfill load is used to process historical data for a specific range of dates. Unlike incremental loads, it does **not update the incremental CDC watermark**. This allows safe reprocessing of old data without affecting future incremental runs.

## Key Features
- Processes historical data for a given range: `backfill_start` → `backfill_end`.  
- Parameterized via pipeline parameters:  
  - `load_type = backfill`  
  - `backfill_start` – Start date/time  
  - `backfill_end` – End date/time  
- Does **not** overwrite `cdc.json`.  
- Ensures idempotent execution — multiple runs produce consistent results.  
- Supports multiple tables and schemas dynamically.  

## Backfill Flow
1. Lookup CDC JSON (optional) – output ignored.  
2. Set current UTC variable (optional).  
3. Copy Activity:  
   - SQL Query dynamically generates:  
     ```sql
     SELECT * FROM <schema>.<table>
     WHERE <cdc_column> BETWEEN 'backfill_start' AND 'backfill_end'
     ```
   - Sink: ADLS2 Bronze layer (Parquet)  
   - Output folder: `bronze/data_parquet/<table>_<current_utc>/`  
4. Skip updating `cdc.json` (maintains incremental integrity).  
5. Optional: Trigger WebActivity or alert if required.  

## When to Use Backfill
- Recover missing historical data.  
- Reprocess data for corrections or late-arriving records.  
- Onboard tables where historical data must be ingested for analytics.  

## Notes
- Fully parameterized — no code changes required for new tables.  
- Safe for production — does not interfere with incremental loads.  
- Parquet format ensures storage efficiency and schema evolution.
