proc sql; create table combined_checks as 
select Step, 'Value'n, 'Check'n from dd_01 union all 
select Step, 'Value'n, 'Check'n from dd_02 union all 
select Step, 'Value'n, 'Check'n from dd_03 union all 
select Step, 'Value'n, 'Check'n from dd_04 union all 
select Step, 'Value'n, 'Check'n from dd_05 union all 
select Step, 'Value'n, 'Check'n from dd_06 union all 
select Step, 'Value'n, 'Check'n from dd_07 union all 
select Step, 'Value'n, 'Check'n from dd_08 union all 
select Step, 'Value'n, 'Check'n from dd_09 union all 
select Step, 'Value'n, 'Check'n from dd_10 union all 
select Step, 'Value'n, 'Check'n from dd_11 union all 
select Step, 'Value'n, 'Check'n from dd_12 union all 
select Step, 'Value'n, 'Check'n from dd_13; 
quit;

filename mailtemp temp;

data null; set combined_checks; file mailtemp; put Step $30. +3 'Value'n $20. +3 'Check'n $10.; run;

filename sendmail email to="bowen.long@gmfinancial.com" subject="4660 Data Pipleine Finished" from="bowen.long@gmfinancial.com";

data null; file sendmail; infile mailtemp; input; put infile; run;

filename mailtemp clear;
