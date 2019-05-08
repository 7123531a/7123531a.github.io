# 1. Check roles assigned to users
```sql
SELECT * FROM user_ROLE_PRIVS;
```
# 2. Check priviledges assigned to roles
```sql
select * from DBA_SYS_PRIVS;
select * from DBA_TAB_PRIVS;
select * from  role_sys_privs;
select * from  role_tab_privs;
select * from  role_role_privs;
```
