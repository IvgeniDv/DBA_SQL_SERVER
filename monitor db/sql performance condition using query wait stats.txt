SELECT
    wait_type,
    waiting_tasks_count,
    wait_time_ms,
    max_wait_time_ms,
    signal_wait_time_ms
FROM
    sys.dm_os_wait_stats
WHERE
    wait_time_ms > 0
    AND waiting_tasks_count > 0
ORDER BY
    wait_time_ms DESC