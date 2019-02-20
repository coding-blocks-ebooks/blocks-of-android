# Room

The next problem to be solved was that of persistence of data. This is done with the help of room library. Room library provides an object-mapping abstraction layer that easens out the process of performing the CRUD operations and at the same time fully exploits the power of SQLite.
Although there are a lot of APIs doing the same task yet Room is better because:
- For raw queries, the error is not detected at compile time by the APIs
- While using APIs, the SQL queries will have to be updated manually, in case of change in the structure of database
- A lot of boilerplate code is required
- Room abstarcts away some of the underlying implementation details of working with raw SQL tables and queries
- One instance of the Room database is usually enough for the whole app
- Room database does not allow queries to run on the main thread

Also, while the app is running, Room caches the data of the app on to the device. This cache data serves as the app's single source of truth thus retaining the data on to the app when the device has lost internet connection and the changes that are made by the user during this time are later on synced with the database when the internet connection is restored.

There are three major components in Room: Entity, DAO and database. Putting in simple words,
- <b> Entity </b>  represents the table which is constructed using an annotated data object.
- <b> DAO (Data Access Object) </b> defines the queries and associates them with the method calls.
- <b> Database </b> is a holder class that uses annotations to define the list of entities and database version. The class content defines the list of DAOs. It's also the main access point for the underlying database connection.

### Annotations
One class for each entity is made but in order to make it more meaningful to room database, annotations are required. Annotations identify how each part of class is related to entries in the database. Some of the important annotations are as follows:

- `@Entity(tableName = "word_table")`
Each @Entity class represents an entity in a table. The class declaration is annotated to indicate that it is an entity. Specifying the name of the table is optional, if not done the name of the class is stored as the name of table.
- `@PrimaryKey`
This is used to declare a field as primary key.
- `@NonNull`
This annotation is used to indicate that the particular parameter or method return value cannot be null.
- `@ColumnInfo(name = "word")`
This is used to change the name of column if you want it to be different from the name of the member variable.

## Implementation

- Create a class that describes an entity using the annotations mentioned above
```
@Entity(tableName = "users")
data class User(
        @PrimaryKey val id: String,
        val name: String
        val email: String
        val password: String
) {
    override fun toString() = name
}
```
Every field of the above class should either be public or should have a getter method.

- Create the DAO
    * The DAO must be an interface or an abstarct class.
    * The annotation DAO is to identify the class as DAO for Room.
    * For any query, use the annotation `@Query` as shown below, pass the query as a String argument and thereafter declare a method for calling the query. 
    * For the more general queries like performing the CRUD operations, Room has simplified the procedure even more. All you have to do is annotate the method with for example `@Insert` and declare the method after that. No need to write the whole query.
    * Similarly `@Delete` and `@Update` annotations are also available.
    * The return type of the methods is kept as LiveData so that the changes can observed and the required action can be taken.
    * However, if you wish to modify the data then use MutableLiveData.

```
@Dao
interface UsersDao {
 
    @Query("SELECT * FROM users")
    fun getAll(): LiveData<List<User>>

    @Insert
    fun insertAll(user: List<User>)
 
    @Delete
    fun delete(user: Person)
}
```
    
- Create the database

    - An abstarct class is to made that extends RoomDatabase.
    - Annotate the class to be a Room database, declare the entities that belong in the database and set the version number. Listing the entities will create tables in the database.

```
@Database(entities = [User::class], version = 1, exportSchema = false)
abstract class AppDatabase : RoomDatabase() {
    abstract fun usersDao(): UserDao
 
    companion object {
 
        @Volatile private var instance: AppDatabase? = null
 
        fun getInstance(context: Context): AppDatabase {
            return instance ?: synchronized(this) {
                instance
                        ?: buildDatabase(context).also { instance = it }
            }
        }
 
        private fun buildDatabase(context: Context): AppDatabase {
            return Room.databaseBuilder(context, AppDatabase::class.java, DATABASE_NAME)
                    .addCallback(object : RoomDatabase.Callback() {
                        override fun onCreate(db: SupportSQLiteDatabase) {
                            super.onCreate(db)
                            val request = OneTimeWorkRequestBuilder<SeedDatabaseWorker>().build()
                            WorkManager.getInstance()?.enqueue(request)
                        }
                    })
                    .build()
        }
    }
}
```

With this, the setup for Room database is complete. After this create a ViewModel to store, observe and serve data on the UI. Lastly use a PagedListAdapter for displaying the PagedList.
  


