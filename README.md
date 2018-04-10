# Useful SQL queries (PG)

## Indexes related

### General hit ratio

```
SELECT sum(heap_blks_read) as heap_read, sum(heap_blks_hit)  as heap_hit, (sum(heap_blks_hit) - sum(heap_blks_read)) / sum(heap_blks_hit) as ratio
FROM pg_statio_user_tables;
```
### Index utilization per table

```
SELECT relname, 100 * idx_scan / (seq_scan + idx_scan) percent_of_times_index_used, n_live_tup rows_in_table
FROM pg_stat_user_tables 
ORDER BY n_live_tup DESC;
```

## 20 slowest queries

```
SELECT  query,
round(total_time::numeric, 2) AS total_time,
calls,
round(mean_time::numeric, 2) AS mean,
round((100 * total_time /
sum(total_time::numeric) OVER ())::numeric, 2) AS percentage_cpu
FROM    pg_stat_statements
ORDER BY total_time DESC
LIMIT 20;
```

## Table stats (reads, writes, updates) for specified tables (relname)

```
select * from pg_stat_all_tables where relname IN ('users', 'trips', 'trip_templates', 'vehicles', 'invitations', 'service_areas', 'country_codes', 'user_privacy_options', 'privacy_options');
```

## Currently running queries (runtime longer than 500ms)

```
SELECT now() - query_start as "runtime", query, client_addr, client_port, client_hostname, query_start
FROM  pg_stat_activity
WHERE now() - query_start > '500 milliseconds'::interval
ORDER BY runtime DESC;
```
