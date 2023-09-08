# First Query
spark.sql("""
CREATE OR REPLACE TEMPORARY VIEW custmr_nbr_details AS
SELECT DISTINCT A.PARTIAL_ACCT_NBR, 
                C.actual_collect_unit_id,
                D.CUSTMR_NBR, 
                E.CUSTMR_NBR AS CO_CUSTMR_NBR, 
                D.FIRST_NM, 
                D.LAST_NM
FROM SVCNG.ACCT_STATUS_FACT A
JOIN SVCNG.DAY_DIM B
  ON A.DAY_KEY = B.DAY_KEY
  AND B.day_key = 'day_key_after_value' -- replace 'day_key_after_value' with the actual value or a variable
JOIN SVCNG.ACCT_DIM C
  ON A.ACCT_KEY = C.ACCT_KEY
  AND C.PORTFLO_TYPE_CD IN ('RS','RD')
LEFT JOIN SVCNG.CUSTMR_DIM D
  ON A.BORR_CUSTMR_KEY = D.CUSTMR_KEY
LEFT JOIN SVCNG.CUSTMR_DIM E
  ON A.C_CUSTMR_KEY = E.CUSTMR_KEY
""")

# Second Query
spark.sql("""
CREATE OR REPLACE TEMPORARY VIEW custmr_nbr AS
SELECT partial_acct_nbr, CUSTMR_NBR 
FROM custmr_nbr_details 
WHERE CUSTMR_NBR NOT IN ("", "UNKNOWN")
UNION
SELECT partial_acct_nbr, CO_CUSTMR_NBR AS CUSTMR_NBR 
FROM custmr_nbr_details 
WHERE CO_CUSTMR_NBR NOT IN ("", "UNKNOWN")
""")
