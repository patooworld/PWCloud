# Telemetry

Azure Data Studio collects telemetry data, which is used to help understand how to improve the product. For example, this usage data helps to debug issues, such as slow start-up times, and to prioritize new features.  While we appreciate the insights this data provides, we also know that not everyone wants to send usage data and you can disable telemetry as described in [disable telemetry reporting](#disable-telemetry-reporting). You can also read our [privacy statement](https://privacy.microsoft.com/privacystatement) to learn more.

# How to disable telemetry reporting
Azure Data Studio collects usage data and sends it to Microsoft to help improve our products and services. Read the [Microsoft Enterprise and Developer Privacy Statement](https://privacy.microsoft.com/privacystatement) to learn more.

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

## Extensions and telemetry

Azure Data Studio lets you add features to the product by installing Microsoft and third-party extensions. These extensions may be collecting their own usage data and are not controlled by the `telemetry.enableTelemetry` setting. Consult the specific extension's documentation to learn about its telemetry reporting and whether it can be disabled.

## Output channel for telemetry events

If you'd like to review the telemetry events in Azure Data Studio as they are sent, you can enable tracing and it will record telemetry events. Set the log level to **Trace** using the **Developer: Set Log Level...** command and then in the Output panel (`kb(workbench.action.output.toggleOutput)`), pick **Log (Telemetry)** from the drop-down.

When tracing telemetry events, the events are also logged to a local file `telemetry.log` which you can view using the **Developer: Open Log File...** command and choosing **Telemetry** from the drop-down.

## GDPR and Azure Data Studio

In addition to supporting the General Data Protection Regulation (GDPR), the Azure Data Studio team takes privacy very seriously. That's both for Microsoft as a company and specifically within the Azure Data Studio team.

One question we expect people to ask is to see the data we collect. However, we don't have a reliable way to do this as Azure Data Studio does not have a 'sign-in' experience that would uniquely identify a user.  We do send information that helps us approximate a single user for diagnostic purposes (this is based on a hash of the network adapter NIC) but this is not guaranteed to be unique. For example, virtual machines (VMs) often rotate NIC IDs or allocate from a pool. This technique is sufficient to help us when working through problems, but it is not reliable enough for us to 'provide your data'.

We expect our approach to evolve as we learn more about GDPR and the expectations of our users. We greatly appreciate the data users do send to us, as it is very valuable and Azure Data Studio is a better product for everyone because of it. And again, if you are worried about privacy, we offer the ability to disable sending telemetry as described in [disable telemetry reporting](/docs/getstarted/telemetry.md#disable-telemetry-reporting).