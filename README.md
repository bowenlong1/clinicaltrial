from pyspark.sql.functions import concat_ws

# Convert all columns to string
for column in dataframe.columns:
    dataframe = dataframe.withColumn(column, dataframe[column].cast("string"))

# Concatenate all columns into a single column using '|' as the delimiter
single_column_df = dataframe.withColumn("single_column", concat_ws("|", *dataframe.columns))

# Now save the concatenated DataFrame as a text file
single_column_df.select("single_column").coalesce(1).write.text(target_directory + "/tmp_" + txt_filename)

# ... Rest of your code remains the same ...

