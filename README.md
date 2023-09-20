proc sql;
    create table combined_checks as 
    select Step, 'Value'n, 'Check'n from dd_01
    union all
    select Step, 'Value'n, 'Check'n from dd_02
    union all
    select Step, 'Value'n, 'Check'n from dd_03
    union all
    /* ... continue with the other dd_XX tables ... */
    select Step, 'Value'n, 'Check'n from dd_13;
quit;

filename outbox email to="your_email_address@example.com"
                    subject="Combined Checks"
                    from="sender_email_address@example.com"
                    attach=("path_to_store_combined_checks_table.csv" type="text/plain");

data _null_;
    set combined_checks;
    file outbox;
    put Step $20. +3 'Value'n $10. +3 'Check'n $10.;
run;
