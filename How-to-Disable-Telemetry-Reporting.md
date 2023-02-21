# Telemetry

Azure Data Studio collects telemetry data, which is used to help understand how to improve the product. For example, this usage data helps to debug issues, such as slow start-up times, and to prioritize new features.  While we appreciate the insights this data provides, we also know that not everyone wants to send usage data and you can disable telemetry as described in [disable telemetry reporting](#how-to-disable-telemetry-reporting). You can also read our [privacy statement](https://privacy.microsoft.com/privacystatement) to learn more.

## Types of telemetry data

Azure Data Studio and this page refer to three different types of data with respect to telemetry.

**Crash Reports** - Crash reports collect diagnostic information when Azure Data Studio crashes and sends it to Microsoft to help understand why the crash occurred and what changes are needed to prevent the crash in the future.

**Error Telemetry** - Error telemetry collects information about errors that do not crash the application but are unexpected.

**Usage Data** - Usage data collects information about how features are used and perform in Azure Data Studio which helps us prioritize future product improvements.

## How to disable telemetry reporting

Azure Data Studio collects usage data and sends it to Microsoft to help improve our products and services. Read the [Microsoft Enterprise and Developer Privacy Statement](https://privacy.microsoft.com/privacystatement) to learn more.

With the `telemetry.telemetryLevel` user setting, you can control the different types of telemetry we send with a single setting. Here is a table of the different types of data sent with each value of `telemetry.telemetryLevel`:

|       | Crash Reports         | Error Telemetry | Usage Data     |
|:------|:---------------------:|:---------------:|:--------------:|
| all   |            ✓          |        ✓        |        ✓       |
| error |            ✓          |        ✓        |        -       |
| crash |            ✓          |        -        |        -       |
| off   |            -          |        -        |        -       |

For example, if you don't want to send any telemetry data to Microsoft, you can set the `telemetry.telemetryLevel` user setting to `off`.

From **File** > **Preferences** > **Settings** (macOS: **Azure Data Studio** > **Preferences** > **Settings**), search for `telemetry`, and set the **Telemetry: Telemetry Level** setting to `off`. This will silence all telemetry events from Azure Data Studio going forward. Telemetry information may have been collected and sent up until the point when you disable the setting.

![disable telemetry](https://user-images.githubusercontent.com/28519865/220482854-1abe11f6-c9cc-4415-87dd-cd498bea29ec.png)

If you use the JSON editor for your settings, add the following line:

```json
    "telemetry.telemetryLevel": "off"
```

## Extensions and telemetry

Azure Data Studio lets you add features to the product by installing Microsoft and third-party extensions. These extensions may be collecting their own usage data and may not be controlled by the `telemetry.telemetryLevel` setting. Consult the specific extension's documentation to learn about its telemetry reporting and whether it can be disabled.

Extension authors may refer to the [Telemetry extension authors guide](https://code.visualstudio.com/api/extension-guides/telemetry) for guidance on implementing telemetry best practices within their extension.

## Output channel for telemetry events

If you'd like to review the telemetry events in Azure Data Studio as they are sent, you can enable tracing and it will record telemetry events. Using the **Developer: Set Log Level...** command and select log level **Trace**.  To view the logging output, go to the Output panel (`kb(workbench.action.output.toggleOutput)`) and pick **Telemetry** from the dropdown.

![output panel log telemetry](https://user-images.githubusercontent.com/28519865/220483405-1d9023cf-2f0f-420f-bf92-e7c97102ffe8.png)

When tracing telemetry events, the events are also logged to a local file `telemetry.log`, which you can view using the **Developer: Open Log File...** command and choosing **Telemetry** from the dropdown.

![open telemetry log file](https://user-images.githubusercontent.com/28519865/220483661-5685b270-d13b-49c5-8806-b79161a44fcb.png)

## GDPR and Azure Data Studio

In addition to supporting the General Data Protection Regulation (GDPR), the Azure Data STudio team takes privacy very seriously. That's both for Microsoft as a company and specifically within the Azure Data Studio team.

To ensure GDPR compliance, we made several updates to Azure Data Studio, these include:

* Making it easier to opt out of telemetry collection by placing a notification in product for all existing and new users.
* Reviewing and classifying the telemetry that we send.
* Ensuring that we have valid data retention policies in place for any data we do collect, for example crash dumps.

In short, we have worked hard to do the right thing, for all users, as these practices apply to all geographies, not just Europe.

One question we expect people to ask is to see the data we collect. However, we don't have a reliable way to do this as Azure Data Studio does not have a 'sign-in' experience that would uniquely identify a user.  We do send information that helps us approximate a single user for diagnostic purposes (this is based on a hash of the network adapter NIC on the desktop and a randomly assigned UUID on the web) but this is not guaranteed to be unique. For example, virtual machines (VMs) often rotate NIC IDs or allocate from a pool. This technique is sufficient to help us when working through problems, but it is not reliable enough for us to 'provide your data'.

We expect our approach to evolve as we learn more about GDPR and the expectations of our users. We greatly appreciate the data users do send to us, as it is very valuable and Azure Data Studio is a better product for everyone because of it. And again, if you are worried about privacy, we offer the ability to disable sending telemetry as described in [disable telemetry reporting](#how-to-disable-telemetry-reporting).
