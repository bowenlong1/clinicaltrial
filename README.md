from pyspark.sql import functions as F

# Convert due_key to string
acct2 = acct2.withColumn("due_key_str", F.col("due_key").cast("string"))

# Extract year, month, day and create the desired date string format
acct2 = acct2.withColumn("due_date", 
                         F.expr("concat(substr(due_key_str, 5, 2), '/', substr(due_key_str, 7, 2), '/', substr(due_key_str, 1, 4))"))

# Drop the old due_key column and rename due_date to due_key
acct2 = acct2.drop("due_key").withColumnRenamed("due_date", "due_key")

# Apply the previous filter to get acct3
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
