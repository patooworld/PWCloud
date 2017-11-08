# Project "Carbon" Roadmap
Our goal is to make project "Carbon" an indispensable tool in your database DevOps toolset on Linux, macOS and Windows for SQL and PostgreSQL running anywhere.

As part of our planning rhythms, we typically look out 6 months, establish a set of themes we want to work towards, and then schedule work each milestone supporting those themes. Everything we do in project "Carbon" accrues to scenarios in one or more of these themes for SQL Server, Azure SQL Database, Azure SQL Data Warehouse and PostgreSQL.

Over the next 6 months, our plan is to enable end-2-end scenarios in the 4 themes below:
- **Modern Code Workflow:** Project "Carbon" is built on top of VS Code and offers the same lightweight, keyboard focused modern code workflow experiences when working with SQL and PostgreSQL. Features such as multiple tab windows, a rich SQL editor, IntelliSense, keyword completion, code snippets & code navigation, source control integration, execute ad-hoc SQL, view & save results as text, JSON or Excel, editing data, organizing & managing a large number of connections and the familiar Object Explorer are all core experiences that users rely on in their daily database DevOps tasks. We want to make sure these core experiences are a pleasure to use in project "Carbon".

- **Test + Deploy:** On top of these core experiences, we wanted to make it easy for users to create a copy of their databases for development/testing purposes or generate scripts to automate provisioning/database seeding tasks in DevOps pipelines. We've built experiences to generate CREATE + INSERT database scripts and execute scripts against single or multiple servers. In addition, users can use their favorite command line tools (e.g. Bash, PowerShell and ssh) in the Integrated Terminal window right within the project "Carbon" user interface.

- **Provision + Monitor + Troubleshoot:** Users often create new databases and database objects (such as tables, views, stored procedures, users, logins, roles, etc.) or update existing database objects. We've made this easy & fun with smart code snippets, rich GUI experiences and deep Azure integration. Users can easily monitor their databases or quickly troubleshoot performance bottlenecks, thanks to the fully customizable management dashboard, the execution plan viewer and the Extended Events (XEvent) viewer for SQL.

- **Business Continuity and Disaster Recovery**: Every database needs a *good* disaster recovery plan to ensure business continuity. Project "Carbon" lets users create a database recovery plan appropriate for their business needs and has an intuitive experience to backup and restore databases. Our longer term goal is to allow users to configure, monitor and troubleshoot *SQL Server Always On* for their mission-critical SQL Server databases and quickly failover to a secondary database in the event of a disaster.

Project "Carbon" will ship previews every month, and we'll make progress against each of the above themes during each iteration. Our detailed sprint plan is here: <https://github.com/Microsoft/carbon/projects/1>

## Legend
| | Description |
| :-: | :-- |
| :heavy_check_mark: | completed |
| :running_man: | work in progress |
| :muscle: | Stretch goal |
| :x: | not planned |
| :memo: | planned |

We will update this roadmap once a month at the end of each milestone.

We invite you to try the :heavy_check_mark: items in each area and file issues and suggestions here: <https://github.com/Microsoft/carbon/issues>

## Modern Code Workflow
| Scenario | SQL Server | Azure SQL DB/DW | PostgreSQL |
|---|---|---|---|
| Connect to database/server | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Rich SQL Editor + IntelliSense | :heavy_check_mark: | :heavy_check_mark:| :running_man: |
| Load/save SQL files (workspaces) | :heavy_check_mark: | :heavy_check_mark:| :heavy_check_mark: |
| Git integration and source control | :heavy_check_mark: | :heavy_check_mark:| :heavy_check_mark: |
| Run ad-hoc SQL queries | :heavy_check_mark: | :heavy_check_mark:| :heavy_check_mark: |
| View results & messages | :heavy_check_mark: | :heavy_check_mark:| :heavy_check_mark: |
| Export results as CSV, JSON, Excel | :heavy_check_mark: | :heavy_check_mark:| :running_man: |
| Select Top 1000 Rows (View Data) | :heavy_check_mark: | :heavy_check_mark:| :heavy_check_mark: |
| Edit Top 200 Rows (Edit Data) | :heavy_check_mark: | :heavy_check_mark:| :memo: |
| Organize servers in groups | :heavy_check_mark: | :heavy_check_mark:| :heavy_check_mark: |
| Code snippets for common tasks  | :heavy_check_mark: | :memo: | :memo: |
| Visualize database hierarchy and objects in an object explorer | :heavy_check_mark: | :heavy_check_mark:| :running_man: |
| Connect to SQL with integrated authentication from Linux/macOS | :heavy_check_mark: | N/A | N/A |
| Azure support - sign into Azure account | N/A | :memo: | :memo: |
| Azure support - subscription and resource discovery | N/A | :memo: | :memo: |

