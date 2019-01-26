# Save data in a local database using Room
Room provides an abstraction layer over SQLite to allow fluent database access while harnessing the full power of SQLite.
Apps that handle non-trivial amounts of structured data can benefit greatly from persisting that data locally. The most common use case is to cache relevant pieces of data. That way, when the device cannot access the network, the user can still browse that content while they are offline. Any user-initiated content changes are then synced to the server after the device is back online.
Because Room takes care of these concerns, using Room is recommended instead of SQLite. 
### There are 3 major components in Room:

#### 1. Database:
Contains the database holder and serves as the main access point for the underlying connection to your app's persisted, relational data.
The class that's annotated with ``` @Database ``` should satisfy the following conditions:
* Be an abstract class that extends ``` @RoomDatabase ``` .
* Include the list of entities associated with the database within the annotation.
* Contain an abstract method that has 0 arguments and returns the class that is annotated with ``` @Dao ``` .
At runtime, you can acquire an instance of Database by calling ``` Room.databaseBuilder() ``` or ``` Room.inMemoryDatabaseBuilder() ``` .

#### 2.Entity:
Represents a table within the database.

#### 3.DAO:
Contains the methods used for accessing the database.

The app uses the Room database to get the data access objects, or DAOs, associated with that database. The app then uses each DAO to get entities from the database and save any changes to those entities back to the database. Finally, the app uses an entity to get and set values that correspond to table columns within the database.

The following code snippet contains a sample database configuration with one entity and one DAO:
#### user
```kotlin
@Entity
data class User(
    @PrimaryKey var uid: Int,
    @ColumnInfo(name = "first_name") var firstName: String?,
    @ColumnInfo(name = "last_name") var lastName: String?
)
```
#### user Dao
``` kotlin
@Dao
interface UserDao {
    @Query("SELECT * FROM user")
    fun getAll(): List<User>

    @Query("SELECT * FROM user WHERE uid IN (:userIds)")
    fun loadAllByIds(userIds: IntArray): List<User>

    @Query("SELECT * FROM user WHERE first_name LIKE :first AND " +
           "last_name LIKE :last LIMIT 1")
    fun findByName(first: String, last: String): User

    @Insert
    fun insertAll(vararg users: User)

    @Delete
    fun delete(user: User)
}
```
#### AppDatabase
```kotlin
@Database(entities = arrayOf(User::class), version = 1)
abstract class AppDatabase : RoomDatabase() {
    abstract fun userDao(): UserDao
}
```
After creating the files above, you get an instance of the created database using the following code:
```kotlin
val db = Room.databaseBuilder(
            applicationContext,
            AppDatabase::class.java, "database-name"
        ).build()
```
# Accessing data using Room DAOs

To access your app's data using the ``` Room persistence library ```, you work with data access objects, or DAOs. This set of ``` Dao ``` objects forms the main component of Room, as each DAO includes methods that offer abstract access to your app's database.
By accessing a database using a DAO class instead of query builders or direct queries, you can separate different components of your database architecture.
Furthermore, DAOs allow you to easily mock database access as you test your app.
A DAO can be either an interface or an abstract class. If it's an abstract class, it can optionally have a constructor that takes a ``` RoomDatabase ``` as its only parameter. Room creates each DAO implementation at compile time.
There are multiple convenience queries that you can represent using a DAO class. This document includes several common examples.
#### Insert
When you create a DAO method and annotate it with ``` @Insert ```, Room generates an implementation that inserts all parameters into the database in a single transaction.
The following code snippet shows several example queries:
``` kotlin
@Dao
interface MyDao {
    @Insert(onConflict = OnConflictStrategy.REPLACE)
    fun insertUsers(vararg users: User)

    @Insert
    fun insertBothUsers(user1: User, user2: User)

    @Insert
    fun insertUsersAndFriends(user: User, friends: List<User>)
}
```
If the @Insert method receives only 1 parameter, it can return a long, which is the new rowId for the inserted item. If the parameter is an array or a collection, it should return long[] or List<Long> instead.

#### Update
The ``` Update ``` convenience method modifies a set of entities, given as parameters, in the database. It uses a query that matches against the primary key of each entity.

The following code snippet demonstrates how to define this method:
``` kotlin
@Dao
interface MyDao {
    @Update
    fun updateUsers(vararg users: User)
}
```
#### Delete
The ``` Delete ``` convenience method removes a set of entities, given as parameters, from the database. It uses the primary keys to find the entities to delete.
The following code snippet demonstrates how to define this method:
``` kotlin
@Dao
interface MyDao {
    @Delete
    fun deleteUsers(vararg users: User)
}
```
