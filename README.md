# Write the DataFrame to the TXT file
dataframe.coalesce(1).write.text(target_directory + "/tmp_" + txt_filename)

# List files in the source directory
src_files = dbutils.fs.ls(src_directory)

# Look for TXT files in the source directory
for src_file in src_files:
    if src_file.name.endswith(".txt"):
        src_file_path = src_file.path
        dest_file_path = f"{dest_directory}/{new_txt_name}"
        # Copy the TXT file to the destination directory and rename it
        dbutils.fs.cp(src_file_path, dest_file_path)
        # Remove the source directory (recursively)
        dbutils.fs.rm(src_directory, True)
