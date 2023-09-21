from pyspark.sql import functions as F

# Adding new columns 'PB2' and 'PB3'
fnl = fnl.withColumn("PB2", fnl["next_pmt_dt"])
fnl = fnl.withColumn("PB3", F.round(fnl["past_due_amt"], 2).cast("string"))

# Selecting specific columns for 'adobe'
adobe = fnl.select("Profile_id", "account_number", "program_code", "campign_name", "Channel", "Control_Group", 
                   "Campaign_type", "PS1", "PS2", "PB1", "PB2", "PB3", "PB4", "PB5")
