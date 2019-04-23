SELECT s.sid, S.BLOCKING_SESSION, S.FINAL_BLOCKING_SESSION
  FROM v$session s 
 WHERE s.blocking_session IS NULL AND
 exists( select 1 from v$session s2 where S2.BLOCKING_SESSION = S.SID );
 
下面内容为copy别人的
 
空闲之际，写了个sql语句，解决当前工作中经常遇到的锁定和被锁定表得信息，欢迎大家使用，指正。
如何快捷的显示当前锁定和被锁定表的用户，sql等信息
如果是dba用户那么可以使用
一、
阻塞和等待的用户及sql语句 
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

二、如果不是dba那么就不能使用dba_waiters表了，可以使用v$lock来替换

select 'blocker('||lb.sid||':'||sb.username||')-sql:'|| qb.sql_text blockers,
'waiter ('||lw.sid||':'||sw.username||')-sql:'|| qw.sql_text waiters


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
如果向更详细的显示username的信息可以加入


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

三、--根据以上显示的信息可以知道当前产生锁和等待的用户信息
--那么就需要杀掉当前产生阻塞的用户，在执行杀掉用户进程前请咨询dba和该业务的同事，是否产生其他的不当影响

select sid,serial# from v$session sid in (...) --查询产生阻塞用户的 sid,serial#,然后
alter system kill session(sid,serial#); --杀掉产生阻塞的用户
