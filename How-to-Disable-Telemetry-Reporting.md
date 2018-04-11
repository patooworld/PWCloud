# How to disable telemetry reporting
SQL Operations Studio collects usage data and sends it to Microsoft to help improve our products and services. Read the [Microsoft Enterprise and Developer Privacy Statement](https://privacy.microsoft.com/en-us/privacystatement) to learn more.

If you don’t wish to send usage data to Microsoft, you can set the `telemetry.enableTelemetry` setting to `false`.

From **File > Preferences > Settings**, add the following option to disable telemetry reporting, this will silence all telemetry events from the SQL Operations Studio shell.
```JSON
    "telemetry.enableTelemetry": false
```
>**Important Notice:** This option requires a restart of SQL Operations Studio to take effect. SQL Operations Studio gives you the option to install Microsoft and third party extensions. These extensions may be collecting their own usage data and are not controlled by the `telemetry.enableTelemetry` setting. Consult the specific extension’s documentation to learn about its telemetry reporting.


# How to disable crash reporting
SQL Operations Studio collects data about any crashes that occur and sends it to Microsoft to help improve our products and services. Read our privacy statement to learn more.

If you don’t wish to send crash data to Microsoft, you can set the `telemetry.enableCrashReporter` setting to `false`.
```JSON
    "telemetry.enableCrashReporter": false
```
>**Important Notice:** This option requires a restart of SQL Operations Studio to take effect.