## How to find blocking session

```sql
SELECT s.sid, S.BLOCKING_SESSION, S.FINAL_BLOCKING_SESSION
  FROM v$session s 
 WHERE s.blocking_session IS NULL AND
 exists( select 1 from v$session s2 where S2.BLOCKING_SESSION = S.SID );
```

## Kill session

```sql
select sid,serial# from v$session sid in (...) --查询产生阻塞用户的 sid,serial#,然后

alter system kill session(sid,serial#); --杀掉产生阻塞的用户
```


  下面内容为copy别人的
 
  空闲之际，写了个sql语句，解决当前工作中经常遇到的锁定和被锁定表得信息，欢迎大家使用，指正。
  如何快捷的显示当前锁定和被锁定表的用户，sql等信息
  如果是dba用户那么可以使用
  
  1. 阻塞和等待的用户及sql语句 
  ```sql
  select 'blocker('||wb.holding_session||':'||sb.username||')-sql:'||qb.sql_text blockers,sb.machine,sb.terminal,
  'waiter ('||wb.waiting_session||':'||sw.username||')-sql:'||qw.sql_text waiters ,sw.machine,sb.terminal
  from dba_waiters wb,
  v$session sb,
  v$session sw,
  v$sqlarea qb,
  v$sqlarea qw
  where wb.holding_session=sb.sid
  and wb.waiting_session=sw.sid
  and sb.prev_sql_addr=qb.address
  and sw.sql_address=qw.address
  and wb.mode_held<>'None' 
```
  2. 如果不是dba那么就不能使用dba_waiters表了，可以使用v$lock来替换

```sql
  select 'blocker('||lb.sid||':'||sb.username||')-sql:'|| qb.sql_text blockers,sb.machine,sb.terminal,sb.logon_time,
  'waiter ('||lw.sid||':'||sw.username||')-sql:'|| qw.sql_text waiters ,sw.machine,sw.terminal,sw.logon_time
  from v$lock lb, 
  v$lock lw,
  v$session sb, 
  v$session sw,
  v$sql qb, 
  v$sql qw
  where lb.sid=sb.sid
  and lw.sid=sw.sid
  and sb.prev_sql_addr=qb.address
  and sw.sql_address=qw.address
  and lb.id1=lw.id1
  and sw.lockwait is not null
  and sb.lockwait is null
  and lb.block=1 
 ```

