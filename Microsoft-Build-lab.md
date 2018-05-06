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
    Add a full dashboard tab? **Y**
    What's the name of your extension?** **sample**
    What's the identifier of the extension? *hit enter*
    What's the description of your extension? **building a sample extension**
    What's your publisher name? *hit enter*
     
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

## Create an extension using Typescript
1. Hit Ctrl+` to open the Integrated Terminal **Note:** This is not the apostrophe, this is the grave accent below the ESC key. 
2. Type ```yo sqlops``` and hit Enter. This opens the SQL Operations Studio Extension generator. 
3. To start off, use arrow keys and hit enter on **New Extension (Typescript)**
4. In package.json 
    Add command to contributions.commands
    ```json
        {
        "command": "sp_whoisactive3.simple.install",
        "title": "Whoisactive: install",
        "icon": {
        "light": "./out/media/download.svg",
        "dark": "./out/media/download_inverse.svg"
        }
    ```

    Add dashboard contribution to contributions
    ```json 
    "dashboard.tabs": [
        {
        "id": "sp_whoisactive3-simple",
        "title": "whoisactive3",
        "description": "Extension for checking who is active.",
        "container": {
            "nav-section": [
            {
                "id": "sp_whoisactive3_insights",
                "title": "Insights",
                "icon": {
                "light": "./out/media/insights.svg",
                "dark": "./out/media/insights_inverse.svg"
                },
                "container": {
                "sp_whoisactive3-simple-insights": {}
                }
            },
            {
                "id": "sp_whoisactive3_documentation",
                "title": "Documentation",
                "icon": {
                "light": "./out/media/documentation.svg",
                "dark": "./out/media/documentation_inverse.svg"
                },
                "container": {
                "sp_whoisactive3-simple-insights3": {}
                }
            }
            ]
        }
        }
    ],
    "dashboard.containers": [
        {
        "id": "sp_whoisactive3-simple-insights",
        "container": {
            "widgets-container": [
            {
                "name": "Tasks",
                "widget": {
                "tasks-widget": [
                    "sp_whoisactive3.simple.install"
                ]
                }
            }
            ]
        }
        },
        {
        "id": "sp_whoisactive3-simple-insights3",
        "container": {
            "widgets-container": [
            
            {
                "name": "Top 10 CPU Usage",
                "gridItemConfig": {
                "sizex": 2,
                "sizey": 1
                },
                "widget": {
                "sp_whoisactive3-simple-CPU-usage": {}
                }
            }
            ]
        }
        }
    ],
    "dashboard.insights": [
        {
        "id": "sp_whoisactive3-simple-CPU-usage",
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
            "queryFile": "./out/sql/cpuUsage.sql"
        }
        }
    ]    
    ```
7. Run “npm install” 
8. Run “npm run compile”
9. Copy sql and media folder to ./out folder 
10. Remove everything from readme.md
13. Hit CTRL+` to open the integrated terminal. Type 'vsce package' to package your extension.
14. Copy the directory link of your extension.
15. Open **SQL Operations Studio.** Click **File**, then click **Install Extension from VSIX package.**
16. Paste directory link of extension and click **Install**
17. Click **Reload Now** on bottom right.
18. Click on arrow next to **Localhost**, arrow next to **Databases**, then right click **AdventureWorks2014** and click **Manage**
19. On the line next to **Home,** click **Sample** to see your sample extension. 


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