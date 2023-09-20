proc sql;
    create table missings as
    select 
        'Missing Other Variables' as Step,
        nmiss(ACCT_NBR)/count(ACCT_NBR) as pct_miss_ACCT_NBR,
        nmiss(LOAN_ACCT_NBR)/count(LOAN_ACCT_NBR) as pct_miss_LOAN_ACCT_NBR,
        /*... repeat for all your variables ...*/
        nmiss(DPD)/count(DPD) as pct_miss_DPD,
        /* Determine the maximum missing percentage */
        calculated pct_miss_ACCT_NBR, calculated pct_miss_LOAN_ACCT_NBR, /*... repeat for all your variables ...*/, calculated pct_miss_DPD) as max_miss_pct,
        /* Check if there's any missing value */
        case 
            when calculated max_miss_pct > 0 then 'ERROR!!!!'
            else 'PASS' 
        end as Check
    from 
        samp3;
quit;

