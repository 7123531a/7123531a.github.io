    DECLARE 
        v_col_exists number := 0;
    begin
    select count(1) into v_col_exists from user_tab_cols where TABLE_NAME = UPPER('<table_nane>');

    if(v_col_exists = 0) then 
    execute immediate '';
    else 
    dbms_output.put_line('already there');
    end if;
    end;
    /

