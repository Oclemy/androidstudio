# Debug your database with the Database Inspector

- [Debug your database with the Database Inspector](#debug-your-database-with-the-database-inspector)
  - [Open the Database Inspector](#open-the-database-inspector)
  - [View and modify data](#view-and-modify-data)
    - [See live database changes](#see-live-database-changes)
  - [Query your databases](#query-your-databases)
    - [Run DAO queries](#run-dao-queries)
    - [Run custom SQL queries](#run-custom-sql-queries)
  - [Keep database connections open](#keep-database-connections-open)
  - [Additional resources](#additional-resources)
    - [Blog posts](#blog-posts)
    - [Videos](#videos)

In Android Studio 4.1 and higher, the Database Inspector allows you to inspect, query, and modify your app's databases while your app is running. This is especially useful for database debugging. The Database Inspector works with plain SQLite and with libraries built on top of SQLite, such as [Room](https://developer.android.com/training/data-storage/room).

**Note:** The Database Inspector only works with the SQLite library included in the Android operating system on API level 26 and higher. It doesn't work with other SQLite libraries that you bundle with your app.

## Open the Database Inspector

To open a database in the Database Inspector, do the following:

1.  [Run your app](https://developer.android.com/studio/run) on an emulator or connected device running API level 26 or higher.

    **Note:** A known issue related to the Android 11 emulator causes apps to crash when connecting to the DB Inspector. To fix the issue, [follow these steps](https://developer.android.com/studio/known-issues#ki-android-11-db-inspector).

2.  Select **View > Tool Windows > Database Inspector** from the menu bar.

3.  Select the running app process from the dropdown menu.

4.  The databases in the currently running app appear in the **Databases** pane. Expand the node for the database that you want to inspect.

## View and modify data

The **Databases** pane displays a list of the databases in your app and the tables that each database contains. Double\-click a table name to display its data in the inspector window to the right. You can click a column header to sort the data in the inspector window by that column.

![Screenshot of the Database Inspector window.](https://developer.android.com/studio/images/inspect/db-inspector-window.png)

**Figure 1.** The Database Inspector window.

You can modify data in a table by double\-clicking a cell, typing a new value, and pressing Enter. If your app uses Room and your UI observes the database (such as with `LiveData` or `Flow`), then any changes you make to the data are visible in your running app immediately. Otherwise, changes are only visible the next time your app reads the modified data from the database.

### See live database changes

If you want the Database Inspector to automatically update the data it presents as you interact with your running app, check the **Live updates** checkbox at the top of the inspector window. While live updates are enabled, the table in the inspector window becomes read\-only and you cannot modify its values.

Alternatively, you can manually update the data by clicking the **Refresh table** button at the top of the inspector window.

## Query your databases

The Database Inspector can run queries against your app's database while the app is running. The tool can use DAO queries if your app uses Room, but it also supports custom SQL queries.

### Run DAO queries

If your app uses Room, Android Studio provides gutter actions that allow you to quickly run query methods that you have already defined in your [DAO classes](https://developer.android.com/training/data-storage/room/accessing-data). These actions are available while your app is running and the Database Inspector is open in the IDE. You can run any query method in a DAO by clicking the **Run SQLite statement in Database Inspector** ![](https://developer.android.com/studio/images/app-inspection/database_inspector_query.png) button next to its `@Query` annotation.

![Screenshot of DAO gutter actions.](https://developer.android.com/studio/images/inspect/db-inspector-dao-gutters.png)

**Figure 2.** DAO query gutter actions.

If your app includes more than one database, Android Studio prompts you to select the database to query against from a drop\-down list. If your query method includes named [bind parameters](https://developer.android.com/training/data-storage/room/accessing-data#query-params), Android Studio requests values for each parameter before running the query. The query results are displayed in the inspector window.

### Run custom SQL queries

You can also use the Database Inspector to run custom SQL queries against your app's databases while your app is running. To query a database follow these steps:

1.  Click **Open New Query tab** ![](https://developer.android.com/studio/images/app-inspection/database_inspector_query.png) at the top of the **Databases** pane to open a new tab in the inspector window.

    ![Screenshot indicating the new query tab button.](https://developer.android.com/studio/images/inspect/db-inspector-new-query.png)

    **Figure 3.** Open a **New Query** tab.

2.  If your app includes more than one database, select the database that you want to query from the drop\-down list at the top of the **New Query** tab.

3.  Type your custom SQL query into the text field at the top of the **New Query** tab and click **Run**.

The query results that are displayed in the **New Query** tab are read\-only and cannot be modified. However, you can use the custom SQL query field to run modifier statements such as `UPDATE`, `INSERT`, or `DELETE`. If your app uses Room and your UI observes the database (such as with `LiveData` or `Flow`), then any changes you make to the data are visible in your running app immediately. Otherwise, changes are only visible the next time your app reads the modified data from the database.

## Keep database connections open

The Database Inspector can only interact with a database while your app maintains a live connection to that database. That means that if your app frequently connects to and disconnects from databases, it can be difficult to inspect those databases. The **Databases** pane uses icons to identify open ( ![](https://developer.android.com/studio/images/app-inspection/database_inspector-db_open.png)) and closed ( ![](https://developer.android.com/studio/images/app-inspection/database_inspector-db_closed.png)) databases.

Additionally, you can prevent database connections from closing by toggling **Keep database connections open** from off ( ![](https://developer.android.com/studio/images/app-inspection/database_inspector_unlocked.png)) to on ( ![](https://developer.android.com/studio/images/app-inspection/database_inspector_locked.png)) at the top of the **Databases** pane.

## Additional resources

To learn more about the Database Inspector, see the following additional resources:

### Blog posts

*   [Database Inspector: A live database tool we've been waiting for!](https://medium.com/androiddevelopers/database-inspector-9e91aa265316)

### Videos

*   [Database Inspector](https://www.youtube.com/watch?v=UMc7Tu0nKYQ) (11 Weeks of Android)
