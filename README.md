dbfs_path = "abfss://datascience@gmfcusproddpmlandingsa.dfs.core.windows.net/MLOps_46-60/digital"

file_list = dbutils.fs.ls(dbfs_path)


qual_file_list = []
for file in file_list:
    filedate = file.name[9:17]
    filename = file.name
    try:
        date_stamp = datetime.strptime(filedate, "%Y%m%d")
        if start_date <= date_stamp <= end_date:
            # filtered_files.append(file)
            qual_file_list.append(file.path)
    except ValueError:
        pass
    
if qual_file_list:
    hist_tgt = spark.read.option("header","true").csv(qual_file_list).toPandas()
else:
    hist_tgt = pd.DataFrame({'account_number':[]})
