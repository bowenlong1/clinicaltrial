def write_dataframe_to_txt(dataframe, target_directory, csv_filename):

    for column in dataframe.columns: 
        dataframe = dataframe.withColumn(column, dataframe[column].cast("string"))

    ## add headers
    headers = "|".join(dataframe.columns) 
    headers_df = spark.createDataFrame([Row(single_column=headers)])

    concatenated_df = dataframe.withColumn("single_column", concat_ws("|", *dataframe.columns)).select("single_column")

    # Source and destination directory paths
    src_directory = target_directory + "/tmp_" + csv_filename
    dest_directory = target_directory + "/"
    new_csv_name = csv_filename

    # Write the DataFrame to the CSV file
    final_df = headers_df.union(concatenated_df)
    final_df.coalesce(1).write.text(target_directory + "/tmp_" + csv_filename)

    # List files in the source directory
    src_files = dbutils.fs.ls(src_directory)

    # Look for CSV files in the source directory
    for src_file in src_files: 
        if src_file.name.endswith(".txt"): 
            src_file_path = src_file.path 
            dest_file_path = f"{dest_directory}/{new_csv_name}" # Copy the TXT file to the destination directory and rename it 
            dbutils.fs.cp(src_file_path, dest_file_path) # Remove the source directory (recursively) 
            dbutils.fs.rm(src_directory, True)
5856136|002300010000000000111043999119|S1|GMF_SERV_PUSH_EM_4660DPD_confirm_Sep23|P|N|E|GMF|09/13/2023|1922.02|8150|1-855-719-9695
4924651|002300010000000000111030265144|S1|GMF_SERV_PUSH_EM_4660DPD_confirm_Sep23|P|N|E|GMF|09/08/2023|1123.37|8050|1-800-944-5047
6874531|002300010000000000111058497501|S1|GMF_SERV_PUSH_EM_4660DPD_confirm_Sep23|P|N|E|GMF|09/17/2023|1040.36|8050|1-800-944-5047
2999558|002300010000000000111000501087|S1|GMF_SERV_PUSH_EM_4660DPD_confirm_Sep23|P|N|E|GMF|09/12/2023|743.32|8150|1-855-719-9695
Profile_id|account_number|program_code|campign_name|Channel|Control_Group|Campaign_type|PS1|PS2|PB1|PB2|PB3|PB4|PB5
3899191|002300010000000000111013464270|S1|GMF_SERV_PUSH_EM_4660DPD_confirm_Sep23|P|N|E|GMF|09/17/2023|1192.99|8050|1-800-944-5047
3944855|002300010000000000111013480009|S1|GMF_SERV_PUSH_EM_4660DPD_confirm_Sep23|P|N|E|GMF|09/15/2023|691.84|8150|1-855-719-9695
5048666|002300010000000000111032066752|S1|GMF_SERV_PUSH_EM_4660DPD_confirm_Sep23|P|N|E|GMF|09/16/2023|1282.42|8150|1-855-719-9695
3071280|002300010000000000111001238502|S1|GMF_SERV_PUSH_EM_4660DPD_confirm_Sep23|P|N|E|GMF|09/10/2023|2022.04|8150|1-855-719-9695
2578548|002300010000000000111048283019|S1|GMF_SERV_PUSH_EM_4660DPD_confirm_Sep23|P|N|E|GMF|09/13/2023|1115.05|8050|1-800-944-5047
2791765|002300010000000000111004396072|S1|GMF_SERV_PUSH_EM_4660DPD_confirm_Sep23|P|N|E|GMF|09/14/2023|2591.84|8150|1-855-719-9695
