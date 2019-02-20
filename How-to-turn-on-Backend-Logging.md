While running Azure Data Studio do the following:
1. open up settings by pressing Ctrl+,
and then turn on this setting:
_"mssql.tracingLevel": "All",_
1. Save the setings and then either do a reload or restart azure data studio so we capture the logs from the beginning of the session.
1. Ctrl-Shift-U -> Log (SqlTools) from the drop down selection will show you the logs and location of log file. This window will also show the tail of the log as the scenario is being traced.
1. Now run your repro and then do some stray clicks in azure data studio to make sure that the log from repro is fully flushed to the file. Save the log file and share with the development team for troubleshooting.

