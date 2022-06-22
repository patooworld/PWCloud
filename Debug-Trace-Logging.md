Debug/Trace logging can be helpful to trace what happened when an issue occurred or why some operation is failing. There are a number of different types of logging that ADS generates during runtime, this document will go over each of them in turn. 

# General Information

## Where Logs are stored

All log files mentioned here are written to the same directory, which can be accessed by running the `Developer: Open Logs folder`. This folder will be referenced as `$(LogsRoot)` from now on.

## Logs for multiple instances

If you have multiple instances of ADS opened at once then they will all write to the same log folder, but to separate files. These will typically be differentiated by a number (1, 2, 3, etc.) being appended to either the log file name or the folder (e.g. `renderer1.log` or `exthost1`)

# Log Information

## Core (Renderer) Logging

The core part of ADS (also called the renderer) is where most of the central logic for the app happens. 

### How to access logs

They are written to `$(LogsRoot)/render#.log` where # is a number associated with the instance of ADS that generated the log.

### How to change the log level

Run the `Developer: Set Log Level` command. This will take effect immediately.

## Developer Console Logging

While console logs aren't output to a file directly they can still be useful for debugging since some log messages are only sent to the console. 

### How to access logs

Run the `Developer: Toggle Developer Tools` command and then go to the `Console` tab.

### How to change the log level

Console logs are unable to be changed, they will log everything sent to the console. You can filter what level is shown in the developer console though by clicking the filter dropdown at the top of the developer tools window. 

## Azure Logging

### How to access logs

The built-in Azure extension has its own logging. This is all logged to the `Azure Accounts` Output channel, so can be viewed in ADS by going to the `Output` panel and selecting `Azure Accounts` from the dropdown. 

The values for this output channel are also written to `$(LogsRoot)/exthost#/output_logging_$(timestamp)`. The exact output_logging folder it's stored in depends on when the channel was initialized, look for a file named `#-Azure Accounts.log`.

### How to change the log level

There are two types of logging level for the Azure extension. 

First is the general log level - this is controlled by the `Azure: Logging Level` setting.

Second is PII logging. Because of the sensitive nature of this logging there's a separate setting named `Azure: Pii Logging` that when enabled will log PII information about your Azure Account (such as account ID) to the output channel.

## MSSQL 

The built-in MSSQL extension has its own logging as well for the 3 service processes that it starts up (CredentialStore, ResourceProvider and SqlTools).

The SqlTools logs can contain information about any T-SQL queries ran for most functionality in the app; including the query editor, Object Explorer, the dashboard and queries that extensions execute through the azdata API. To enable this logging set the MSSQL logging level to `Verbose` or `All`

### How to access logs

The logs for these processes are stored in the `$(LogsRoot)/exthost#/Microsoft.mssql` folder and are named `credentialstore.log`, `resourceprovider.log` and `sqltools.log` respectively. 

### How to change the log level

All logging for the MSSQL extension is controlled through the `MSSQL: Tracing Level` setting in ADS. 

*Important* After changing this value you must restart ADS for the changes to take effect.

