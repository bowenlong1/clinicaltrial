‘compare’ dataframe
['acct_nbr', 'p_full_custid', 's_full_custid', 'p_state', 's_state', 'p_home_phone', 'p_business_phone', 'p_other_phone', 's_home_phone', 's_business_phone', 's_other_phone', 'scrub_acct_nbr', 'scrub_p_custid', 'scrub_p_home_phone', 'scrub_p_business_phone', 'scrub_p_other_home', 'scrub_s_custid', 'scrub_s_home_phone', 'scrub_s_business_phone', 'scrub_s_other_phone'],

for this df, I need to create six 6 columns compare p_home_phone vs scrub_p_home_phone, s_home_phone vs scrub_s_phone…

if they are equal give me 1, if not then 0. Noting that if both columns are nan, treat them as equal 1

