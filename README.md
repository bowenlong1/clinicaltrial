from pyspark.sql import functions as F

# Read email_agent into a DataFrame (assuming it's already available)
email_agent_df = spark.read.table("email_agent")

# Extract `loan_acct_nbr` from email_agent
loan_acct_nbr_list = [row['loan_acct_nbr'] for row in email_agent_df.collect()]

# Read hist_tgt into a DataFrame (assuming it's already available)
hist_tgt_df = spark.read.table("hist_tgt")

# Extract `account_number` from hist_tgt
account_number_list = [row['account_number'] for row in hist_tgt_df.collect()]

# Filter the acct2 DataFrame based on given conditions
acct2_df = spark.read.table("acct2")
acct3_df = (acct2_df
            .filter((F.col("hold_ind") != 'Y') & 
                    (F.col("dpd").between(46, 60)) & 
                    (F.col("actvty_cd") == 1) &
                    (F.col("PROMISE_PEND_IND") != "Y") & 
                    (~F.col("QUEUE_ID").isin([5243, 5240, 5205, 5202])) & 
                    (~F.col("loan_acct_nbr").isin(loan_acct_nbr_list)) & 
                    (~F.col("acct_nbr").isin(account_number_list))))

# Save acct3_df into a table or perform further actions as needed
acct3_df.write.saveAsTable("acct3")

