TypeError: boolean value of NA is ambiguous
---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
File <command-2483107499807553>, line 6
      3     return np.where(compare[col1] == compare[col2], 1, np.where(compare[col1].isna() & compare[col2].isna(), 1, 0))
      5 # Creating the new comparison columns
----> 6 compare['compare_p_home_phone'] = compare_columns('p_home_phone', 'scrub_p_home_phone')
      7 compare['compare_s_home_phone'] = compare_columns('s_home_phone', 'scrub_s_home_phone')
      8 compare['compare_p_business_phone'] = compare_columns('p_business_phone', 'scrub_p_business_phone')

File <command-2483107499807553>, line 3, in compare_columns(col1, col2)
      2 def compare_columns(col1, col2):
----> 3     return np.where(compare[col1] == compare[col2], 1, np.where(compare[col1].isna() & compare[col2].isna(), 1, 0))

File <__array_function__ internals>:5, in where(*args, **kwargs)

File /databricks/python/lib/python3.10/site-packages/pandas/_libs/missing.pyx:382, in pandas._libs.missing.NAType.__bool__()

TypeError: boolean value of NA is ambiguous
