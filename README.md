import re
from datetime import datetime
import pandas as pd

dbfs_path = "abfss://datascience@gmfcusproddpmlandingsa.dfs.core.windows.net/MLOps_46-60/digital"

file_list = dbutils.fs.ls(dbfs_path)

qual_file_list = []

pattern = re.compile(r'^Campaign_model_Strategy_46_60_email_push_(\d{8}).txt$')

for file in file_list:
    match = pattern.match(file.name)
    if match:
        filedate = match.group(1)
        try:
            date_stamp = datetime.strptime(filedate, "%Y%m%d")
            if start_date <= date_stamp <= end_date:
                qual_file_list.append(file.path)
        except ValueError:
            pass

if qual_file_list:
    hist_tgt = spark.read.option("header", "true").text(qual_file_list).toPandas()
else:
    hist_tgt = pd.DataFrame({'account_number': []})

