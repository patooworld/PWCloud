# Introduction

Welcome to Microsoft Build! In this session, you will learn to build your own extensions for [SQL Operations Studio](https://aka.ms/sqlopsstudio). SQL Operations Studio is a data management tool that enables you to work with SQL Server, Azure SQL DB and SQL DW from Windows, macOS and Linux. In addition to providing core functionality like a rich T-SQL editing experience, we also provide an Extension Marketplace where the community can contribute their own **Extensions** so that anyone can use the extension.

These extensions are generally lighted up through our **Dashboard**, where users can quickly monitor and troubleshoot their databases.

![dash](https://cloudblogs.microsoft.com/uploads/prod/2018/03/whoisactive.png)

Users can build their own **Insight Widgets** as shown in the image above, which are powered by T-SQL queries. This allows a user to see a visualization of the common queries they run day to day.

Over the course of this session, you will:
- Build your first sqlops extension using a [Visual Studio Code](https://code.visualstudio.com/) environment.
- Use an [Extension generator](https://github.com/llali/generator-sqlops) to help write an extension
- Create an Insight Widget extension
- Create an extension using Typescript

## Create an insight widget
1. Open SQL Operations Studio.
2. Click new connection.
3. Click **Localhost** and click **Connect**
4. Click arrow next to localhost under Servers, then click arrow by databases, then **right click** AdventureWorks2014 and click **New Query**
5. Copy and paste this snippet
    ```sql
    -- Get the space used by table TableName
    SELECT TOP 10 tabl.name AS table_name,
    --SUM(PART.rows) AS rows_count,
    SUM(ALOC.total_pages) AS total_pages,
    SUM(ALOC.used_pages) AS used_pages,
    SUM(ALOC.data_pages) AS data_pages,
    (SUM(ALOC.total_pages)*8/1024) AS total_space_MB,
    (SUM(ALOC.used_pages)*8/1024) AS used_space_MB,
    (SUM(ALOC.data_pages)*8/1024) AS data_space_MB
    FROM sys.tables AS TABL
    INNER JOIN sys.indexes AS INDX
    ON TABL.object_id = INDX.object_id
    INNER JOIN sys.partitions AS PART
    ON INDX.object_id = PART.object_id
    AND INDX.index_id = PART.index_id
    INNER JOIN sys.allocation_units AS ALOC
    ON PART.partition_id = ALOC.container_id
    --WHERE TABL.name LIKE '%TableName%'
    AND INDX.object_id > 255
    AND INDX.index_id <= 1
    GROUP BY TABL.name, 
    INDX.object_id,
    INDX.index_id,
    INDX.name
    ORDER BY --Object_Name(INDX.object_id),
    (SUM(ALOC.total_pages)*8/1024) DESC
    GO
    ```
6. Click File->Save As:
    ```
    C:\Users\LabUser\.sqlops\sample.sql
    ```
7. Click Run.
8. Click **View as Chart** Icon on right of Results window.
9. Click **Create Insight**.
10. Edit name as 'Table Spaces'
11. Edit queryfile as 'C:\Users\LabUser\.sqlops\sample.sql'
12. Click bottom-left gear icon, then **Settings**
13. Under **User Settings**, find the last ']'. Add a comma and new line, then copy and paste your insight widget:
    ```json
    "dashboard.database.widgets": [
        {
            "name": "Table Spaces",
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
                    "queryFile": "C:\\Users\\LabUser\\.sqlops\\sample.sql"
                }
            }
        }
    ]
    ```
14. Hit Ctrl+S to save your settings file
15. Right click **AdventureWorks2014** under servers and click **Manage.** You should now be able to see the table spaces widget! 

## Create an insight extension
1. Open **Visual Studio Code** from the bottom task bar.
2. Hit Ctrl+` to open the Integrated Terminal **Note:** This is not the apostrophe, this is the grave accent below the ESC key. 
3. Type ```yo sqlops``` and hit Enter. This opens the SQL Operations Studio Extension generator. This allows you to quickly get started on building an extension of your choice. 
4. To start off, use arrow keys and hit enter on **New Dashboard Insight.**
5. Follow these instructions:
    - Add a full dashboard tab? **Y**
    - What's the name of your extension? **sample**
    - What's the identifier of the extension? *hit enter*
    - What's the description of your extension? **building a sample extension**
    - What's your publisher name? *hit enter*
     
6. You have now created a very simple extension. Now let's edit it.
7. Type ```cd sample``` into the terminal and hit enter.
8. Type ```code .``` into the terminal and hit enter. This opens a new VS Code window, and shows the folder contents of what is currently in the Sample directory.
9. Click **sql** and open **query.sql** to see our target insight extension. 
10. Select and delete what is currently in **query.sql.**
11. Paste the following T-SQL:
    ```sql
    -- Get the space used by table TableName
    SELECT TOP 10 tabl.name AS table_name,
    --SUM(PART.rows) AS rows_count,
    SUM(ALOC.total_pages) AS total_pages,
    SUM(ALOC.used_pages) AS used_pages,
    SUM(ALOC.data_pages) AS data_pages,
    (SUM(ALOC.total_pages)*8/1024) AS total_space_MB,
    (SUM(ALOC.used_pages)*8/1024) AS used_space_MB,
    (SUM(ALOC.data_pages)*8/1024) AS data_space_MB
    FROM sys.tables AS TABL
    INNER JOIN sys.indexes AS INDX
    ON TABL.object_id = INDX.object_id
    INNER JOIN sys.partitions AS PART
    ON INDX.object_id = PART.object_id
    AND INDX.index_id = PART.index_id
    INNER JOIN sys.allocation_units AS ALOC
    ON PART.partition_id = ALOC.container_id
    --WHERE TABL.name LIKE '%TableName%'
    AND INDX.object_id > 255
    AND INDX.index_id <= 1
    GROUP BY TABL.name, 
    INDX.object_id,
    INDX.index_id,
    INDX.name
    ORDER BY --Object_Name(INDX.object_id),
    (SUM(ALOC.total_pages)*8/1024) DESC
    GO
    ```    
12. Open **README.md** and delete the contents of the readme. Paste the following:
    ```
    Sample insight widget extension.
    ```
13. Hit CTRL+` to open the integrated terminal. Type 'vsce package' to package your extension.
14. Copy the directory link of your extension.
15. Open **SQL Operations Studio.** Click **File**, then click **Install Extension from VSIX package.**
16. Paste directory link of extension and click **Install**
17. Click **Reload Now** on bottom right.
18. Click on arrow next to **Localhost**, arrow next to **Databases**, then right click **AdventureWorks2014** and click **Manage**
19. On the line next to **Home,** click **Sample** to see your sample extension.
20. (Optional) Try adding these insight widgets following Steps 9-20. Note: You can add new .sql files under SQL folder to have multiple widgets.

    **Top Active Connections**
    ```sql
    SELECT count(session_id) as [Active Sessions]
    FROM sys.dm_exec_sessions
    WHERE status = 'running'
    ```

    **DB Size**
    ```sql
    with fs
    as
    (
        select database_id, type, size * 8.0 / 1024 size
        from sys.master_files
    )
    select 
        name,
        (select sum(size) from fs where type = 0 and fs.database_id = db.database_id) DataFileSizeMB,
        (select sum(size) from fs where type = 1 and fs.database_id = db.database_id) LogFileSizeMB
    from sys.databases db
    where database_id > 4
    ```

    **All DB Space Used**
    ```sql
    declare @dbsize table
    (Dbname nvarchar(128),
        file_Size_MB decimal(20,2)default (0),
        Space_Used_MB decimal(20,2)default (0),
        Free_Space_MB decimal(20,2) default (0))
    insert into @dbsize
        (Dbname,file_Size_MB,Space_Used_MB,Free_Space_MB)
    exec sp_msforeachdb
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
    exec sp_msforeachdb
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
    exec sp_msforeachdb
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

## Create an extension using Typescript
1. Hit Ctrl+` to open the Integrated Terminal **Note:** This is not the apostrophe, this is the grave accent below the ESC key. 
1. Type ```yo sqlops``` and hit Enter. This opens the SQL Operations Studio Extension generator. 
1. To start off, use arrow keys and hit enter on **New Extension (Typescript)**
1. Follow these instructions:
    - What's the name of your extension? **TypeSample**
    - What's the identifier of the extension? *hit enter*
    - What's the description of your extension? **TypeScript extension**
    - What's your publisher name? msbuild
    - Enable stricter Typescript checking? **Y**
    - Setup linting using 'tslint'? **Y**
    - Initialize a git repository? **n**

1. Wait for npm install to finish.
1. Open Integrated Terminal with **Ctrl+`** and run ```npm run compile``` This will create a new **out** folder.
1. Right click on the **out** folder and create a new folder called **sql**. This is where we will store .sql files to power our insight widgets.
1. Right click on the **sql** folder and click **New File.** Name it *query.sql*
1. Open *query.sql* and paste the following familiar sql query:
1. Paste the following T-SQL:
    ```sql
    -- Get the space used by table TableName
    SELECT TOP 10 tabl.name AS table_name,
    --SUM(PART.rows) AS rows_count,
    SUM(ALOC.total_pages) AS total_pages,
    SUM(ALOC.used_pages) AS used_pages,
    SUM(ALOC.data_pages) AS data_pages,
    (SUM(ALOC.total_pages)*8/1024) AS total_space_MB,
    (SUM(ALOC.used_pages)*8/1024) AS used_space_MB,
    (SUM(ALOC.data_pages)*8/1024) AS data_space_MB
    FROM sys.tables AS TABL
    INNER JOIN sys.indexes AS INDX
    ON TABL.object_id = INDX.object_id
    INNER JOIN sys.partitions AS PART
    ON INDX.object_id = PART.object_id
    AND INDX.index_id = PART.index_id
    INNER JOIN sys.allocation_units AS ALOC
    ON PART.partition_id = ALOC.container_id
    --WHERE TABL.name LIKE '%TableName%'
    AND INDX.object_id > 255
    AND INDX.index_id <= 1
    GROUP BY TABL.name, 
    INDX.object_id,
    INDX.index_id,
    INDX.name
    ORDER BY --Object_Name(INDX.object_id),
    (SUM(ALOC.total_pages)*8/1024) DESC
    GO
    ```        
1. Save this query with Ctrl+S.
1. In package.json, find **activationEvents** and remove the contents in that section (two lines). Type ```"*"``` in **activationEvents** section.
1. In package.json 
    Add command to contributes.commands. **Note:** Ensure to add a comma at } bracket before.
    ```json
        {
        "command": "TypeInsight.simple.install",
        "title": "TypeInsight: install"
        }
    ```

1. In package.json, paste or type this snippet below in Contributes section.
    ```json 
    ],
        "dashboard.tabs": [
        {
        "id": "TypeInsight-simple",
        "title": "TypeInsight",
        "description": "Extension for checking who is active.",
        "container": {
            "nav-section": [
            {
                "id": "TypeInsight_insights",
                "title": "Insights",
                "container": {
                "TypeInsight-simple-insights": {}
                }
            },
            {
                "id": "TypeInsight_documentation",
                "title": "Documentation",
                "container": {
                "TypeInsight-simple-insights3": {}
                }
            }
            ]
        }
        }
    ],
    "dashboard.containers": [
        {
        "id": "TypeInsight-simple-insights",
        "container": {
            "widgets-container": [
            
            {
                "name": "Top 10 CPU Usage",
                "gridItemConfig": {
                "sizex": 2,
                "sizey": 1
                },
                "widget": {
                "TypeInsight-simple-CPU-usage": {}
                }
            }
            ]
        }
        },
        {
        "id": "TypeInsight-simple-insights3",
        "container": {
            "widgets-container": [
            {
                "name": "Tasks",
                "widget": {
                "tasks-widget": [
                    "TypeInsight.simple.install"
                ]
                }
            }
            ]
        }
        }
    ],
    "dashboard.insights": [
        {
        "id": "TypeInsight-simple-CPU-usage",
        "contrib": {
            "type": {
            "bar": {
                "dataDirection": "vertical",
                "dataType": "number",
                "legendPosition": "none",
                "labelFirstColumn": false,
                "columnsAsLabels": true,
                "showTopNData": 10
            }
            },
            "queryFile": "./out/sql/query.sql"
        }
        }
    ]
    },      
    ```

1. Dropdown **src** and open **extension.ts**, add the following line at the bottom of **activate** function to activate method.
    ```typescript
    sqlops.tasks.registerTask('TypeInsight.simple.install', e => vscode.window.showInformationMessage('test!'));
    ```
2. Run ```npm run compile```
3. Remove everything from readme.md
4. Hit CTRL+` to open the integrated terminal. Type 'vsce package' to package your extension.
5. Copy the directory link of your extension.
6. Open **SQL Operations Studio.** Click **File**, then click **Install Extension from VSIX package.**
7. Paste directory link of extension and click **Install**
8. Click **Reload Now** on bottom right.
9. Click on arrow next to **Localhost**, arrow next to **Databases**, then right click **AdventureWorks2014** and click **Manage**
10. On the line next to **Home,** click **Sample** to see your sample extension. 

## Next Steps
Thank you for attending this Microsoft Build session. Now that you have learned to build your own SQL Operations Studio extensions, we encourage you to continue to build extensions and contribute to our Extensions Marketplace.

To learn to build your own extensions:
- Get the [prerequisites](https://github.com/microsoft/sqlopsstudio/wiki/How-to-Contribute)
- Follow along on our [Github Wiki Get Started](https://github.com/microsoft/sqlopsstudio/wiki/Getting-started-with-Extensibility)  
- Read these blog posts:
    - https://medium.com/@kevcunnane/writing-a-sql-operations-studio-extension-in-15-minutes-7dfd24a74dfe 
    - https://medium.com/@kevcunnane/publishing-an-extension-for-sql-operations-studio-f5a5b323c13b 

Want to learn more about SQL Operations Studio?
- Leave a star on our [Github](https://github.com/microsoft/sqlopsstudio)
- Download [SQL Operations Studio](https://aka.ms/sqlopsstudio)
- Report Issues or suggest Feature Requests on our [Github Issues page](https://github.com/microsoft/sqlopsstudio/issues)
- Follow us on Twitter @sqlopsstudio