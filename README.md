acct3 = spark.sql("""
  SELECT * 
  FROM acct2 
  WHERE hold_ind <> 'Y' 
    AND dpd BETWEEN 46 AND 60 
    AND actvty_cd = 1 
    AND PROMISE_PEND_IND <> 'Y' 
    AND QUEUE_ID NOT IN (5243, 5240, 5205, 5202)
""")
