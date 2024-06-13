import pandas as pd
import numpy as np

# Sample dataframe creation (replace this with your actual data loading)
compare = pd.DataFrame({
    'acct_nbr': [1, 2, 3],
    'p_full_custid': ['cust1', 'cust2', 'cust3'],
    's_full_custid': ['cust4', 'cust5', 'cust6'],
    'p_state': ['CA', 'NY', 'TX'],
    's_state': ['TX', 'CA', 'NY'],
    'p_home_phone': [123, 456, np.nan],
    'p_business_phone': [789, np.nan, 321],
    'p_other_phone': [111, 222, 333],
    's_home_phone': [444, 555, 666],
    's_business_phone': [np.nan, 777, 888],
    's_other_phone': [999, 0, 123],
    'scrub_acct_nbr': [1, 2, 3],
    'scrub_p_custid': ['cust1', 'cust2', 'cust3'],
    'scrub_p_home_phone': [123, 999, np.nan],
    'scrub_p_business_phone': [789, 0, 321],
    'scrub_p_other_home': [111, 222, 333],
    'scrub_s_custid': ['cust4', 'cust5', 'cust6'],
    'scrub_s_home_phone': [444, 555, 0],
    'scrub_s_business_phone': [np.nan, 777, 888],
    'scrub_s_other_phone': [999, 0, 999]
})

# Convert all phone number columns to strings to handle missing values
phone_columns = [
    'p_home_phone', 'p_business_phone', 'p_other_phone',
    's_home_phone', 's_business_phone', 's_other_phone',
    'scrub_p_home_phone', 'scrub_p_business_phone', 'scrub_p_other_home',
    'scrub_s_home_phone', 'scrub_s_business_phone', 'scrub_s_other_phone'
]

compare[phone_columns] = compare[phone_columns].astype('str')

# Function to compare columns and handle NaN values
def compare_columns(col1, col2):
    return np.where((compare[col1] == compare[col2]) | (compare[col1].isna() & compare[col2].isna()), 1, 0)

# Creating the new comparison columns
compare['compare_p_home_phone'] = compare_columns('p_home_phone', 'scrub_p_home_phone')
compare['compare_s_home_phone'] = compare_columns('s_home_phone', 'scrub_s_home_phone')
compare['compare_p_business_phone'] = compare_columns('p_business_phone', 'scrub_p_business_phone')
compare['compare_s_business_phone'] = compare_columns('s_business_phone', 'scrub_s_business_phone')
compare['compare_p_other_phone'] = compare_columns('p_other_phone', 'scrub_p_other_home')
compare['compare_s_other_phone'] = compare_columns('s_other_phone', 'scrub_s_other_phone')

import ace_tools as tools; tools.display_dataframe_to_user(name="Comparison DataFrame", dataframe=compare)

compare
