# How to disable telemetry reporting
Azure Data Studio collects usage data and sends it to Microsoft to help improve our products and services. Read the [Microsoft Enterprise and Developer Privacy Statement](https://privacy.microsoft.com/en-us/privacystatement) to learn more.

If you don’t wish to send usage data to Microsoft, you can set the `telemetry.enableTelemetry` setting to `false`.

From **File > Preferences > Settings**, add the following option to disable telemetry reporting, this will silence all telemetry events from the Azure Data Studio shell.
```JSON
    "telemetry.enableTelemetry": false
```
>**Important Notice:** This option requires a restart of Azure Data Studio to take effect. Azure Data Studio gives you the option to install Microsoft and third party extensions. These extensions may be collecting their own usage data and are not controlled by the `telemetry.enableTelemetry` setting. Consult the specific extension’s documentation to learn about its telemetry reporting.

The below image highlights the workflow using the updated Settings Editor UI.

<img width="800px" src="https://user-images.githubusercontent.com/599935/54712176-eaa4c800-4b08-11e9-8310-10ae64d74726.png"></img>

# How to disable crash reporting
Azure Data Studio collects data about any crashes that occur and sends it to Microsoft to help improve our products and services. Read our privacy statement to learn more.

If you don’t wish to send crash data to Microsoft, you can set the `telemetry.enableCrashReporter` setting to `false`.
```JSON
    "telemetry.enableCrashReporter": false
```
>**Important Notice:** This option requires a restart of Azure Data Studio to take effect.