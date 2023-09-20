proc sql;
create table acct as select distinct loan_acct_nbr,dpd from pop;
quit;


PROC SQL;
CONNECT TO ORACLE (USER=&uid.     PW=&pwd. PATH='DW7PRD');
CREATE TABLE QUEUES_545_1 AS SELECT * FROM CONNECTION TO ORACLE 
(
SELECT B.PARTIAL_ACCT_NBR,

		CASE WHEN D.DEFLT_ACCT_STATUS_CD = 481 THEN null 
		     WHEN B.ACCT_STATUS_CD <>'AC'  THEN null ELSE Q.QUEUE_ID 
			 END AS QUEUE_ID, 
		CASE WHEN D.DEFLT_ACCT_STATUS_CD = 481  THEN 'Inactive'
		    WHEN B.ACCT_STATUS_CD <>'AC'  THEN 'Inactive'
		    WHEN Q.QUEUE_ID = 561 THEN 'UNASSIGNED'
		    WHEN SUBSTR(Q.QUEUE_NM,4,1) = 'M' 
			THEN SUBSTR(Q.QUEUE_NM,7,LENGTH(Q.QUEUE_NM) - 6) 
			ELSE SUBSTR(Q.QUEUE_NM,6,LENGTH(Q.QUEUE_NM) - 5) 
			END AS QUEUE_NM,
		D.PROMISE_PEND_IND,
		A.DAY_KEY,
		A.LAST_CONTACT_DAY_KEY,
		A.DAYS_IN_CALL_QUEUE_QTY,
		C.DT
FROM MART_ACCT_SVCNG.INTERACTN_ACCT_STATUS_FACT A 
	JOIN MART_ACCT_SVCNG.ACCT_DIM B ON A.ACCT_KEY = B.ACCT_KEY 
	JOIN MART_ACCT_SVCNG.DAY_DIM C ON A.DAY_KEY = C.DAY_KEY
	JOIN MART_ACCT_SVCNG.INTERACTN_ACCT_DIM D ON A.INTERACTN_ACCT_KEY = D.INTERACTN_ACCT_KEY 
	JOIN MART_ACCT_SVCNG.QUEUE_DIM Q ON A.CALL_QUEUE_KEY=Q.QUEUE_KEY
WHERE C.day_key = &day_key_prior.

);
DISCONNECT FROM ORACLE;
QUIT;

proc sql;
create table acct2 as select 
a.loan_acct_nbr, a.dpd as dpd1, b.actvty_cd, b.hold_ind,c.days_delinq_qty as dpd,
d.PROMISE_PEND_IND, d.QUEUE_ID
from acct a left join sar.acct_status b
on a.loan_acct_nbr = b.partial_acct_nbr
and b.day_key = &day_key_prior.
left join svcng.acct_status_fact c
on a.loan_acct_nbr = c.partial_acct_nbr
and c.day_key = &day_key_prior.
left join QUEUES_545_1 d
on a.loan_acct_nbr = d.partial_acct_nbr;
quit;

data acct3;
set acct2;
where hold_ind^='Y' and 46<=dpd<=60 and actvty_cd = 1 and PROMISE_PEND_IND^="Y" and QUEUE_ID not in (5243, 5240, 5205, 5202);
run;
