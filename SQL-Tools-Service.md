# SQL Tools Service

## Introduction

Azure Data Studio uses various "backend" services to do everything. The major one of these services is the SQL Tools Service.

The [SQL Tools Service][STS] (STS) primarily enables MSSQL connections and other features such as the profiler, CMS, Schema Compare, etc. What this means is that we regularly have to create dev builds of this service and debug it in the context of ADS.

The [SQL Tools Service][STS] is used by other applications as well. So it's important to make sure when you're making changes that you don't break any contracts or APIs.

This guide assumes you've read the [wiki][STS Wiki] on the [SQL Tools Service][STS] repository.

## Developer Guide

### Using locally built binaries (for debugging/testing)

For debugging or testing local changes it is easiest to use a locally built version of STS. There are two primary ways to accomplish this

- Use the ADS_SQLTOOLSSERVICE environment variable to direct ADS to use a custom STS
- Copy over the binaries manually into the installed STS location

Both of these require a few common initial steps. Note that these steps are all for the [Microsoft.SqlTools.ServiceLayer](https://github.com/microsoft/sqltoolsservice/tree/main/src/Microsoft.SqlTools.ServiceLayer) project, which is where much of the STS logic is put. There are additional projects though such as [Microsoft.SqlTools.Credentials](https://github.com/microsoft/sqltoolsservice/tree/main/src/Microsoft.SqlTools.Credentials) and [Microsoft.SqlTools.ResourceProvider](https://github.com/microsoft/sqltoolsservice/tree/main/src/Microsoft.SqlTools.ResourceProvider). To replace those just follow the same steps but do them from those folders instead. 

### Common Initial Steps

1. Navigate to `src/Microsoft.SqlTools.ServiceLayer`
2. Run `dotnet publish` from the command line (or use the publish target in Visual Studio)

This should build and publish the project to a folder similar to `$(Root)/src/Microsoft.SqlTools.ServiceLayer/bin/Debug/$(NetCoreVersion)/publish`

#### Using ADS_SQLTOOLSSERVICE environment variable

1. In a terminal window set the `ADS_SQLTOOLSSERVICE` to the full path of the publish folder from the steps above. e.g. using powershell it would look similar to this.

`$ENV:ADS_SQLTOOLSSERVICE="C:\src\sqltoolsservice\src\Microsoft.SqlTools.ServiceLayer\bin\Debug\net5.0\publish"`
2. From the same terminal window launch Azure Data Studio. You can also open up the Azure Data Studio folder in VS Code and then use the debug launch targets there to launch ADS as normal if you're also debugging ADS. 

3. Azure Data Studio should pop up a notification indicating that it's using a custom path for STS. If this doesn't appear check you have the environment variable spelled correctly and then check the console logs to see if there were issues finding the expected EXEs

* Note that in order to publish again (for example if you have a further change you want to test out) you will need to close any running instances of ADS before publishing - otherwise the files will be locked and unable to be updated. 

#### Manually replacing binaries 

1. Close down any running instances of ADS

2. Copy over the files from `src/Microsoft.SqlTools.ServiceLayer/bin/Debug/$(NetCoreVersion)/publish` to `$(AzureDataStudioPath)/extensions/mssql/sqltoolsservice/Windows/#.#.#-release.#/` and overwrite any existing files. The AzureDataStudioPath will either be the source enlistment path or the path to the installed version of ADS, e.g. `%LOCALAPPDATA%\Programs\Azure Data Studio\resources\app`
3. Launch ADS
4. Open the STS project in VS Code or Visual Studio and :
   * VS Code - Click the debug button and press `.NET Core Attach`. Search for `MicrosoftSqlToolsServiceLayer` using the filter menu and attach to the process
   * Visual Studio - Under the debug menu choose `Attach to Process`. Search for `MicrosoftSqlToolsServiceLayer` using the filter menu and attach to the process
and 
5. You should now be able to debug STS and set breakpoints as needed

### Extra Information

- If ADS insists on downloading a new version of SQLToolsService after you've replaced the files, that means you've either not copied them properly, or you've built them for a wrong platform. Try doing a publish first, and don't delete the files that are already in the azuredatastudio path. Just overwrite them.

- If you run into an error that states that message headers must include the ':' symbol, this may be caused by Console.WriteLine() statements in your version of the SQL Tools Service code or any underlying code it refers to (ie: DacFx). In this case, removing the Console.WriteLine() statements should fix the error.

- If you're debugging multiple STS projects at the same time, you're going to need multiple STS VSCode windows as our launch configuration isn't designed to attach to multiple .NET Core Projects at the same time.

- There are a lot of ways this process can be simplified. If you have any ideas please pitch them to the team!


[STS]: https://github.com/microsoft/sqltoolsservice/
[STS Wiki]: https://github.com/microsoft/sqltoolsservice/wiki
