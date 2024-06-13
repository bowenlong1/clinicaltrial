---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
File <command-2483107499807553>, line 9
      1 # Convert all phone number columns to strings to handle missing values
      2 phone_columns = [
      3     'p_home_phone', 'p_business_phone', 'p_other_phone',
      4     's_home_phone', 's_business_phone', 's_other_phone',
      5     'scrub_p_home_phone', 'scrub_p_business_phone', 'scrub_p_other_home',
      6     'scrub_s_home_phone', 'scrub_s_business_phone', 'scrub_s_other_phone'
      7 ]
----> 9 compare[phone_columns] = compare[phone_columns].astype('Int64').astype('str')
     11 # Function to compare columns and handle missing values
     12 def compare_columns(col1, col2):

File /databricks/python/lib/python3.10/site-packages/pandas/core/generic.py:5905, in NDFrame.astype(self, dtype, copy, errors)
   5899         results.append(res_col)
   5901 elif is_extension_array_dtype(dtype) and self.ndim > 1:
   5902     # GH 18099/22869: columnwise conversion to extension dtype
   5903     # GH 24704: use iloc to handle duplicate column names
   5904     # TODO(EA2D): special case not needed with 2D EAs
-> 5905     results = [
   5906         self.iloc[:, i].astype(dtype, copy=copy)
   5907         for i in range(len(self.columns))
   5908     ]
   5910 else:
   5911     # else, only a single dtype is given
   5912     new_data = self._mgr.astype(dtype=dtype, copy=copy, errors=errors)

File /databricks/python/lib/python3.10/site-packages/pandas/core/generic.py:5906, in <listcomp>(.0)
   5899         results.append(res_col)
   5901 elif is_extension_array_dtype(dtype) and self.ndim > 1:
   5902     # GH 18099/22869: columnwise conversion to extension dtype
   5903     # GH 24704: use iloc to handle duplicate column names
   5904     # TODO(EA2D): special case not needed with 2D EAs
   5905     results = [
-> 5906         self.iloc[:, i].astype(dtype, copy=copy)
   5907         for i in range(len(self.columns))
   5908     ]
   5910 else:
   5911     # else, only a single dtype is given
   5912     new_data = self._mgr.astype(dtype=dtype, copy=copy, errors=errors)

File /databricks/python/lib/python3.10/site-packages/pandas/core/generic.py:5912, in NDFrame.astype(self, dtype, copy, errors)
   5905     results = [
   5906         self.iloc[:, i].astype(dtype, copy=copy)
   5907         for i in range(len(self.columns))
   5908     ]
   5910 else:
   5911     # else, only a single dtype is given
-> 5912     new_data = self._mgr.astype(dtype=dtype, copy=copy, errors=errors)
   5913     return self._constructor(new_data).__finalize__(self, method="astype")
   5915 # GH 33113: handle empty frame or series

File /databricks/python/lib/python3.10/site-packages/pandas/core/internals/managers.py:419, in BaseBlockManager.astype(self, dtype, copy, errors)
    418 def astype(self: T, dtype, copy: bool = False, errors: str = "raise") -> T:
--> 419     return self.apply("astype", dtype=dtype, copy=copy, errors=errors)

File /databricks/python/lib/python3.10/site-packages/pandas/core/internals/managers.py:304, in BaseBlockManager.apply(self, f, align_keys, ignore_failures, **kwargs)
    302         applied = b.apply(f, **kwargs)
    303     else:
--> 304         applied = getattr(b, f)(**kwargs)
    305 except (TypeError, NotImplementedError):
    306     if not ignore_failures:

File /databricks/python/lib/python3.10/site-packages/pandas/core/internals/blocks.py:580, in Block.astype(self, dtype, copy, errors)
    562 """
    563 Coerce to the new dtype.
    564 
   (...)
    576 Block
    577 """
    578 values = self.values
--> 580 new_values = astype_array_safe(values, dtype, copy=copy, errors=errors)
    582 new_values = maybe_coerce_values(new_values)
    583 newb = self.make_block(new_values)

File /databricks/python/lib/python3.10/site-packages/pandas/core/dtypes/cast.py:1292, in astype_array_safe(values, dtype, copy, errors)
   1289     dtype = dtype.numpy_dtype
   1291 try:
-> 1292     new_values = astype_array(values, dtype, copy=copy)
   1293 except (ValueError, TypeError):
   1294     # e.g. astype_nansafe can fail on object-dtype of strings
   1295     #  trying to convert to float
   1296     if errors == "ignore":

File /databricks/python/lib/python3.10/site-packages/pandas/core/dtypes/cast.py:1237, in astype_array(values, dtype, copy)
   1234     values = values.astype(dtype, copy=copy)
   1236 else:
-> 1237     values = astype_nansafe(values, dtype, copy=copy)
   1239 # in pandas we don't store numpy str dtypes, so convert to object
   1240 if isinstance(dtype, np.dtype) and issubclass(values.dtype.type, str):

File /databricks/python/lib/python3.10/site-packages/pandas/core/dtypes/cast.py:1108, in astype_nansafe(arr, dtype, copy, skipna)
   1106 # dispatch on extension dtype if needed
   1107 if isinstance(dtype, ExtensionDtype):
-> 1108     return dtype.construct_array_type()._from_sequence(arr, dtype=dtype, copy=copy)
   1110 elif not isinstance(dtype, np.dtype):  # pragma: no cover
   1111     raise ValueError("dtype must be np.dtype or ExtensionDtype")

File /databricks/python/lib/python3.10/site-packages/pandas/core/arrays/integer.py:325, in IntegerArray._from_sequence(cls, scalars, dtype, copy)
    321 @classmethod
    322 def _from_sequence(
    323     cls, scalars, *, dtype: Dtype | None = None, copy: bool = False
    324 ) -> IntegerArray:
--> 325     values, mask = coerce_to_array(scalars, dtype=dtype, copy=copy)
    326     return IntegerArray(values, mask)

File /databricks/python/lib/python3.10/site-packages/pandas/core/arrays/integer.py:228, in coerce_to_array(values, dtype, mask, copy)
    224     values[mask] = 1
    225 if inferred_type in ("string", "unicode"):
    226     # casts from str are always safe since they raise
    227     # a ValueError if the str cannot be parsed into an int
--> 228     values = values.astype(dtype, copy=copy)
    229 else:
    230     values = safe_cast(values, dtype, copy=False)

ValueError: invalid literal for int() with base 10: '<NA>'
