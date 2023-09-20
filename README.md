acct = spark.sql("""
  SELECT DISTINCT loan_acct_nbr, dpd 
  FROM pop
""")

spark.sql("""
CREATE OR REPLACE TEMPORARY VIEW acct AS
SELECT DISTINCT CAST(SUBSTRING(account_number, LENGTH(account_number) - 11, 12) AS BIGINT) AS loan_acct_nbr
FROM pop
""")


# Assume you have read the data from Oracle into oracle_df
oracle_df.createOrReplaceTempView("oracle_data")

QUEUES_545_1 = spark.sql("""
  SELECT B.PARTIAL_ACCT_NBR,
    CASE 
      WHEN D.DEFLT_ACCT_STATUS_CD = 481 THEN null 
      WHEN B.ACCT_STATUS_CD <> 'AC' THEN null 
      ELSE Q.QUEUE_ID 
    END AS QUEUE_ID, 
    CASE 
      WHEN D.DEFLT_ACCT_STATUS_CD = 481 THEN 'Inactive'
      WHEN B.ACCT_STATUS_CD <> 'AC' THEN 'Inactive'
      WHEN Q.QUEUE_ID = 561 THEN 'UNASSIGNED'
      WHEN SUBSTR(Q.QUEUE_NM, 4, 1) = 'M' THEN SUBSTR(Q.QUEUE_NM, 7, LENGTH(Q.QUEUE_NM) - 6) 
      ELSE SUBSTR(Q.QUEUE_NM, 6, LENGTH(Q.QUEUE_NM) - 5) 
    END AS QUEUE_NM,
    D.PROMISE_PEND_IND,
    A.DAY_KEY,
    A.LAST_CONTACT_DAY_KEY,
    A.DAYS_IN_CALL_QUEUE_QTY,
    C.DT
  FROM oracle_data
""")

acct2 = spark.sql("""
  SELECT a.loan_acct_nbr, 
         a.dpd as dpd1, 
         b.actvty_cd, 
         b.hold_ind,
         c.days_delinq_qty as dpd, 
         d.PROMISE_PEND_IND, 
         d.QUEUE_ID 
  FROM acct a 
  LEFT JOIN sar.acct_status b ON a.loan_acct_nbr = b.partial_acct_nbr AND b.day_key = {}
  LEFT JOIN svcng.acct_status_fact c ON a.loan_acct_nbr = c.partial_acct_nbr AND c.day_key = {}
  LEFT JOIN QUEUES_545_1 d ON a.loan_acct_nbr = d.partial_acct_nbr
""".format(day_key_prior, day_key_prior))

acct3 = spark.sql("""
  SELECT * 
  FROM acct2 
  WHERE hold_ind <> 'Y' 
    AND dpd BETWEEN 46 AND 60 
    AND actvty_cd = 1 
    AND PROMISE_PEND_IND <> 'Y' 
    AND QUEUE_ID NOT IN (5243, 5240, 5205, 5202)
""")
