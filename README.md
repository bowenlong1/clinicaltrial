from pyspark.sql import functions as F

loan_acct_nbr_list = [row['loan_acct_nbr'] for row in email_agent.collect()]
account_number_list = [row['account_number'] for row in hist_tgt.collect()]

acct3 = (acct2.filter(
    (F.col("hold_ind").isNull() | (F.col("hold_ind") != 'Y')) &
    (F.col("dpd").between(46, 60)) &
    (F.col("actvty_cd") == 1) &
    (F.col("PROMISE_PEND_IND").isNull() | (F.col("PROMISE_PEND_IND") != "Y")) &
    (~F.col("QUEUE_ID").isin([5243, 5240, 5205, 5202])) &
    (~F.col("loan_acct_nbr").isin(loan_acct_nbr_list)) &
    (~F.col("acct_nbr").isin(account_number_list))
))
