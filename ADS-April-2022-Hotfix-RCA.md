# RCA for ADS April 2022 – Issue 19166 Connection explorer is unable to retrieve tables' list 

## Summary

There was a regression in the ADS April 2022 release that caused timeouts expanding the Object Explorer Tables folder.  The underlying issue was that new [SMO](https://github.com/Microsoft/sqlmanagementobjects) property queries were introduced to check table type to display Graph Node and Edge table types.  These SMO properties had an unexpected side-effect of introducing a new SQL query to be executed per table on the connected database.  The Table folder expansion has a timeout of 45 seconds therefore on databases with a large number of tables or slow response times, these additional queries caused timeouts. 

## Github Artifacts
* Customer reported issue https://github.com/microsoft/azuredatastudio/issues/19166 
* Follow-up issue https://github.com/microsoft/azuredatastudio/issues/19180  
* Code change https://github.com/microsoft/sqltoolsservice/pull/1440/files 

## Customer Impact
### How was the bug discovered?
The bug was customer-reported on the day of the release.  There were 4 duplicate reports of bugs.

### What was the workaround?
The customers could downgrade to previously released to workaround the regression. If necessary, customers could retrieve similar information directly from DMVs and launch most of the same commands from Dashboard or Command Palette.

### What was the impact on customer experience? 
Customers could not use Object Explorer to view table definitions or launch table specific commands.  This is a critical and highly used aspect of the ADS UI and therefore many customers who encountered this bug would block from completing their normal workflow. 

## Technical Details
The regression causing change [added logic for node sub type](https://github.com/microsoft/sqltoolsservice/pull/1440/files) was added to support new icons for Graph Node and Graph Edge table types. This was done by adding a check for the properties “IsNode” and “IsEdge” which is received from SMO. This check was added to the expansion mechanism of all the tables in Object Explorer. 

![Screen Shot 2022-04-28 at 2 19 04 PM](https://user-images.githubusercontent.com/6411451/168689414-d285375b-2d15-430e-9dd8-5413b4b00dbc.png)

The intended change was to check the properties of the SMO Table object and return a string describing table type, so that the Object Explorer can use that table type in its metadata to show specific icons for the Graph Edge and Graph Node types, instead of always showing the Temporal table icon as default. In addition, these new icons were also being used by the Table Designer Editor tab. 

A side effect of checking these properties is running new StretchDB queries for every table to check of the IsNode and IsEdge properties in the SMO Table object. These queries were run behind the scenes by SMO to populate those properties for every table. This added an additional overhead of many new queries executed per table when expanding the tables folder in Object Explorer, since the metadata is for every node is fetched when the object is expanded in the Object Explorer. This caused an issue with servers with a large number of tables or tables with slower response times, since expanding the tables folder on those servers caused it to run a large number of new StretchDB queries, which often did not finish within 45 seconds, which is the default connection timeout period in ADS. This caused those servers to fail to expand the tables folder.  

A comparison of the events when executing the same scenario with SQL Profiler attached between the previous release 1.35.1 (February 2022 Hotfix Release 1) and release 1.36.0 (April 2022 Release) shows many new queries being run for the same scenario in the April release of ADS:

### ADS Release 1.35.1 (February 2022 Hotfix Release 1)
![MicrosoftTeams-image (1)](https://user-images.githubusercontent.com/6411451/168690346-5ca0a530-6489-4f1d-906e-bf3f15137c14.png)

### ADS Release 1.36.0 (April 2022 Release) 
![MicrosoftTeams-image (3)](https://user-images.githubusercontent.com/6411451/168690540-d4a9d0a6-1869-494f-9e79-d30c1eaacb9f.png)

## Why wasn't the bug found?
### What could have helped detect the defect at reach of the following stages?
**Dev**: The code change was adding simple property calls to SMO. The expectation was that these properties would already be available on the client-side without requiring additional metadata queries. There was a gap in understanding the underlying behavior of the SMO API and how it evaluated properties. This could have been mitigated with a deeper understanding of the SMO implementation.

The problem was readily apparent when the scenario was executed with a SQL Profiler attached to the server. Typically, we do not require SQL profiling of every STS change to commit. In this case, the change was made to code in a timed-inner loop during a critical user scenario (OE node expansion). Due to the nature of this change the precheck-in validation should have included a manual SQL profiling verification. This should have been done as best practice but has not been expected up to this point as part of the typical developer process for this project. 

**Code Review**: The code change appears relatively simple, and the performance regression is not immediately apparent from code inspection. The review would need to have an understanding of potential SMO side-effects to identify from the source code.

**CI/CD Pipelines**: We do not run automated performance tests during the CI or full build pipelines. An automated test that either: 
1. timed the expansion of a Tables folder with a large number of tables or  
2. a baseline test that detected new SQL queries executed within a particular scenario could have caught this issue.

**Dogfooding/Insiders**: The regression was introduced into the main branch on 3/22/2022, about a month before the release on 4/20/2022. This is sufficient time for Dogfooders or Insider Build users to encounter and report the issue. Since it was not reported prior to release the assumption is the insider build user base is too small or they did not have an environment that triggered this timeout. 

**Release Verification**: The issue would normally be encountered during manual testing unless the environment was configured with:
1. a database with a large number of tables sufficient to trigger a 45 second timeout through metadata queries or
2. a database with slow response times that would timeout from a smaller number of additional queries.  

We do not have these test environments available, and the test sign-off instructions do not include notes to specifically test these configurations.   

## Repair Items/Follow ups
Firstly, we still will get the [original bug](https://github.com/microsoft/azuredatastudio/issues/19180) fixed with the correct implementation where the SMO Table Objects have their SMO properties are pre-loaded, so that accessing those properties do not run any additional metadata queries in the background. In addition, there will be several new testing mechanisms that will be added:

### Automated Testing
We need to add some type of automation for this scenario. We could either add perf-type tests that run timed methods, or just run normal functional tests in preconfigured problematic environments (stress tests). Another option is to have SQL Baseline tests, which compare the queries run and the expected queries to be run for basic scenarios. 

The advantage of adding perf or stress tests is that that framework could be used for other feature areas as well. While the perf tests have an additional benefit of giving performance metrics of various connection related scenarios between releases, creating test environments with more diverse configurations will improve the variety of coverage in manual or automated tests throughout the product, increasing likelihood of “stumbling upon” bugs. The preconfigured tests could include setup scripts to setup the environment before every/any test. 

The SQL Baseline tests, however, seem to be the better option to go with in this scenario, as comparing exact expected SQL queries that are run in the background for basic scenarios such as connections and Object Explorer expansions with the actual queries that are run in a release will be able to pinpoint any regressions or behavior change with great accuracy regardless of the environment, while also working as a sanity check for our base user scenarios.

### Manual Release Testing
As the committer of Object Explorer, I will add additional detailed scenarios for Object Explorer release testing, and adding preconfigured environments will help in manual testing in addition to automated testing. Currently, the release testing for Object Explorer includes:  

1. Expansion of Server nodes by- 
    1. Auth Type: SQL Login/AAD/Windows Auth etc. 
    2. Server Type: On Prem/Azure SQL DB/DW etc. 
    3. Server Versions: 2012, 2016 etc. 
2. Expansion of Database nodes by- 
    1. Auth Type: SQL Login/AAD/Windows Auth etc. 
    2. Server Type: On Prem/Azure SQL DB/DW etc. 
    3. Server Versions: 2012, 2016 etc. 
3. Recursive expansion of the server and database nodes and their children. 
4. Context menu actions in different types of nodes. ￼ 

Some additional explicit manual tests that I’ll add to the ADS Release Testing ADO suite under Object Explorer will include running the above scenarios in: 

1. Servers with a high number (~current threshold until timeout) of databases. 
2. Databases with a high number (~current threshold until timeout) of tables. 
3. Databases with a high number of different objects and context menu actions on them. 