from pyspark.sql import Row
from pyspark.sql.functions import concat_ws

def write_dataframe_to_dat(dataframe, target_directory, dat_filename):
    # Convert all columns to string
    for column in dataframe.columns:
        dataframe = dataframe.withColumn(column, dataframe[column].cast("string"))

    # Prepare the concatenated DataFrame
    concatenated_df = dataframe.withColumn("single_column", concat_ws("|", *dataframe.columns)).select("single_column")

    # Create header row
    headers = "|".join(dataframe.columns)
    headers_df = spark.createDataFrame([Row(single_column=headers)])
    
    # Union the header and data
    final_df = headers_df.union(concatenated_df)

    # Temporary directory to initially write the data
    temp_path = f"{target_directory}/temp_{dat_filename}"
    
    # Write the DataFrame to a temporary location
    final_df.coalesce(1).write.mode('overwrite').text(temp_path)

    # Rename the part file to .dat extension and move it to the target directory
    part_files = dbutils.fs.ls(temp_path)
    for part_file in part_files:
        if 'part-' in part_file.name:
            dbutils.fs.mv(part_file.path, f"{target_directory}/{dat_filename}")

    # Cleanup: Remove the temporary directory
    dbutils.fs.rm(temp_path, True)

    return f"File saved to: {target_directory}/{dat_filename}"

# Example Usage:
# result = write_dataframe_to_dat(your_dataframe, "/your/target/directory", "output.dat")
# print(result)

