[Click on me to see raw source](https://samadhandba.wordpress.com/2011/03/15/finding-currently-running-sql/)

## Here is a small script to determine what sql is executing

```sql
select sesion.sid,
       sesion.username,
       optimizer_mode,
       hash_value,
       address,
       cpu_time,
       elapsed_time,
       sql_text
       --sql_fulltext
  from v$sql sql, v$session sesion
 where sesion.sql_hash_value = sql.hash_value
   and sesion.sql_address    = sql.address
   and sesion.username is not null 
```
## IO being done by active sessions

```sql
select sess_io.sid,
       sess_io.block_gets,
       sess_io.consistent_gets,
       sess_io.physical_reads,
       sess_io.block_changes,
       sess_io.consistent_changes
  from v$sess_io sess_io, v$session sesion
 where sesion.sid = sess_io.sid
   and sesion.username is not null 
```

