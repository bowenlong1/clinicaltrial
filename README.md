fnl['PB2'] = fnl['next_pmt_dt']
fnl['PB3'] = fnl['past_due_amt'].round(2).apply(str)

adobe = fnl[['Profile_id', 'account_number', 'program_code',
       'campign_name', 'Channel', 'Control_Group', 'Campaign_type', 'PS1', 'PS2', 'PB1', 'PB2',
       'PB3', 'PB4', 'PB5']]
