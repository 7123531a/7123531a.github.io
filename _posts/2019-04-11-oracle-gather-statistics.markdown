exec DBMS_STATS.GATHER_TABLE_STATS('SH', 'SALES', method_opt => 'FOR COLUMNS (empno, deptno)'); 
    
begin 
    DBMS_STATS.GATHER_TABLE_STATS (
      ownname => '"INVENTORY"',
      tabname => '"COUNTRY"',
      estimate_percent => 1
      );
end;

 select table_name,num_rows,to_char(last_analyzed,'DD.MM.YYYY HH24:MI:SS') 
 from ALL_tables 
 where owner ='INVENTORY' and table_name='<table name>';


