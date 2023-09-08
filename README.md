proc sql;
create table custmr_nbr_details as

select distinct A.PARTIAL_ACCT_NBR, c.actual_collect_unit_id,

d.CUSTMR_NBR, e.CUSTMR_NBR as CO_CUSTMR_NBR, d.FIRST_NM, d.LAST_NM

FROM SVCNG.ACCT_STATUS_FACT A

            JOIN SVCNG.DAY_DIM B

                ON A.DAY_KEY = B.DAY_KEY

                    AND b.day_key = &day_key_after.

            JOIN SVCNG.ACCT_DIM C

                ON A.ACCT_KEY = C.ACCT_KEY

                    AND C.PORTFLO_TYPE_CD in ('RS','RD')

            left join SVCNG.CUSTMR_DIM d

                on a.BORR_CUSTMR_KEY = d.CUSTMR_KEY

            left join SVCNG.CUSTMR_DIM e

                on a.C_CUSTMR_KEY = e.CUSTMR_KEY;
quit;

proc sql;
create table custmr_nbr as select partial_acct_nbr, CUSTMR_NBR
from custmr_nbr_details
where CUSTMR_NBR not in ("", "UNKNOWN")
union
select partial_acct_nbr, CO_CUSTMR_NBR as CUSTMR_NBR
from custmr_nbr_details
where CO_CUSTMR_NBR not in ("", "UNKNOWN");
quit;
