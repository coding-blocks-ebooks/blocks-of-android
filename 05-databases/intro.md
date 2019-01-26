# Database
Do you want to store and save data of your Android application in your device? 

The solution is very easy as Android use **SQLite** as it’s default built-in database. SQLite is a light weight relational database. SQLite stores data of your application in a text file. SQLite database supports standard features of relational database. Every device has an inbuilt support for SQLite database, which is automatically managed on android right from its creation, execution to querying up process. SQLite is an open source database, available on every android database. It supports standard relations database features, like SQL syntax, transactions & SQL statements. SQLite is considerably, the lighter version of SQL database, where most of the SQL commands don’t run on SQLite database. Once SQLite is in place, it is important to ensure that a feature or command is available in SQLite; only then can it be executed. 

 - ``` android.database.sqlite``` package has all you need to save data of your application. 
 - These classes are used to manage the `Cursor` object returned from a content provider query. 
 - Databases are usually created and opened with ``` openOrCreateDatabase(String, int, SQLiteDatabase.CursorFactory) ``` 
 - To make requests through content providers, you can use the ``` content.ContentResolver ```class.
 - All databases are stored on the device in `` /data/data/<package_name>/databases``

