# Pipeline Activities

**Pipeline Name:** p1_incremental_backfill_fullload_ingestion_bronze_withlogicapp__user001

**Activities Overview:**

1. **lookup_cdc_json**  
   - Type: Lookup  
   - Reads `cdc.json` from ADLS2 to get the last processed CDC value.  
   - Provides dynamic watermark for incremental loads.  
   - Ignored for Backfill and Full Load scenarios.

2. **current_utc**  
   - Type: SetVariable  
   - Stores current runtime UTC timestamp (`set_current_value_utc`).  
   - Used for dynamic folder/file naming and incremental load range.

3. **copy_azureSQL_to_adls2_p2**  
   - Type: Copy  
   - Source: Azure SQL Database (dynamic schema & table).  
   - Sink: ADLS2 Bronze layer in Parquet format.  
   - SQL dynamically switches based on `load_type`:  
     - **Incremental:** `cdc_column > last CDC AND <= current UTC`  
     - **Backfill:** `cdc_column BETWEEN backfill_start AND backfill_end`  
     - **Full Load:** `1=1` (entire table)  
   - Output stored in: `bronze/data_parquet/<table>_<current_utc>/`

4. **max_cdc**  
   - Type: Script  
   - Calculates `MAX(cdc_column)` from the source table after the current run.  
   - Used to update `cdc.json` only for incremental runs.

5. **update_last_cdc_value**  
   - Type: Copy  
   - Updates `cdc.json` in ADLS2 with the latest CDC value.  
   - Runs **only if load_type = incremental**.  
   - Backfill and Full Load do **not** overwrite `cdc.json`.  
   - Source provides `cdc = @activity('max_cdc').output.resultSets[0].rows[0].cdc`.  
   - Sink overwrites `bronze/cdc/cdc.json`.

6. **webactivity_logicapp**  
   - Type: WebActivity  
   - Triggered if `update_last_cdc_value` fails or is skipped.  
   - Sends pipeline info (name, runId) to a Logic App endpoint for alerting or custom handling.

**Parameters:**
- `schema` (string) – Database schema name  
- `table` (string) – Table name  
- `cdc_column` (string) – Column used for incremental load  
- `load_type` (string) – `incremental` / `backfill` / `full`  
- `backfill_start` (string) – Start date for backfill (optional)  
- `backfill_end` (string) – End date for backfill (optional)

**Variables:**
- `set_current_value_utc` (String) – Stores current UTC timestamp for dynamic filtering and file naming.