## Test + Deploy
| Scenario | SQL Server | Azure SQL DB/DW | PostgreSQL |
|---|---|---|---|
| Run queries against groups of servers| :x: | :x: | :x: |
| Full Database scripting (schema plus data) akin to mssql-scripter | :muscle: | :muscle: | :muscle: |
| Top object scripting (script as create for table, view, proc) | :heavy_check_mark: | :heavy_check_mark: | :running_man: |
| Full object scripting (script as create, alter, drop etc. for all valid objects) | :memo: | :memo: | :memo: |
| Easily view the status of current and completed tasks | 🏃 | 📝 | 📝 |
| View execution script and relevant logs for completed tasks | 📝 | 📝 | 📝 |

## Provision + Monitor + Troubleshoot
| Scenario | SQL Server | Azure SQL DB/DW | PostgreSQL |
|---|---|---|---|
| Basic customizable dashboard that shows database and server properties and allows navigating around server objects | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Customizable insights widgets for database and server status in the management dashboard | :heavy_check_mark: | :heavy_check_mark: | :running_man: |
| Database CRUD | :running_man: | :running_man: | :muscle: |
| CRUD for new users, logins, and roles | :muscle: | :muscle: | :muscle: |
| CMS - import registered SQL servers | :muscle: | :muscle: | N/A  |
| Performance Troubleshooting - view actual query plans (graphical) | :memo: | :memo: | :memo: |
| Performance Troubleshooting - view actual query plans (tabular) | :memo: | :memo: | :memo: |
| Performance Troubleshooting - view estimated query plans (graphical) | :running_man: | :running_man: | :muscle: |
| Performance Troubleshooting - view estimated query plans (tabular) | :running_man: | :running_man: | :muscle: |
| Performance Troubleshooting - open and save query execution plans as files | :heavy_check_mark: | :heavy_check_mark: | :muscle: |
| Performance Troubleshooting - simple XEvent viewer | :memo: | N/A | N/A |
| Azure support - simple XEvent viewer | N/A | :memo: | N/A |
| Azure support - database CRUD | N/A | :memo: | :memo: |
| Azure support - change SLO | N/A | :memo: | :memo: |
| Azure support - manage firewall settings | N/A | :memo: | :memo: |
| Azure support - ARM-based management dashboard widgets | N/A | :x: | :x: |
| Azure support - Azure Active Directory | N/A | :x: | :x: |
| Azure support - support for SQL managed instance (SQL only) | N/A | :memo: | N/A |

## Business Continuity and Disaster Recovery
| Scenario | SQL Server | Azure SQL DB/DW | PostgreSQL |
|---|---|---|---|
| Full backup using basic options | :heavy_check_mark: | N/A |:running_man: |
| Differential backup using basic options | :heavy_check_mark: | N/A | :x: |
| Log backup using basic options | :heavy_check_mark: | N/A | :x: |
| Full backup using advanced options | :running_man: | N/A | :x: |
| Differential backup using advanced options | :running_man: | N/A | :x: |
| Log backup using advanced options | :running_man: | N/A | :x: |
| Remote file picker to support .bak file paths | :memo: | N/A | :x: |
| Generate scripts for all backup operations | :memo: | N/A | :memo: |
| Restore full backups using basic options | :memo: |  N/A | :running_man: |
| Restore full using advanced options | :memo: | N/A | :x: |
| Restore differential backups using advanced options  | :memo: | N/A | :x: |
| Restore log backups using advanced options | :memo: | N/A | :x: |
| Generate scripts for all restore operations | :memo: | N/A | :memo: |
| Point in time restore | :memo: | :x: | :x: |
| Configure Availability Group (set up a new AG) | :x: | N/A | N/A |
| Monitor Availability Group (view status of AG nodes) | :x: | N/A | N/A |
| Troubleshot Availability Group (investigate problems with an AG) | :x: | N/A | N/A |
| Fail-Over Availability Group (trigger a fail-over to a secondary AG node) | :x: | N/A | N/A |