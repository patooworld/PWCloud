# SQL Tools Service

## Introduction

Azure Data Studio uses various "backend" services to do everything. The major one of these services is the SQL Tools Service.

The [SQL Tools Service][STS] (STS) primarily enables MSSQL connections and other features such as the profiler, CMS, Schema Compare, etc. What this means is that we regularly have to create dev builds of this service and debug it in the context of ADS.

The [SQL Tools Service][STS] is used by other applications as well. So it's important to make sure when you're making changes that you don't break any contracts or APIs.

This guide assumes you've read the [wiki][STS Wiki] on the [SQL Tools Service][STS] repository.

## Developer Guide

We're going to be talking about changing the ServiceLayer, this is where most of the "feature" code lives. This guide is applicable to any of the other projects in STS.

- Launch a dev build of ADS once (read the other wiki pages for guides on getting started with ADS)
- Navigate to `src/Microsoft.SqlTools.ServiceLayer`
- `dotnet build` or `dotnet publish -r win7-x64` (assuming you're testing this on windows)
- Copy over the files from `src/Microsoft.SqlTools.ServiceLayer/bin/Debug/netcoreapp#.#/` to `azuredatastudio/extensions/mssql/sqltoolsservice/Windows/#.#.#-release.#/` and overwrite any existing files.

    Note: The from path changes based on if you're doing a build or publish. The publish path is: `src/Microsoft.SqlTools.ServiceLayer/bin/Debug/netcoreapp#.#/win7-x64/publish/`. A publish step brings in all the other various system dependencies along side the code you've written. So it's recommended to start off by doing a publish and then you can continue by just using builds.

- Start your ADS dev build that you added the new binaries to.
- Open your VSCode STS project and click the debug button and press `.NET Core Attach`. Look for `ServiceLayer` and attach to the project.
- Place breakpoints where you're interested and make ADS invoke that piece of code.

### Extra Information

- If ADS insists on downloading a new version of SQLToolsService after you've replaced the files, that means you've either not copied them properly, or you've built them for a wrong platform. Try doing a publish first, and don't delete the files that are already in the azuredatastudio path. Just overwrite them.

- If you're debugging multiple STS projects at the same time, you're going to need multiple STS VSCode windows as our launch configuration isn't designed to attach to multiple .NET Core Projects at the same time.

- There are a lot of ways this process can be simplified. If you have any ideas please pitch them to the team!


[STS]: https://github.com/microsoft/sqltoolsservice/
[STS Wiki]: https://github.com/microsoft/sqltoolsservice/wiki
