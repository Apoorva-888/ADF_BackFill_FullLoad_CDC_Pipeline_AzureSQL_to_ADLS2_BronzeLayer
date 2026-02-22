# Code Snippets

## Dynamic SQL for Incremental / Backfill / Full Load

```sql
@concat(
  'SELECT * FROM ',
  pipeline().parameters.schema,
  '.',
  pipeline().parameters.table,
  ' WHERE ',
  if(
    equals(pipeline().parameters.load_type,'full'),
    '1=1',
    if(
      equals(pipeline().parameters.load_type,'backfill'),
      concat(
        pipeline().parameters.cdc_column,
        ' BETWEEN ''',
        pipeline().parameters.backfill_start,
        ''' AND ''',
        pipeline().parameters.backfill_end,
        ''''
      ),
      concat(
        pipeline().parameters.cdc_column,
        ' > ''',
        activity('lookup_cdc_json').output.value[0].cdc,
        ''' AND ',
        pipeline().parameters.cdc_column,
        ' <= ''',
        variables('set_current_value_utc'),
        ''''
      )
    )
  )
)
