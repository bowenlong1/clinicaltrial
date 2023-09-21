def write_dataframe_to_txt(dataframe, target_directory, csv_filename): # Convert all columns to string 
    for column in dataframe.columns: 
        dataframe = dataframe.withColumn(column, dataframe[column].cast("string"))
    # Prepare the concatenated DataFrame
    concatenated_df = dataframe.withColumn("single_column", concat_ws("|", *dataframe.columns)).select("single_column")

    # Create header row
    headers = "|".join(dataframe.columns)
    headers_df = spark.createDataFrame([Row(single_column=headers)])

    # Add a row_order column to headers_df and concatenated_df for sorting
    headers_df = headers_df.withColumn("row_order", lit(0))
    concatenated_df = concatenated_df.withColumn("row_order", lit(1))

    # Union the header and data and sort by row_order
    final_df = headers_df.union(concatenated_df).orderBy("row_order").drop("row_order")

    # Source and destination directory paths
    src_directory = target_directory + "/tmp_" + csv_filename
    dest_directory = target_directory + "/"
    new_csv_name = csv_filename

    # Write the DataFrame to the TXT file
    final_df.coalesce(1).write.text(src_directory)

    # Move the generated file to the desired directory and rename it
    src_files = dbutils.fs.ls(src_directory)

    for src_file in src_files: 
        if src_file.name.endswith(".txt"): 
            src_file_path = src_file.path 
            dest_file_path = f"{dest_directory}/{new_csv_name}"
            dbutils.fs.cp(src_file_path, dest_file_path)
            break  # break after copying the file

    # Remove the temporary directory
    dbutils.fs.rm(src_directory, True)
