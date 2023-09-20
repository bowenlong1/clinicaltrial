proc sql;
create table dd_01 as
select 'Final Records' as Step,count(distinct acct_nbr) as 'Value'n format comma12.,
       case when calculated 'Value'n ^= (select count(*) from pop3) or calculated 'Value'n<=0  then 'ERROR!!!!' else 'PASS' end as 'Check'n 
from samp3;
quit;

/***check2***/
proc sql;
create table dd_02 as select
'Missing RepeatDQ' as Step,
nmiss(freq_545_fnl)/count(*) as 'Value'n format percent8.2,
 case when calculated 'Value'n >0.15 then 'ERROR!!!!' else 'PASS' end as 'Check'n
 from samp3;
 quit;
proc sql;
create table dd_03 as select
'Missing DPD_6_MNTH_MAX' as Step,
nmiss(DPD_6_MNTH_MAX)/count(*) as 'Value'n format percent8.2,
 case when calculated 'Value'n >0.1 then 'ERROR!!!!' else 'PASS' end as 'Check'n
 from samp3;
 quit;
proc sql;
create table dd_04 as select
'Missing DPD_5_MNTH_MAX' as Step,
nmiss(DPD_5_MNTH_MAX)/count(*) as 'Value'n format percent8.2,
case when calculated 'Value'n >0.1 then 'ERROR!!!!' else 'PASS' end as 'Check'n
from samp3;
quit;
proc sql;
create table dd_05 as select
'Missing PRECSN_SCORE' as Step,
nmiss(PRECSN_SCORE)/count(*) as 'Value'n format percent8.2,
case when calculated 'Value'n >0.05 then 'ERROR!!!!' else 'PASS' end as 'Check'n
from samp3;
quit;

proc sql;
create table dd_06 as select
'Missing EQUITY' as Step,
nmiss(EQUITY)/count(*) as 'Value'n format percent8.2,
case when calculated 'Value'n >0.05 then 'ERROR!!!!' else 'PASS' end as 'Check'n
from samp3;
quit;

proc sql;
create table dd_07 as select
'Missing PTI' as Step,
nmiss(PTI)/count(*) as 'Value'n format percent8.2,
case when calculated 'Value'n >0.01 then 'ERROR!!!!' else 'PASS' end as 'Check'n
from samp3;
quit;

proc sql;
create table dd_08 as select
'Missing DPD_5WEEK' as Step,
nmiss(DPD_5WEEK)/count(*) as 'Value'n format percent8.2,
case when calculated 'Value'n >0.01 then 'ERROR!!!!' else 'PASS' end as 'Check'n
from samp3;
quit;

proc sql;
create table dd_09 as select
'Missing DPD_2_MNTH_MAX' as Step,
nmiss(DPD_2_MNTH_MAX)/count(*) as 'Value'n format percent8.2,
case when calculated 'Value'n >0.01 then 'ERROR!!!!' else 'PASS' end as 'Check'n
from samp3;
quit;

proc sql;
create table dd_10 as select
'Missing DPD_3WEEK' as Step,
nmiss(DPD_3WEEK)/count(*) as 'Value'n format percent8.2,
case when calculated 'Value'n >0.01 then 'ERROR!!!!' else 'PASS' end as 'Check'n
from samp3;
quit;

proc sql;
create table dd_11 as select
'Missing DPD_7WEEK' as Step,
nmiss(DPD_7WEEK)/count(*) as 'Value'n format percent8.2,
case when calculated 'Value'n >0.01 then 'ERROR!!!!' else 'PASS' end as 'Check'n
from samp3;
quit;

proc sql;
create table dd_12 as select
'Missing DIF_CURDPD_MAXDPD' as Step,
nmiss(DIF_CURDPD_MAXDPD)/count(*) as 'Value'n format percent8.2,
case when calculated 'Value'n >0.01 then 'ERROR!!!!' else 'PASS' end as 'Check'n
from samp3;
quit;

proc sql; create table missings as select 
nmiss(ACCT_NBR)/count(ACCT_NBR) as miss_ACCT_NBR, 
nmiss(LOAN_ACCT_NBR)/count(LOAN_ACCT_NBR) as miss_LOAN_ACCT_NBR,
nmiss(ACCT_NBR)/count(*) as miss_ACCT_NBR,
nmiss(LOAN_ACCT_NBR)/count(*) as miss_LOAN_ACCT_NBR,
nmiss(DAY_KEY)/count(*) as miss_DAY_KEY,
nmiss(DAYS_TO_NEXT_PMT)/count(*) as miss_DAYS_TO_NEXT_PMT,
nmiss(TOTAL_PMT_3M)/count(*) as miss_TOTAL_PMT_3M,
nmiss(PAST_DUE_AMT)/count(*) as miss_PAST_DUE_AMT,
nmiss(DAYS_SINCE_LAST_EXT)/count(*) as miss_DAYS_SINCE_LAST_EXT,
nmiss(DAYS_SINCE_PTP)/count(*) as miss_DAYS_SINCE_PTP,
nmiss(DAYS_IN_CALL_QUEUE_QTY)/count(*) as miss_DAYS_IN_CALL_QUEUE_QTY,
nmiss(DAYS_SINCE_LAST_CONTACT)/count(*) as miss_DAYS_SINCE_LAST_CONTACT,
nmiss(LAST_PMT_AMT)/count(*) as miss_LAST_PMT_AMT,
nmiss(TOTAL_PMT_1WEEK)/count(*) as miss_TOTAL_PMT_1WEEK,
nmiss(FINC_AMT)/count(*) as miss_FINC_AMT,
nmiss(CUST_STATE_EN)/count(*) as miss_CUST_STATE_EN,
nmiss(CUR_APR)/count(*) as miss_CUR_APR,
nmiss(TOTAL_PMT_1M)/count(*) as miss_TOTAL_PMT_1M,
nmiss(CUR_MNTH_BILL)/count(*) as miss_CUR_MNTH_BILL,
nmiss(DPD)/count(*) as miss_DPD
from samp3; 
quit;
proc sql;
create table dd_13 as select
'Other Missing Variables' as Step,
max(miss_ACCT_NBR,
miss_LOAN_ACCT_NBR,
miss_DAY_KEY,
miss_DAYS_TO_NEXT_PMT,
miss_TOTAL_PMT_3M,
miss_PAST_DUE_AMT,
miss_DAYS_SINCE_LAST_EXT,
miss_DAYS_SINCE_PTP,
miss_DAYS_IN_CALL_QUEUE_QTY,
miss_DAYS_SINCE_LAST_CONTACT,
miss_LAST_PMT_AMT,
miss_TOTAL_PMT_1WEEK,
miss_FINC_AMT,
miss_CUST_STATE_EN,
miss_CUR_APR,
miss_TOTAL_PMT_1M,
miss_CUR_MNTH_BILL,
miss_DPD) as 'Value'n format percent8.2,
case when calculated 'Value'n >0 then 'ERROR!!!!' else 'PASS' end as 'Check'n
from missings;
quit;

