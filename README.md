proc sql;
create table email7d as select a.loan_acct_nbr, 
b.last_email_dt,
coalesce(case when &day_key_prior_7.<=input(put(last_email_dt,yymmddn8.),8.)<=&day_key_after. then 1 else 0 end, 0) as email_7day
from pop2 a left join AS_LAST_email b
on a.loan_acct_nbr = b.loan_acct_nbr;
quit;
