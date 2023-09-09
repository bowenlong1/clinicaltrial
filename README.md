# Assuming these values for the SAS macro variables
day_key_prior_7 = '2023-08-24' # replace with appropriate value
day_key_after = '2023-08-31'  # replace with appropriate value

query = f"""
CREATE OR REPLACE TEMPORARY VIEW email7d AS 
SELECT a.loan_acct_nbr, 
       b.last_email_dt,
       COALESCE(CASE 
                 WHEN DATE('{day_key_prior_7}') <= DATE(b.last_email_dt) 
                      AND DATE(b.last_email_dt) <= DATE('{day_key_after}') 
                 THEN 1 
                 ELSE 0 
                END, 0) AS email_7day
FROM pop2 a 
LEFT JOIN AS_LAST_email b ON a.loan_acct_nbr = b.loan_acct_nbr
"""

spark.sql(query)

from pyspark.sql import functions as F

# Assuming these values for the SAS macro variables
day_key_prior_7 = '2023-08-24' # replace with appropriate value
day_key_after = '2023-08-31'  # replace with appropriate value

# Assuming dataframes pop2_df and AS_LAST_email_df correspond to pop2 and AS_LAST_email tables respectively
email7d_df = pop2_df.alias('a').join(
    AS_LAST_email_df.alias('b'), 
    F.col('a.loan_acct_nbr') == F.col('b.loan_acct_nbr'), 
    'left'
).select(
    F.col('a.loan_acct_nbr'),
    F.col('b.last_email_dt'),
    F.coalesce(
        F.when(
            (F.col('b.last_email_dt') >= day_key_prior_7) & 
            (F.col('b.last_email_dt') <= day_key_after), 
            F.lit(1)
        ).otherwise(F.lit(0)), 
        F.lit(0)
    ).alias('email_7day')
)

email7d_df.show()

