Traceback (most recent call last):
  File "/databricks/python/lib/python3.9/site-packages/IPython/core/interactiveshell.py", line 3378, in run_code
    exec(code_obj, self.user_global_ns, self.user_ns)
  File "<command-3468532906296225>", line 26, in <module>
    hist_tgt = spark.createDataFrame([Row(account_number=None)])
  File "/databricks/spark/python/pyspark/instrumentation_utils.py", line 48, in wrapper
    res = func(*args, **kwargs)
  File "/databricks/spark/python/pyspark/sql/session.py", line 1216, in createDataFrame
    return self._create_dataframe(
  File "/databricks/spark/python/pyspark/sql/session.py", line 1261, in _create_dataframe
    jdf, struct = self._createFromLocalTrusted(map(prepare, data), schema)
  File "/databricks/spark/python/pyspark/sql/session.py", line 917, in _createFromLocalTrusted
    data, schema = self._wrap_data_schema(data, schema)
  File "/databricks/spark/python/pyspark/sql/session.py", line 861, in _wrap_data_schema
    struct = self._inferSchemaFromList(data, names=schema)
  File "/databricks/spark/python/pyspark/sql/session.py", line 756, in _inferSchemaFromList
    raise ValueError("Some of types cannot be determined after inferring")
ValueError: Some of types cannot be determined after inferring

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "/databricks/python/lib/python3.9/site-packages/IPython/core/interactiveshell.py", line 1997, in showtraceback
    stb = self.InteractiveTB.structured_traceback(
  File "/databricks/python/lib/python3.9/site-packages/IPython/core/ultratb.py", line 1112, in structured_traceback
    return FormattedTB.structured_traceback(
  File "/databricks/python/lib/python3.9/site-packages/IPython/core/ultratb.py", line 1006, in structured_traceback
    return VerboseTB.structured_traceback(
  File "/databricks/python/lib/python3.9/site-packages/IPython/core/ultratb.py", line 859, in structured_traceback
    formatted_exception = self.format_exception_as_a_whole(etype, evalue, etb, number_of_lines_of_context,
  File "/databricks/python/lib/python3.9/site-packages/IPython/core/ultratb.py", line 812, in format_exception_as_a_whole
    frames.append(self.format_record(r))
  File "/databricks/python/lib/python3.9/site-packages/IPython/core/ultratb.py", line 730, in format_record
    result += ''.join(_format_traceback_lines(frame_info.lines, Colors, self.has_colors, lvals))
  File "/databricks/python/lib/python3.9/site-packages/stack_data/utils.py", line 145, in cached_property_wrapper
    value = obj.__dict__[self.func.__name__] = self.func(obj)
  File "/databricks/python/lib/python3.9/site-packages/stack_data/core.py", line 698, in lines
    pieces = self.included_pieces
  File "/databricks/python/lib/python3.9/site-packages/stack_data/utils.py", line 145, in cached_property_wrapper
    value = obj.__dict__[self.func.__name__] = self.func(obj)
  File "/databricks/python/lib/python3.9/site-packages/stack_data/core.py", line 649, in included_pieces
    pos = scope_pieces.index(self.executing_piece)
  File "/databricks/python/lib/python3.9/site-packages/stack_data/utils.py", line 145, in cached_property_wrapper
    value = obj.__dict__[self.func.__name__] = self.func(obj)
  File "/databricks/python/lib/python3.9/site-packages/stack_data/core.py", line 628, in executing_piece
    return only(
  File "/databricks/python/lib/python3.9/site-packages/executing/executing.py", line 164, in only
    raise NotOneValueFound('Expected one value, found 0')
executing.executing.NotOneValueFound: Expected one value, found 0
ValueError: Some of types cannot be determined after inferring

