# Read First
During the Microsoft Ready lab you will be following these instructions on a virtual machine and should open this guide in the virtual machine so that you can copy/paste code. Open a web browser in the virtual machine and type in `https://github.com/Microsoft/azuredatastudio/wiki/Microsoft-Ready-2019-lab` to access this guide

# Introduction

Welcome to Microsoft Ready! In this session, you will learn to build your own extensions for [Azure Data Studio](https://aka.ms/azuredatastudio). Azure Data Studio is a data management tool that enables you to work with SQL Server, Azure SQL DB and SQL DW from Windows, macOS and Linux. In addition to providing core functionality like a rich T-SQL editing experience, we also provide an Extension Marketplace where the community can contribute their own **Extensions** so that anyone can use the extension.

These extensions are generally lighted up through our **Dashboard**, where users can quickly monitor and troubleshoot their databases.

![dash](https://cloudblogs.microsoft.com/uploads/prod/2018/03/whoisactive.png)

Users can build their own **Insight Widgets** as shown in the image above, which are powered by T-SQL queries. This allows a user to see a visualization of the common queries they run day to day.

Over the course of this session, you will:
- Build your first Azure Data Studio extension using a [Visual Studio Code](https://code.visualstudio.com/) environment.
- Use an [Extension generator](https://github.com/llali/generator-sqlops) to help write an extension
- Create an Insight Widget extension

## Prerequisites
All prerequisites are already install on machine. As a summary, here is what is pre-installed:
- [Azure Data Studio](https://aka.ms/azuredatastudio)
- [Visual Studio Code](https://code.visualstudio.com/)
- [Git](https://git-scm.com)
- [Node.JS](https://nodejs.org/en/), `>= 8.9.1, < 9.0.0`
- [Yarn](https://yarnpkg.com/en/), follow the [installation guide](https://yarnpkg.com/en/docs/install)
- [Python](https://www.python.org/downloads/), at least version 2.7 (version 3 is __*not*__ supported)
- [Yeoman](http://yeoman.io), `npm install -g yo`
- [Generator-sqlops](https://github.com/llali/generator-sqlops), `npm install -g generator-sqlops`
- [SQL Server 2017 developer edition](https://www.microsoft.com/en-us/sql-server/sql-server-downloads)
- Visual Studio Code extensions
    - [SQL Operations Studio debug](https://marketplace.visualstudio.com/items?itemName=ms-mssql.sqlops-debug)
    - mssql extension
    - Debugger for Chrome
- npm packages
    - npm install -g vsce

## Create an insight widget
1. Open Azure Data Studio.
2. Click new connection.
3. Select **Localhost** from **Saved Locations** and click **Connect.**
4. Click arrow next to localhost under Servers, then click arrow by databases, then **right click** WideWorldImporters and click **New Query**
5. Copy and paste this snippet
    ```sql
    ------------------------------Data file size----------------------------
    declare @dbsize table
    (Dbname nvarchar(128),
        file_Size_MB decimal(20,2)default (0),
        Space_Used_MB decimal(20,2)default (0),
        Free_Space_MB decimal(20,2) default (0))
    insert into @dbsize
        (Dbname,file_Size_MB,Space_Used_MB,Free_Space_MB)
    exec sp_MSforeachdb
    'use [?];
    select DB_NAME() AS DbName,
    sum(size)/128.0 AS File_Size_MB,
    sum(CAST(FILEPROPERTY(name, ''SpaceUsed'') AS INT))/128.0 as Space_Used_MB,
    SUM( size)/128.0 - sum(CAST(FILEPROPERTY(name,''SpaceUsed'') AS INT))/128.0 AS Free_Space_MB
    from sys.database_files  where type=0 group by type'
    -------------------log size--------------------------------------
    declare @logsize table
    (Dbname nvarchar(128),
        Log_File_Size_MB decimal(20,2)default (0),
        log_Space_Used_MB decimal(20,2)default (0),
        log_Free_Space_MB decimal(20,2)default (0))
    insert into @logsize
        (Dbname,Log_File_Size_MB,log_Space_Used_MB,log_Free_Space_MB)
    exec sp_MSforeachdb
    'use [?];
    select DB_NAME() AS DbName,
    sum(size)/128.0 AS Log_File_Size_MB,
    sum(CAST(FILEPROPERTY(name, ''SpaceUsed'') AS INT))/128.0 as log_Space_Used_MB,
    SUM( size)/128.0 - sum(CAST(FILEPROPERTY(name,''SpaceUsed'') AS INT))/128.0 AS log_Free_Space_MB
    from sys.database_files  where type=1 group by type'
    --------------------------------database free size
    declare @dbfreesize table
    (name nvarchar(128),
        database_size varchar(50),
        Freespace varchar(50)default (0.00))
    insert into @dbfreesize
        (name,database_size,Freespace)
    exec sp_MSforeachdb
    'use [?];SELECT database_name = db_name()
        ,database_size = ltrim(str((convert(DECIMAL(15, 2), dbsize) + convert(DECIMAL(15, 2), logsize)) * 8192 / 1048576, 15, 2) + ''MB'')
        ,''unallocated space'' = ltrim(str((
                    CASE
                        WHEN dbsize >= reservedpages
                            THEN (convert(DECIMAL(15, 2), dbsize) - convert(DECIMAL(15, 2), reservedpages)) * 8192 / 1048576
                        ELSE 0
                        END
                    ), 15, 2) + '' MB'')
    FROM (
        SELECT dbsize = sum(convert(BIGINT, CASE
                        WHEN type = 0
                            THEN size
                        ELSE 0
                        END))
            ,logsize = sum(convert(BIGINT, CASE
                        WHEN type <> 0
                            THEN size
                        ELSE 0
                        END))
        FROM sys.database_files
    ) AS files
    ,(
        SELECT reservedpages = sum(a.total_pages)
            ,usedpages = sum(a.used_pages)
            ,pages = sum(CASE
                    WHEN it.internal_type IN (
                            202
                            ,204
                            ,211
                            ,212
                            ,213
                            ,214
                            ,215
                            ,216
                            )
                        THEN 0
                    WHEN a.type <> 1
                        THEN a.used_pages
                    WHEN p.index_id < 2
                        THEN a.data_pages
                    ELSE 0
                    END)
        FROM sys.partitions p
        INNER JOIN sys.allocation_units a
            ON p.partition_id = a.container_id
        LEFT JOIN sys.internal_tables it
            ON p.object_id = it.object_id
    ) AS partitions'
    -----------------------------------
    select TOP 10
        d.Dbname,
        --(file_size_mb + log_file_size_mb) as DBsize,
        --d.file_Size_MB,
        d.Space_Used_MB,
        --d.Free_Space_MB,
        --l.Log_File_Size_MB,
        l.log_Space_Used_MB--,
        --l.log_Free_Space_MB,
        --fs.Freespace as DB_Freespace
    from @dbsize d join @logsize l on d.Dbname=l.Dbname join @dbfreesize fs on d.Dbname=fs.name
    order by d.Space_Used_MB DESC
    ```

6. Click **File->Save As** and save the sql file with the name **sample.sql** to Documents.
7. Click **Run** on top left of query window.
8. Click **View as Chart** Icon on right of Results window.
9. Set Chart Type as **horizontalBar** and Data Direction to **Vertical**
9. Click **Create Insight**.
10. Edit name as 'DB Space'
11. Select the whole JSON snippet and **Copy** all of it.
12. Click bottom-left gear icon, then **Settings**
13. Under **User Settings** on the right, find the **second** to last '}'. Add a comma and new line, then copy and paste your insight widget.:
    ```json
    "server.database.widgets": [
        {
            "name": "DB Space",
            "gridItemConfig": {
                "sizex": 2,
                "sizey": 1
            },
            "widget": {
                "insights-widget": {
                    "type": {
                        "horizontalBar": {
                            "dataDirection": "vertical",
                            "dataType": "number",
                            "legendPosition": "none",
                            "labelFirstColumn": false,
                            "columnsAsLabels": false
                        }
                    },
                    "queryFile": "C:\\Users\\LabUser\\Documents\\sample.sql"
                }
            }
        }
    ]
    ```
14. Hit **Ctrl+S** to save your settings file
15. Right click **localhost** and click **Manage.** You should now be able to see the table spaces widget! 

## Create an insight extension
1. Open **Visual Studio Code** from the bottom task bar.
2. If the terminal is not already open, Hit Ctrl+` to open the Integrated Terminal **Note:** This is not the apostrophe, this is the grave accent below the ESC key.
3. Type ```yo sqlops``` and hit Enter. This opens the SQL Operations Studio Extension generator. This allows you to quickly get started on building an extension of your choice. 
4. To start off, use arrow keys and hit enter on **New Dashboard Insight.**
5. Follow these instructions:
    - Add a full dashboard tab? **Y**
    - What's the name of your extension? **sample**
    - What's the identifier of the extension? *hit enter*
    - What's the description of your extension? **building a sample extension**
    - What's your publisher name? *MSReady*
        - The publish name must be set, we recommend *MSReady* but you can use any publisher name.
     
6. You have now created a very simple extension. Now let's edit it.
7. Type ```cd sample``` into the terminal and hit enter.
8. Type ```code .``` into the terminal and hit enter. This opens a new VS Code window, and shows the folder contents of what is currently in the Sample directory.
9. Click **sql** and open **query.sql** to see our target insight extension. 
10. Select and delete what is currently in **query.sql.**
11. Paste the following T-SQL:
    ```sql
    ------------------------------Data file size----------------------------
    declare @dbsize table
    (Dbname nvarchar(128),
        file_Size_MB decimal(20,2)default (0),
        Space_Used_MB decimal(20,2)default (0),
        Free_Space_MB decimal(20,2) default (0))
    insert into @dbsize
        (Dbname,file_Size_MB,Space_Used_MB,Free_Space_MB)
    exec sp_MSforeachdb
    'use [?];
    select DB_NAME() AS DbName,
    sum(size)/128.0 AS File_Size_MB,
    sum(CAST(FILEPROPERTY(name, ''SpaceUsed'') AS INT))/128.0 as Space_Used_MB,
    SUM( size)/128.0 - sum(CAST(FILEPROPERTY(name,''SpaceUsed'') AS INT))/128.0 AS Free_Space_MB
    from sys.database_files  where type=0 group by type'
    -------------------log size--------------------------------------
    declare @logsize table
    (Dbname nvarchar(128),
        Log_File_Size_MB decimal(20,2)default (0),
        log_Space_Used_MB decimal(20,2)default (0),
        log_Free_Space_MB decimal(20,2)default (0))
    insert into @logsize
        (Dbname,Log_File_Size_MB,log_Space_Used_MB,log_Free_Space_MB)
    exec sp_MSforeachdb
    'use [?];
    select DB_NAME() AS DbName,
    sum(size)/128.0 AS Log_File_Size_MB,
    sum(CAST(FILEPROPERTY(name, ''SpaceUsed'') AS INT))/128.0 as log_Space_Used_MB,
    SUM( size)/128.0 - sum(CAST(FILEPROPERTY(name,''SpaceUsed'') AS INT))/128.0 AS log_Free_Space_MB
    from sys.database_files  where type=1 group by type'
    --------------------------------database free size
    declare @dbfreesize table
    (name nvarchar(128),
        database_size varchar(50),
        Freespace varchar(50)default (0.00))
    insert into @dbfreesize
        (name,database_size,Freespace)
    exec sp_MSforeachdb
    'use [?];SELECT database_name = db_name()
        ,database_size = ltrim(str((convert(DECIMAL(15, 2), dbsize) + convert(DECIMAL(15, 2), logsize)) * 8192 / 1048576, 15, 2) + ''MB'')
        ,''unallocated space'' = ltrim(str((
                    CASE
                        WHEN dbsize >= reservedpages
                            THEN (convert(DECIMAL(15, 2), dbsize) - convert(DECIMAL(15, 2), reservedpages)) * 8192 / 1048576
                        ELSE 0
                        END
                    ), 15, 2) + '' MB'')
    FROM (
        SELECT dbsize = sum(convert(BIGINT, CASE
                        WHEN type = 0
                            THEN size
                        ELSE 0
                        END))
            ,logsize = sum(convert(BIGINT, CASE
                        WHEN type <> 0
                            THEN size
                        ELSE 0
                        END))
        FROM sys.database_files
    ) AS files
    ,(
        SELECT reservedpages = sum(a.total_pages)
            ,usedpages = sum(a.used_pages)
            ,pages = sum(CASE
                    WHEN it.internal_type IN (
                            202
                            ,204
                            ,211
                            ,212
                            ,213
                            ,214
                            ,215
                            ,216
                            )
                        THEN 0
                    WHEN a.type <> 1
                        THEN a.used_pages
                    WHEN p.index_id < 2
                        THEN a.data_pages
                    ELSE 0
                    END)
        FROM sys.partitions p
        INNER JOIN sys.allocation_units a
            ON p.partition_id = a.container_id
        LEFT JOIN sys.internal_tables it
            ON p.object_id = it.object_id
    ) AS partitions'
    -----------------------------------
    select TOP 10
        d.Dbname,
        --(file_size_mb + log_file_size_mb) as DBsize,
        --d.file_Size_MB,
        d.Space_Used_MB,
        --d.Free_Space_MB,
        --l.Log_File_Size_MB,
        l.log_Space_Used_MB--,
        --l.log_Free_Space_MB,
        --fs.Freespace as DB_Freespace
    from @dbsize d join @logsize l on d.Dbname=l.Dbname join @dbfreesize fs on d.Dbname=fs.name
    order by d.Space_Used_MB DESC

    ```
12. Open **package.json.** Under **contributes** and **dashboard.insights, change bar to **horizontalbar** and datadirection from horizontal to **vertical**.

If you are completely lost at this point, replace package.json contents with:

    {
    "name": "sample",
    "displayName": "sample",
    "description": "building a sample extension",
    "version": "0.0.1",
    "publisher": "msignite",
    "engines": {
        "vscode": "^1.27.0",
        "sqlops": "*"
    },
    "categories": [
        "Other"
    ],
    "contributes": {
        "dashboard.insights": [
            {
                "id": "sample.insight",
                "contrib": {
                    "queryFile": "./sql/query.sql",
                    "type": {
                        "horizontalBar": {
                            "dataDirection": "vertical",
                            "dataType": "number",
                            "legendPosition": "none",
                            "labelFirstColumn": false,
                            "columnsAsLabels": true
                        }
                    }
                }
            }
        ]
        ,
        "dashboard.tabs": [
            {
                "id": "sample.tab",
                "title": "sample",
                "description": "",
                "container": {
                    "widgets-container": [
                        {
                            "name": "sample",
                            "gridItemConfig": {
                                "sizex": 2,
                                "sizey": 1
                            },
                            "widget": {
                                "sample.insight": {}
                            }
                        }
                    ]
                }
            }
        ]
    }
    }    
    
12. Open **README.md** and delete the contents inside the readme. Paste the following:

    ```
    Sample insight widget extension.
    ```
13. Click **File->Save All**. 
13. Hit CTRL+` to open the integrated terminal. Type 'vsce package' to package your extension. Type 'Y' and enter when prompted if you would like to continue without the repository field.
14. Use “Ctrl + C” to copy the file location of the vsix package from the terminal.
15. Open **Azure Data Studio.** Click **File**, then click **Install Extension from VSIX package.**
16. Paste directory link of extension and click **Install.** If you can't find it, try pasting: **C:\Users\LabUser\sample\sample-0.0.1**
17. Click **Yes** to third-party extensions on bottom-right, and **Reload Now** on bottom right.
18. Click on localhost to connect. Then right-click on localhost and click **Manage**
19. On the line next to **Home,** click **Sample** to see your sample extension.
20. Let's try adding multiple insight widgets. Go back to **Visual Studio Code** and add a new file under sql folder called **query2.sql**
21. Paste the following query.

    **Top Active Connections**
    ```sql
    SELECT count(session_id) as [Active Sessions]
    FROM sys.dm_exec_sessions
    WHERE status = 'running'
    ```

22. Now, go to package.json file so that we can create a new container for our widget. Find *dashboard.insights*.
23. Type or paste the following below the existing insight json content:
    ```json
    {
        "id": "sample2.insight",
        "contrib": {
            "queryFile":"./sql/query2.sql",
            "type": {    
                "count": {
                    "dataDirection": "vertical",
                    "dataType": "number",
                    "legendPosition": "none",
                    "labelFirstColumn": false
                }
            }
        }
    }
    ```
24. Find *widgets-container.* Type or paste the following:
    ```json
    {
        "name": "sample2",
        "gridItemConfig": {
            "sizex": 2,
            "sizey": 1
        },
        "widget": {
            "sample2.insight": {}
        }
    }
    ```
25. Repeat Steps 13-19 and remember to Save All.
26. Using what you have learned, create two additional insight widgets using the following queries.

    **DB Buffer Usage**
    (Use horizontalBar and vertical datadirection)
    ```sql
    -- source:https://www.mssqltips.com/sqlservertip/2393/determine-sql-server-memory-use-by-database-and-object/
    -- Use for a demo/sample purpose only. This query is not built-in to any product.

    DECLARE @total_buffer INT;

    SELECT @total_buffer = cntr_value
    FROM sys.dm_os_performance_counters 
    WHERE RTRIM([object_name]) LIKE '%Buffer Manager'
    AND counter_name = 'Database Pages';

    ;WITH src AS
    (
    SELECT 
    database_id, db_buffer_pages = COUNT_BIG(*)
    FROM sys.dm_os_buffer_descriptors
    --WHERE database_id BETWEEN 5 AND 32766
    GROUP BY database_id
    )
    SELECT TOP 10 
    [db_name] = CASE [database_id] WHEN 32767 
    THEN 'Resource DB' 
    ELSE DB_NAME([database_id]) END,
    --db_buffer_pages,
    --db_buffer_MB = db_buffer_pages / 128,
    db_buffer_percent = CONVERT(DECIMAL(6,3), 
    db_buffer_pages * 100.0 / @total_buffer)
    FROM src
    --ORDER BY db_buffer_MB DESC; 
    order by db_buffer_percent DESC;
    ```

## Next Steps
Thank you for attending this Microsoft Ready session. Now that you have learned to build your own Azure Data Studio extensions, we encourage you to continue to build extensions and contribute to our Extensions Marketplace.

To learn to build your own extensions:
- Get the [prerequisites](https://github.com/microsoft/sqlopsstudio/wiki/How-to-Contribute)
- Follow along on our [Github Wiki Get Started](https://github.com/microsoft/azuredatastudio/wiki/Getting-started-with-Extensibility)  
- Read these blog posts:
    - https://medium.com/@kevcunnane/writing-a-sql-operations-studio-extension-in-15-minutes-7dfd24a74dfe 
    - https://medium.com/@kevcunnane/publishing-an-extension-for-sql-operations-studio-f5a5b323c13b 

Want to learn more about Azure Data Studio?
- Leave a star on our [Github](https://github.com/microsoft/azuredatastudio)
- Download [Azure Data Studio](https://aka.ms/azuredatastudio)
- Report Issues or suggest Feature Requests on our [Github Issues page](https://github.com/microsoft/sqlopsstudio/issues)
- Follow us on Twitter [@azuredatastudio](https://twitter.com/azuredatastudio)