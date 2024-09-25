;WITH qs AS (
  SELECT TOP 10 
    total_worker_time/execution_count AvgCPU
  , total_elapsed_time/execution_count AvgDuration
  , (total_logical_reads + total_physical_reads)/execution_count AvgReads
  , execution_count
  , sql_handle
  , plan_handle
  , statement_start_offset
  , statement_end_offset
  FROM sys.dm_exec_query_stats
  WHERE execution_count > 5
    AND min_logical_reads > 100
    AND min_worker_time > 100
  ORDER BY (total_logical_reads + total_physical_reads)/execution_count DESC)
SELECT
  AvgCPU
, AvgDuration
, AvgReads
, execution_count
 ,SUBSTRING(st.TEXT, (qs.statement_start_offset/2) + 1, 
            ((CASE qs.statement_end_offset  
                WHEN -1 THEN DATALENGTH(st.TEXT)
                ELSE qs.statement_end_offset  
              END - qs.statement_start_offset)/2) + 1) StatementText
 ,query_plan ExecutionPlan
FROM 
  qs  
    CROSS APPLY
  sys.dm_exec_sql_text(qs.sql_handle) AS st  
    CROSS APPLY
  sys.dm_exec_query_plan (qs.plan_handle) AS qp 
ORDER BY 
  AvgDuration DESC;