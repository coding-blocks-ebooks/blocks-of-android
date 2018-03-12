# Database

In software applications, it is mostly required to save information for some internal use or off course to provide user to great features depending on the data. And when we talk about android so SQLite is that default feature which is used as a database and also used as a local database for any application. .

# Implementation of SQLiteOpenHelper

* Start a new project and open up `activity_main.xml` and  
  add Edit text , 2 Buttons and TextView to it .

In this application we're taking an input from the user and adding contents to the database table and then showing it off .

```xml
<EditText
        android:id="@+id/et"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />

    <Button
        android:id="@+id/btnAdd"
        android:layout_width="100dp"
        android:layout_height="150dp" />

    <Button
        android:id="@+id/btnRemove"
        android:layout_width="100dp"
        android:layout_height="150dp" />

    <TextView
        android:id="@+id/tv"
        android:layout_width="400dp"
        android:layout_height="200dp" />
```

* Now we've to create a contract class which is goint to store some properties about the contents that we're adding into database .

Create a new Java class and name it as Products .

* Now we've to add the attributes and their respective constuctor & setters and getters method which can use to the pass the information .

```java
public class Products {

    private int _id;
    private String _productname;

public void Products(){

}

public Products(String productname) {
  this._productname = productname;
}

public int get_id() {
  return _id;
}

public String get_productname() {
  return _productname;
}

public void set_id(int _id) {this._id = _id;}

public void set_productname(String _productname) {this._productname = _productname;}

}
```

Now we've to create a database , create a new Java class MyDBHandler extends SQLiteOpenHelper

* Every database has to have some properties like : version , DBname , Table name , collumn ID , collumn name .

```java
public class MyDBHandler extends SQLiteOpenHelper {

//make some variables

private static final int DATABASE_VERSION=1;

private static final String DATABSE_NAME="products.db";

public static final String TABLE_PRODUCTS="products";

public static final String COLOUMN_ID="_id";

public static final String COLOUMN_PRODUCTNAME="productname";

public MyDBHandler(Context context, String name, SQLiteDatabase.CursorFactory factory, int version) {

super(context, DATABSE_NAME, factory, DATABASE_VERSION);}
}
```

* Now we've to override OnCreate and OnUpgrade .

* In the onCreate , we've to decleare and execute query which is going to generate a blank table

```java
@Override
public void onCreate(SQLiteDatabase db) {

String query = "CREATE TABLE " + TABLE_PRODUCTS + "(" +

COLOUMN_ID + " INTEGER PRIMARY KEY AUTOINCREMENT, " +

COLOUMN_PRODUCTNAME + " TEXT " +

");";

db.execSQL(query);

}
```

* In the OnUpgrade method we've to write the code for updating the table .

```java
@Override
public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {

db.execSQL("DROP TABLE IF EXISTS"+TABLE_PRODUCTS);

onCreate(db);

}
`
```

* Now we've to create the respective methods for Adding , Deleting products to the database.

* Adding products to the database we've to associate every value which have to be inserted into database using content values .

```java
public void addProduct(Products products){

ContentValues values = new ContentValues();

values.put(COLOUMN_PRODUCTNAME,products.get_productname());

SQLiteDatabase db= getWritableDatabase();

db.insert(TABLE_PRODUCTS,null,values);

db.close();

}
```

* After association , get reference to the database .

* Enter the values using `.insert` 

* Close the database

Method for Deleting products from the database 

* First you've to get reference to the database .

* Then you've to execute a query for deleting a single item from the database table .

```java
public void deleteProduct(String productname){

SQLiteDatabase db =getWritableDatabase();

db.execSQL("DELETE FROM " + TABLE_PRODUCTS + " WHERE " + COLOUMN_PRODUCTNAME + "=\"" + productname + "\";");

}
 ```
 
Once you're done with adding and deleting the only thing which is left behind was printing the database .

```java
public String databasetostring(){

  String dbString="";
  SQLiteDatabase db= getWritableDatabase();
  String query = "SELECT * FROM " + TABLE_PRODUCTS + " WHERE 1";
  Cursor c = db.rawQuery(query,null);
  c.moveToFirst();
  while (!c.isAfterLast()) {
    if(c.getString(c.getColumnIndex("productname"))!=null) {
      dbString+= c.getString(c.getColumnIndex("productname"));
      dbString+="\n";
    }
    c.moveToNext();
  }
  db.close();
  return dbString;
}
```
