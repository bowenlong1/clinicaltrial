# Spark SQL translation
spark.sql("""
CREATE OR REPLACE TEMPORARY VIEW AS_LAST_email AS
SELECT loan_ACCT_NBR, DATE_FORMAT(LAST_email_DT, 'yyyy-MM-dd') AS LAST_email_DT
FROM (
    SELECT A.ACCT_NBR, 
           A.loan_ACCT_NBR, 
           CEF.contact_evnt_id,
           CASE 
               WHEN B.USR_ID = 'debatch' THEN 'DIALER'
               WHEN B.USR_ID LIKE 'IVR%' THEN 'IVR'
               WHEN B.USR_ID IS NULL THEN 'NO_ACTIVITY'
               ELSE 'AGENT' 
           END AS usr_type, 
           MAX(DATE(CEF.CONTACT_MEMO_TM)) AS LAST_email_DT
    FROM pop2 A
    JOIN SVCNG.CONTACT_EVNT_FACT CEF ON A.ACCT_NBR = CEF.ACCT_NBR
    JOIN SVCNG.CONTACT_EVNT_PROFL_DIM CEPD ON CEPD.CONTACT_EVNT_PROFL_KEY = CEF.CONTACT_EVNT_PROFL_KEY
    JOIN SVCNG.USR_DIM B ON CEF.CONTACT_by_usr_KEY = B.usr_KEY
    WHERE CEPD.CONTACT_METH_DESCR IN ('Outgoing Email')
    AND CEPD.CONTACT_WHO_DESCR IN ('Borrower', 'Co Borrower', 'Spouse')
    AND DATE(CEF.CONTACT_MEMO_TM) <= 'day_key_after_value'  -- replace 'day_key_after_value' with the actual date or variable
    GROUP BY A.ACCT_NBR, A.loan_ACCT_NBR, usr_type
)
WHERE usr_type = 'AGENT'
""")

# Ensure you replace 'day_key_after_value' with the appropriate date or variable you intend to use.

