DBMS_STATS.GATHER_TABLE_STATS(
    'SH', 'SALES', method_opt => 'FOR COLUMNS (empno, deptno)'); 
    
    begin 
  	  	DBMS_STATS.GATHER_TABLE_STATS (
  	  	  ownname => '"INVENTORY"',
          tabname => '"COUNTRY"',
          estimate_percent => 1
          );
          end;
