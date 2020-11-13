## If you've experienced any issue

Report issues to [Github Issue Tracker](https://github.com/Microsoft/azuredatastudio/issues) and let us know any details that will help reproduce the error. Please include any log information from the log file.

## How to set the logging level:

### Azure Data Studio
Run the `Developer: Set Log Level...` command to select the log level for the current session. This value is NOT persisted over multiple sessions - so if you restart Azure Data Studio it will revert back to the default `Info` level. 

If you want to enable debug logging for startup then set the log level to `Debug` and run the `Developer: Reload Window` command

### MSSQL (Built-In Extension)

If the `Mssql: Log Debug Info` user setting is set to true then debug log info will be sent to the `MSSQL` output channel.

The `Mssql: Tracing Level` user setting is used to control the verbosity of the logging.

## Debug log location
From Azure Data Studio run the `Developer: Open Logs Folder` command to open the path to the logs. There's many different type of log files that write there, a few of the commonly used ones are:

1. `renderer#.log` (e.g. renderer1.log) - this is the log file for the main process.
1. `telemetry.log` - When the log level is set to `Trace` this will contain the telemetry events sent by Azure Data Studio
1. `exthost#/exthost.log` - Log file for the extension host process (this is only the process itself, not the extensions running inside it)
1. `exthost#/Microsoft.mssql` - Logs for the mssql extension which contains much of the core logic for MSSQL related features
   * sqltools.log is the log for SQL Tools Service
1. `exthost#/output_logging_#######` - these folders contain the messages displayed in the `Output` panel in Azure Data Studio. Each file is named `#-<Channel Name>` so for example the `Notebooks` output channel may output to a file named `3-Notebooks.log`.

If asked to provide logs please zip up the entire folder to ensure that the correct logs are included. 