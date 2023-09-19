from pyspark.sql import Row
from pyspark.sql.functions import concat_ws

# Convert all columns to string
for column in dataframe.columns:
    dataframe = dataframe.withColumn(column, dataframe[column].cast("string"))

# Create a DataFrame containing column names as a single row
headers = "|".join(dataframe.columns)
headers_df = spark.createDataFrame([Row(single_column=headers)])

# Concatenate all columns of the main DataFrame into a single column using '|' as the delimiter
concatenated_df = dataframe.withColumn("single_column", concat_ws("|", *dataframe.columns)).select("single_column")

# Union the headers DataFrame with the main DataFrame
final_df = headers_df.union(concatenated_df)

# Save the final DataFrame as a text file
final_df.coalesce(1).write.text(target_directory + "/tmp_" + txt_filename)

# ... Rest of your code remains the same ...

