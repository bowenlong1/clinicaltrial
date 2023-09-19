def write_dataframe_to_csv(dataframe, target_directory, csv_filename):
    # Source and destination directory paths
    src_directory = target_directory + "/tmp_" + csv_filename
    dest_directory = target_directory + "/"
    new_csv_name = csv_filename

    # Write the DataFrame to the CSV file
    dataframe.coalesce(1).write.csv(target_directory + "/tmp_" + csv_filename, header=True, mode="overwrite")    

    # List files in the source directory
    src_files = dbutils.fs.ls(src_directory)

    # Look for CSV files in the source directory
    for src_file in src_files:
        if src_file.name.endswith(".csv"):
            src_file_path = src_file.path
            dest_file_path = f"{dest_directory}/{new_csv_name}"
            # Copy the CSV file to the destination directory and rename it
            dbutils.fs.cp(src_file_path, dest_file_path)
            # Remove the source directory (recursively)
            dbutils.fs.rm(src_directory, True)
