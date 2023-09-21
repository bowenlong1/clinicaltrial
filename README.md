proc sql;
create table acct3 as select * from acct2 a
where a.hold_ind^='Y' and 46<=a.dpd<=60 and a.actvty_cd = 1 and a.PROMISE_PEND_IND^="Y" and a.QUEUE_ID not in (5243, 5240, 5205, 5202)
and a.loan_acct_nbr not in (select loan_acct_nbr from email_agent) and a.acct_nbr not in (select account_number in hist_tgt);
quit;
