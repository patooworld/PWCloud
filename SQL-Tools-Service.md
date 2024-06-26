# SQL Tools Service

## Introduction

Azure Data Studio uses various "backend" services to do everything. The major one of these services is the SQL Tools Service.

The [SQL Tools Service][STS] (STS) primarily enables MSSQL connections and other features such as the profiler, CMS, Schema Compare, etc. What this means is that we regularly have to create dev builds of this service and debug it in the context of ADS.

The [SQL Tools Service][STS] is used by other applications as well. So it's important to make sure when you're making changes that you don't break any contracts or APIs.

This guide assumes you've read the [wiki][STS Wiki] on the [SQL Tools Service][STS] repository.

## Developer Guide

### Using locally built binaries (for debugging/testing)

For debugging or testing local changes it is easiest to use a locally built version of STS. You can do this by using the `ADS_SQLTOOLSSERVICE` environment variable to direct ADS to use a custom STS

Follow the instructions below to set this up. Note that these steps are all for the [Microsoft.SqlTools.ServiceLayer](https://github.com/microsoft/sqltoolsservice/tree/main/src/Microsoft.SqlTools.ServiceLayer) project, which is where much of the STS logic is put. There are additional projects though such as [Microsoft.SqlTools.Credentials](https://github.com/microsoft/sqltoolsservice/tree/main/src/Microsoft.SqlTools.Credentials) and [Microsoft.SqlTools.ResourceProvider](https://github.com/microsoft/sqltoolsservice/tree/main/src/Microsoft.SqlTools.ResourceProvider). To replace those just follow the same steps but do them from those folders instead. 

#### Steps

1. Navigate to `src/Microsoft.SqlTools.ServiceLayer`
2. Run `dotnet publish` from the command line (or use the publish target in Visual Studio)

This should build and publish the project to a folder similar to `$(Root)/src/Microsoft.SqlTools.ServiceLayer/bin/Debug/$(NetCoreVersion)/publish`

   * Note: Be sure to exit any instances of ADS that are using STS from the publish location before building, otherwise the files will be locked and unable to be overwritten. If that happens close all instances of ADS and then run `dotnet publish --force` to force a republish. 

3. In a terminal window set the `ADS_SQLTOOLSSERVICE` to the full path of the publish folder from the steps above. e.g. using powershell it would look similar to this.

`$ENV:ADS_SQLTOOLSSERVICE="C:\src\sqltoolsservice\src\Microsoft.SqlTools.ServiceLayer\bin\Debug\net7.0\publish"`

4. From the same terminal window launch Azure Data Studio. You can also open up the Azure Data Studio folder in VS Code and then use the debug launch targets there to launch ADS as normal if you're also debugging ADS. 

   * NOTE Make sure that wherever you set the environment variable that you launch ADS from the same environment. e.g. if you set it in a terminal window then you either launch ADS from that same window or launch VS Code from that window and launch ADS from that VS Code instance

5. Azure Data Studio should pop up a notification indicating that it's using a custom path for STS. If this doesn't appear check you have the environment variable spelled correctly and then check the console logs to see if there were issues finding the expected EXEs

   * Note that in order to publish again (for example if you have a further change you want to test out) you will need to close any running instances of ADS before publishing - otherwise the files will be locked and unable to be updated. 

6. Open the STS project in VS Code or Visual Studio and :

   * VS Code - Click the debug button and press `.NET Core Attach`. Search for `MicrosoftSqlToolsServiceLayer` using the filter menu and attach to the process
   * Visual Studio - Under the debug menu choose `Attach to Process`. Search for `MicrosoftSqlToolsServiceLayer` using the filter menu and attach to the process

7. You should now be able to debug STS and set breakpoints as needed

### Troubleshooting Information

* If you aren't seeing your changes or are attaching a debugger to STS and the symbols aren't loading (`Source Code is different` error), then check these things
   * You should see a toast notification on startup of ADS telling you what STS it's using if there's an override specified - if you don't see that then you don't have the environment variable set up correctly. See the note above and double check you set it in the correct environment
   * Verify that you're using the latest version of the built DLLs - see the note above for more information and how to rebuild if they are out of date
* If you run into an error that states that message headers must include the ':' symbol, this may be caused by Console.WriteLine() statements in your version of the SQL Tools Service code or any underlying code it refers to (ie: DacFx). In this case, removing the Console.WriteLine() statements should fix the error.

* If you're debugging multiple STS projects at the same time, you're going to need multiple STS VSCode windows as our launch configuration isn't designed to attach to multiple .NET Core Projects at the same time.


[STS]: https://github.com/microsoft/sqltoolsservice/
[STS Wiki]: https://github.com/microsoft/sqltoolsservice/wiki
