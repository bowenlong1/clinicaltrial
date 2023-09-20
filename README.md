proc sql;
create table missings as select 
'Missing Other Variables' as Step,
max(
nmiss(ACCT_NBR)/count(*) as ACCT_NBR,
nmiss(LOAN_ACCT_NBR)/count(*) as LOAN_ACCT_NBR,
nmiss(DAY_KEY)/count(*) as DAY_KEY,
nmiss(DAYS_TO_NEXT_PMT)/count(*) as DAYS_TO_NEXT_PMT,
nmiss(TOTAL_PMT_3M)/count(*) as TOTAL_PMT_3M,
nmiss(PAST_DUE_AMT)/count(*) as PAST_DUE_AMT,
nmiss(DAYS_SINCE_LAST_EXT)/count(*) as DAYS_SINCE_LAST_EXT,
nmiss(DAYS_SINCE_PTP)/count(*) as DAYS_SINCE_PTP,
nmiss(DAYS_IN_CALL_QUEUE_QTY)/count(*) as DAYS_IN_CALL_QUEUE_QTY,
nmiss(DAYS_SINCE_LAST_CONTACT)/count(*) as DAYS_SINCE_LAST_CONTACT,
nmiss(LAST_PMT_AMT)/count(*) as LAST_PMT_AMT,
nmiss(TOTAL_PMT_1WEEK)/count(*) as TOTAL_PMT_1WEEK,
nmiss(FINC_AMT)/count(*) as FINC_AMT,
nmiss(CUST_STATE_EN)/count(*) as CUST_STATE_EN,
nmiss(CUR_APR)/count(*) as CUR_APR,
nmiss(TOTAL_PMT_1M)/count(*) as TOTAL_PMT_1M,
nmiss(CUR_MNTH_BILL)/count(*) as CUR_MNTH_BILL,
nmiss(DPD)/count(*) as DPD),
case when calculated 'Value'n >0 then 'ERROR!!!!' else 'PASS' end as 'Check'n
from samp3;
quit;
