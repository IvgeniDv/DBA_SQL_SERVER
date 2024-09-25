SELECT DISTINCT 
  vs.volume_mount_point Drive
, vs.logical_volume_name
, vs.total_bytes/1024/1024/1024 CapacityGB
, vs.available_bytes/1024/1024/1024 FreeGB
, CAST(vs.available_bytes * 100. / vs.total_bytes AS DECIMAL(4,1)) FreePct 
FROM 
  sys.master_files mf
    CROSS APPLY 
  sys.dm_os_volume_stats(mf.database_id, mf.file_id) AS vs;