# Introduction

Welcome to Microsoft Ignite! In this session, you will learn to build your own extensions for [Azure Data Studio](https://aka.ms/azuredatastudio). Azure Data Studio is a data management tool that enables you to work with SQL Server, Azure SQL DB and SQL DW from Windows, macOS and Linux. In addition to providing core functionality like a rich T-SQL editing experience, we also provide an Extension Marketplace where the community can contribute their own **Extensions** so that anyone can use the extension.

These extensions are generally lighted up through our **Dashboard**, where users can quickly monitor and troubleshoot their databases.

![dash](https://cloudblogs.microsoft.com/uploads/prod/2018/03/whoisactive.png)

Users can build their own **Insight Widgets** as shown in the image above, which are powered by T-SQL queries. This allows a user to see a visualization of the common queries they run day to day.

Over the course of this session, you will:
- Build your first sqlops extension using a [Visual Studio Code](https://code.visualstudio.com/) environment.
- Use an [Extension generator](https://github.com/llali/generator-sqlops) to help write an extension
- Create an Insight Widget extension
- **Never before seen End-To-End Tutorial** to create an extension using Typescript to create a Wizard extension 

## Prerequisites
All prerequisites are already install on machine. As a summary, here is what is pre-installed:
- [Azure Data Studio](https://aka.ms/azuredatastudio)
- [Visual Studio Code](https://code.visualstudio.com/)
- [Git](https://git-scm.com)
- [Node.JS](https://nodejs.org/en/), `>= 8.9.1, < 9.0.0`
- [Yarn](https://yarnpkg.com/en/), follow the [installation guide](https://yarnpkg.com/en/docs/install)
- [Python](https://www.python.org/downloads/), at least version 2.7 (version 3 is __*not*__ supported)
- [Generator-sqlops](https://github.com/llali/generator-sqlops)
- [SQL Server 2017 developer edition](https://www.microsoft.com/en-us/sql-server/sql-server-downloads)
- Visual Studio Code extensions
    - Azure Data Studio debug
    - mssql extension
    - Debugger for Chrome
- npm packages
    - npm install -g vsce

## Create an insight widget
1. Open Azure Data Studio.
2. Click new connection.
3. Select **Localhost** from **Saved Locations** and click **Connect.**
4. Click arrow next to localhost under Servers, then click arrow by databases, then **right click** AdventureWorks2014 and click **New Query**
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

6. Click File->Save As:
    ```
    C:\Users\LabUser\.azuredatastudio\sample.sql
    ```
7. Click Run.
8. Click **View as Chart** Icon on right of Results window.
9. Set Chart Type as **horizontalBar** and Data Direction to **Vertical**
9. Click **Create Insight**.
10. Edit name as 'DB Space'
11. Edit queryfile as 'C:\Users\LabUser\\.sqlops\sample.sql'
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
                    "queryFile": "C:\\Users\\LabUser\\.azuredatastudio\\sample.sql"
                }
            }
        }
    ]
    ```
14. Hit Ctrl+S to save your settings file
15. Right click **AdventureWorks2014** under servers and click **Manage.** You should now be able to see the table spaces widget! 

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
    - What's your publisher name? *ignite*
        - The publish name must be set, we recommend *ignite* but you can use any publisher name.
     
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
12. Open **README.md** and delete the contents of the readme. Paste the following:
    ```
    Sample insight widget extension.
    ```
13. Click **File->Save All**. 
13. Hit CTRL+` to open the integrated terminal. Type 'vsce package' to package your extension. Type 'Y' and enter when prompted if you would like to continue without the repository field.
14. Use “Ctrl + C” to copy the file location of the vsix package from the terminal.
15. Open **Azure Data Studio.** Click **File**, then click **Install Extension from VSIX package.**
16. Paste directory link of extension and click **Install**
17. Click **Reload Now** on bottom right.
18. Click on arrow next to **Localhost**, arrow next to **Databases**, then right click **AdventureWorks2014** and click **Manage**
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

# Creating a 'Script As' Wizard extension using Typescript

# Creating the extension
## Getting Started
1. Hit **Ctrl+`** to open the Integrated Terminal Note: This is not the apostrophe, this is the grave accent below the ESC key.
1. Type `yo sqlops` from the command line and press enter.
1. To start off, use arrow keys and hit enter on **New Extension (Typescript)** 
Follow these instructions:
    - What's the name of your extension? **WizardSample**
    - What's the identifier of the extension? *hit enter*
    - What's the description of your extension? **Wizard extension**
    - What's your publisher name? ignite
    - Enable stricter Typescript checking? **n**
    - Setup linting using 'tslint'? **Y**
    - Initialize a git repository? **n**
1. After npm install finishes, switch to the extension directory using ```cd WizardSample```
2. Open the code in VS Code by running `code .`
3. Your extension can already run! Go to the debug tab in VS Code and hit the green play button to open Azure Data Studio with your extension installed. Press `ctrl+shift+p` to open the command palette and type "Hello World" and hit enter to run the default "Hello World" command contributed by your extension.
4. Open `README.md` in the created extension and delete the contents, then save it.
## Writing Code
**1.** Create a folder under `src` called `typings` and download the latest [API typings file](https://raw.githubusercontent.com/Microsoft/sqlopsstudio/master/src/sql/sqlops.proposed.d.ts) and save it in that folder as `sqlops.proposed.d.ts`

**2.** Open `package.json` and add the command to open the wizard by replacing the `"contributes"` section with the code from Code Sample 1 below:

### Code Sample 1 - `package.json contributes`
```
"contributes": {
    "commands": [
        {
            "command": "extension.scriptObjects",
            "title": "Open Script As Wizard"
        }
    ]
},
```

**3.** Replace the `"activationEvents"` section with the code from Code Sample 2:
### Code Sample 2 - `package.json activationEvents`
```
"activationEvents": [
    "*"
],
``` 
This will cause the extension to register a new command that we will use to open the wizard and to start the extension whenever Azure Data Studio starts.

**4.** Open `src/extension.ts` and add code to execute the command you just registered by replacing the `activate` extension's code with the code from Code Sample 3 below:

### Code Sample 3 - `extension.ts activate`
```
// The command has been defined in the package.json file
// Now provide the implementation of the command with  registerCommand
// The commandId parameter must match the command field in package.json
context.subscriptions.push(vscode.commands.registerCommand('extension.scriptObjects', async () => {
    let connection = await sqlops.connection.getCurrentConnection();
    let wizard = new ScriptWizard(connection);
    wizard.open();
}));
```

**5.** Create the `ScriptWizard` class at the bottom of that file with the code from Code Sample 4. This sample shows a basic use of the wizard framework to open a wizard that has 2 pages but no content yet:
### Code Sample 4 - `extension.ts ScriptWizard`
```
class ScriptWizard {
    private objectType: string;
    private selectedObjectLabel: string;
    private scriptType: string;
    private scriptTypeDropdown: sqlops.DropDownComponent | undefined;
    private wizard: sqlops.window.modelviewdialog.Wizard;
    private page1: sqlops.window.modelviewdialog.WizardPage;
    private page2: sqlops.window.modelviewdialog.WizardPage;
    private objectMetadataMap = new Map<string, sqlops.ObjectMetadata>();
    static scriptOperations = new Map<string, sqlops.ScriptOperation>([
        ['Select', sqlops.ScriptOperation.Select],
        ['Create', sqlops.ScriptOperation.Create],
        ['Insert', sqlops.ScriptOperation.Insert],
        ['Update', sqlops.ScriptOperation.Update],
        ['Delete', sqlops.ScriptOperation.Delete],
        ['Execute', sqlops.ScriptOperation.Execute],
        ['Alter', sqlops.ScriptOperation.Alter]
    ]);
    static scriptOperationsByType = new Map<string, string[]>([
        ['Table', ['Create', 'Delete']],
        ['View', ['Create', 'Select', 'Alter', 'Delete']],
        ['StoredProcedure', ['Create', 'Execute', 'Alter', 'Delete']]
    ]);

    constructor(private connection: sqlops.connection.Connection) {
        this.objectType = '';
        this.selectedObjectLabel = '';
        this.scriptType = '';
        this.scriptTypeDropdown = undefined;
        this.page1 = this.setupPage1();
        this.page2 = this.setupPage2();
        this.wizard = this.setupWizard(); 
    }

    public open(): void {
        this.wizard.open();
    }

    private setupPage1(): sqlops.window.modelviewdialog.WizardPage {
        let page1 = sqlops.window.modelviewdialog.createWizardPage('Select object');
        page1.registerContent(view => Promise.resolve());
        return page1;
    }

    private setupPage2(): sqlops.window.modelviewdialog.WizardPage {
        let page2 = sqlops.window.modelviewdialog.createWizardPage('Details');
        page2.registerContent(view => Promise.resolve());
        return page2;
    }

    private setupWizard(): sqlops.window.modelviewdialog.Wizard {
        this.wizard = sqlops.window.modelviewdialog.createWizard('Script Wizard');
        this.wizard.pages = [this.page1, this.page2];
        this.wizard.generateScriptButton.hidden = true;
        return this.wizard;
    }
}
```

**6.** Save the file, then go to the debug pane in VS Code and **click the green play button**. Azure Data Studio should open. 

Once Azure Data Studio opens, **connect to localhost by clicking on it** in Object Explorer.

Then press **`ctrl+shift+p`** to open the command palette, then start typing **"Open Script As Wizard"** and press **enter** once that option is displayed to open the wizard. Note that you have to connect in Object Explorer before opening the wizard so that the wizard has a server to use for scripting. You now have an extension that opens a 2-page wizard with no content!

**7.** Go back to Visual Studio Code. Add content to page 1 of the wizard by replacing the `setupPage1` function with the one from Code Sample 5 below, which uses the model view framework to add an object type dropdown and an object choice listbox.

### Code Sample 5 - `ScriptWizard setupPage1 function`
```
private setupPage1(): sqlops.window.modelviewdialog.WizardPage {
    let page1 = sqlops.window.modelviewdialog.createWizardPage('Select object');
    page1.registerContent(async view => {
        let typeSelection = view.modelBuilder.dropDown().withValidation(component => component.value !== '').component();
        let objectDropdown = view.modelBuilder.listBox().component();

        typeSelection.values = ['', 'Table', 'View', 'StoredProcedure'];
        typeSelection.onValueChanged(value => {
            this.objectType = value.selected;
            this.fillInObjectChoices(objectDropdown);
        });

        objectDropdown.onRowSelected(() => {
            this.selectedObjectLabel = objectDropdown.values[objectDropdown.selectedRow || 0];
        });

        let flexView = view.modelBuilder.formContainer().withFormItems([
            {
                component: typeSelection,
                title: 'Type'
            },
            {
                component: objectDropdown,
                title: 'Object to script'
            }
        ]).component();

        return view.initializeModel(flexView);
    });

    return page1;
}
```

**8.** Add the **`fillInObjectChoices`** function referenced in `setupPage1` to the `ScriptWizard` class using the code from Code Sample 6. 

### Code Sample 6 - `ScriptWizard fillInObjectChoices function`
```
private async fillInObjectChoices(objectDropdown: sqlops.ListBoxComponent): Promise<void> {
    this.objectMetadataMap.clear();
    let metadataProvider = sqlops.dataprotocol.getProvider<sqlops.MetadataProvider>(this.connection.providerName, sqlops.DataProviderType.MetadataProvider);
    let objectMetadata = (await metadataProvider.getMetadata(await sqlops.connection.getUriForConnection(this.connection.connectionId))).objectMetadata;
    let matchingObjects: string[] = [];
    objectMetadata.forEach(metadata => {
        if (metadata.metadataTypeName === this.objectType) {
            let objectLabel = (metadata.schema ? `${metadata.schema}.` : '') + metadata.name;
            matchingObjects.push(objectLabel);
            this.objectMetadataMap.set(objectLabel, metadata);
        }
    });
    objectDropdown.values = matchingObjects;
    if (this.scriptTypeDropdown && this.objectType) {
        this.scriptTypeDropdown.values = ScriptWizard.scriptOperationsByType.get(this.objectType) as string[];
    }
}
```

This code uses the data protocol API to list scriptable objects by type. After this step you can save and rerun the extension like before in **Step 6** and when you open the wizard you should see that it has the dropdown and list box that you added and that it lists the available objects when you choose a type.

**9.** Add content to page 2 of the wizard by replacing the `setupPage2` function with the one from Code Sample 7 below, which uses the model view framework to add a dropdown box to select the script type. Again you can debug the extension after this step and see that both pages now have content.

### Code Sample 7 - `ScriptWizard setupPage2 function`
```
private setupPage2(): sqlops.window.modelviewdialog.WizardPage {
    let page2 = sqlops.window.modelviewdialog.createWizardPage('Details');
    page2.registerContent(async view => {
        let scriptTypeDropdown = view.modelBuilder.dropDown().component();
        
        scriptTypeDropdown.values = [];
        ScriptWizard.scriptOperations.forEach((_, key) => {
            (scriptTypeDropdown.values as string[]).push(key);
        });
        scriptTypeDropdown.onValueChanged(value => this.scriptType = value.selected);
        let formContainer = view.modelBuilder.formContainer().withFormItems([
            {
                component: scriptTypeDropdown,
                title: 'Operation'
            }
        ]).component();
        this.scriptTypeDropdown = scriptTypeDropdown;
        return view.initializeModel(formContainer);
    });
    
    return page2;
}
```

**10.** Update the `setupWizard` function to add a callback when the "Done" button is pressed that uses the data protocol API to generate the requested script by replacing the `setupWizard` function with the one from Code Sample 8:

### Code Sample 8 - `ScriptWizard setupWizard function`
```
private setupWizard(): sqlops.window.modelviewdialog.Wizard {
    this.wizard = sqlops.window.modelviewdialog.createWizard('Script Wizard');
    this.wizard.pages = [this.page1, this.page2];
    this.wizard.generateScriptButton.hidden = true;
    this.wizard.doneButton.onClick(async () => {
        let scriptingProvider = sqlops.dataprotocol.getProvider<sqlops.ScriptingProvider>(this.connection.providerName, sqlops.DataProviderType.ScriptingProvider);
        let chosenObject = this.objectMetadataMap.get(this.selectedObjectLabel);
        if (chosenObject && this.objectType) {
            let result = await scriptingProvider.scriptAsOperation(await sqlops.connection.getUriForConnection(this.connection.connectionId), ScriptWizard.scriptOperations.get(this.scriptType) as sqlops.ScriptOperation, chosenObject, {
                filePath: '',
                scriptCompatibilityOption: 'Script140Compat',
                targetDatabaseEngineEdition: 'SqlServerStandardEdition',
                targetDatabaseEngineType: 'SingleInstance'
            });
            if (result.script) {
                let document = await vscode.workspace.openTextDocument({
                    language: 'sql',
                    content: result.script
                });
                vscode.window.showTextDocument(document);
            }
        }
    });
    return this.wizard;
}
```

If you have written a VS Code extension before you will notice that this code uses some VS Code APIs to open the generated script. Azure Data Studio exposes most existing VS Code APIs for extension authors to use.

## Finishing Touches
At this point you should have a working wizard that scripts the selected object. To try it out save the **`extension.ts`** file and run the extension like before. Connect to the saved server in Object Explorer and press `ctrl+shift+p` to open the command palette, then type "Open Script As Wizard" and hit enter to open the wizard. You can interact with the wizard and it will create the requested script when you go through both pages and click "Done".

To package your extension as an installable `.vsix` file, you can run `vsce package` from the command line in Visual Studio Code.

## Next Steps
Thank you for attending this Microsoft Ignite session. Now that you have learned to build your own Azure Data Studio extensions, we encourage you to continue to build extensions and contribute to our Extensions Marketplace.

To learn to build your own extensions:
- Get the [prerequisites](https://github.com/microsoft/sqlopsstudio/wiki/How-to-Contribute)
- Follow along on our [Github Wiki Get Started](https://github.com/microsoft/sqlopsstudio/wiki/Getting-started-with-Extensibility)  
- Read these blog posts:
    - https://medium.com/@kevcunnane/writing-a-sql-operations-studio-extension-in-15-minutes-7dfd24a74dfe 
    - https://medium.com/@kevcunnane/publishing-an-extension-for-sql-operations-studio-f5a5b323c13b 

Want to learn more about Azure Data Studio?
- Leave a star on our [Github](https://github.com/microsoft/azuredatastudio)
- Download [Azure Data Studio](https://aka.ms/azuredatastudio)
- Report Issues or suggest Feature Requests on our [Github Issues page](https://github.com/microsoft/sqlopsstudio/issues)
- Follow us on Twitter [@sqlopsstudio](https://twitter.com/sqlopsstudio)