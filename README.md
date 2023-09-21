from pyspark.sql.functions import col, substring

queues_545_1 = spark.sql(
    f"""select
        B.PARTIAL_ACCT_NBR,
        CASE WHEN D.DEFLT_ACCT_STATUS_CD = 481 THEN null 
        WHEN B.ACCT_STATUS_CD  != 'AC'  THEN null 
        ELSE Q.QUEUE_ID END AS QUEUE_ID,
        CASE WHEN D.DEFLT_ACCT_STATUS_CD = 481  THEN 'Inactive'
        WHEN B.ACCT_STATUS_CD != 'AC'  THEN 'Inactive'
        WHEN Q.QUEUE_ID = 561 THEN 'UNASSIGNED'
        WHEN substring(Q.QUEUE_NM, 4,1) = 'M' 
        THEN substring(Q.QUEUE_NM,7,len(Q.QUEUE_NM) - 6) 
        ELSE substring(Q.QUEUE_NM,6,len(Q.QUEUE_NM) - 5) 
        END AS QUEUE_NM
        ,D.PROMISE_PEND_IND
        ,A.DAY_KEY
        ,A.LAST_CONTACT_DAY_KEY
        ,A.DAYS_IN_CALL_QUEUE_QTY
    	,C.DT
        FROM prsnt_svcng.INTERACTN_ACCT_STATUS_FACT A 
            JOIN prsnt_svcng.ACCT_DIM B ON A.ACCT_KEY = B.ACCT_KEY 
            JOIN prsnt_svcng.DAY_DIM C ON A.DAY_KEY = C.DAY_KEY
            JOIN prsnt_svcng.INTERACTN_ACCT_DIM D ON A.INTERACTN_ACCT_KEY = D.INTERACTN_ACCT_KEY 
            JOIN prsnt_svcng.QUEUE_DIM Q ON A.CALL_QUEUE_KEY = Q.QUEUE_KEY
            WHERE C.day_key <= {day_key_prior_str}
                AND c.day_key >= {day_key_prior_str}

    """
)
queues_545_1.createOrReplaceTempView("queues_545_1")


queues_545_1.createOrReplaceTempView("queues_545_1")
AnalysisException: Table or view not found: prsnt_base.acct_dim; line 66 pos 5
