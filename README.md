print("Total number of accounts for the run {} are {}".format(exp_st_dt, worklist_file.count()))
# Aggregating at segment level, for each segment checking number of accounts and percentage of accounts contacted / called
calls_full.groupBy("group").agg(
    count("*").alias("#accounts"),
    sum("CALL").alias("TOTAL_CALL"),
    (round(sum("CALL") / count("*") * 100, 2)).alias("PCT_TOTAL_CALL"),
    sum("CONTACT").alias("TOTAL_CONTACT"),
    (round(sum("CONTACT") / count("*") * 100, 2)).alias("PCT_TOTAL_CONTACT"),
    sum("IC_CALL").alias("TOTAL_IC_CALL"),
    (round(sum("IC_CALL") / count("*") * 100, 2)).alias("PCT_TOTAL_IC_CALL"),
    sum("IC_CONTACT").alias("TOTAL_IC"),
    (round(sum("IC_CONTACT") / count("*") * 100, 2)).alias("PCT_TOTAL_IC"),
    (sum("OC_CALL").alias("TOTAL_OC_CALL")),
    (round(sum("OC_CALL") / count("*") * 100, 2)).alias("PCT_TOTAL_OC_CALL"),
    (sum("OC_CONTACT").alias("TOTAL_OC")),
    (round(sum("OC_CONTACT") / count("*") * 100, 2)).alias("PCT_TOTAL_OC"),
).display()



(9) Spark Jobs
Total number of accounts for the run 20230905 are 6781
SparkRuntimeException: [INVALID_PARAMETER_VALUE] The value of parameter(s) 'expr, key' in `aes_encrypt`/`aes_decrypt` is invalid: Detail message: Input length must be multiple of 16 when decrypting with padded cipher
