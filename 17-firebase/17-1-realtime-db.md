# Realtime Database

The Firebase Realtime Database allows data to be stored securely on Google cloud servers
and synchronized in `realtime` across all clients sharing the same database.

The database is referred to as being `realtime` because the speed with which the data is
synchronized across clients is probably as close to `realtime` .

Firebase uses NoSQL database for storing data in a Realtime Database. Data is not stored
in the tables and rows found in relational database management systems (RDBMS) such as
Oracle Database or Microsoft SQL Server. Nor is the data accessed using Structured Query
Language (SQL) statements.

Instead, the data is stored in the form of a `JSON` object. JSON is an acronym for `JavaScript Object Notation` and it defines a syntax used to transmit data in a format that is both lightweight and easy for humans to read, write and understand.

`JSON` objects typically consist of a key/value pair, where the key uniquely identifies the object within the database and the value represents the data that is being stored. Multiple JSON objects are structured in the form of a `JSON` tree.

## Nested JSON Data

```javascript
{
  "profile" : {
    "name" : "Raunaq",
    "email" : {
      "address" : "Pojo Address. ",
      "verified" : true
    }
  }
}
```

- In this case, a JSON object has been declared with “profile” as the key. This object consists of two child nodes identified by keys which read “name” and “email”. The “email” object, in turn, contains two child objects containing the user’s email address and a value indicating whether or not the email address has been verified.

- Rather like the directory structure of a computer filesystem, each node within a `JSON` tree is referenced by a path. Within the path each path component is represented by the key element of the corresponding node.

## Implementation of Realtime database

- Create a new project on Firebase Console. Enter you project name and select country, than click create project .

- After creation of project click on Add Firebase to your Android app. Enter package name, App nickname(optional) and SHA-1 key(optional) and click on add app.

- You will receive google-services.json file, put it inside app/ folder of your project.

- Add dependency to your root level build.gradle file.

```java
dependencies {
...
classpath 'com.google.gms:google-services:3.0.0'
}
```

- Add dependency to your app/module level build.gradle file.

```java
dependencies {
...
compile 'com.google.firebase:firebase-database:10.2.4'
}
```

- Add plugin at the bottom of your app-level build.gradle file.

```java
apply plugin: 'com.google.gms.google-services'
```

- After adding google.json file and all setup you might get this error.

```shell
Error:Execution failed for task ‘:app:processDebugGoogleServices’.
> Missing api_key/current_key object
```

- It is a bug in google-services:3.0.0, once you add any service of the Firebase, replace the google.json file with newly created file. You can get new google.json file from Project Settings in Firebase console.

- Default : It allows authenticated users to read and write data. They are useful if you want data open to all users of your app.

```javascript
{
  "rules": {
    ".read": "auth != null",
    ".write": "auth != null"
  }
}
```

- Public : It allows everyone to access your application database.

```javascript
{
  "rules": {
    ".read": true,
    ".write": true
  }
}
```

- User : It gives each authenticated user a personal node at /users/$user_id where $user_id is the ID of the user obtained through Authentication.

```javascript
{
  "rules": {
    "users": {
      "$uid": {
        ".read": "$uid === auth.uid",
        ".write": "$uid === auth.uid"
      }
    }
  }
}
```

- Private : It will disable read and write operation for all users. Admin can only access data from Firebase Console.

```javascript
{
  "rules": {
    ".read": false,
    ".write": false
  }
}
```

- In order to write data to Firebase Database, you need an instance.

```java
FirebaseDatabase firebaseDatabase = FirebaseDatabase.getInstance();
```

- Create a reference of your parent node and write data into it.

```java
DatabaseReference databaseReference = firebaseDatabase.getReference("version");
databaseReference.setValue(BuildConfig.VERSION_NAME);
```

- It will create a node named “version” and writes value into it.

## Read data added to database

```java
databaseReference.addValueEventListener(new ValueEventListener() {
  @Override
  public void onDataChange(DataSnapshot dataSnapshot) {
    Log.e("MainActivity", "version : " + dataSnapshot.getValue(String.class));
  }
  @Override
  public void onCancelled(DatabaseError databaseError) {
    Log.e("MainActivity", "onCancelled: " + databaseError.toString());
  }
});
```

- Here we have added string data so we can write dataSnapshot.getValue(String.class). If it is other datatype, you can mention it there.

## Write an object to the database

```java
DatabaseReference databaseReference = firebaseDatabase.getReference("user");
databaseReference.setValue(new User("Android","Raunaq"));
```

- It will create a node named “user” and writes data into it.

# Structured Data .

As we know this is not the right way to write the data. We can have multiple users so data should be structured. Lets see how to make it.

```java
databaseReference.push().setValue(new User("Android","Raunaq"));
databaseReference.push().setValue(new User("Android1","Coding-blocks"));
```

- `push()` will create a unique id and User data will be stored inside that.

## Listener for particular node .

```java
databaseReference.addValueEventListener(new ValueEventListener() {
    @Override
    public void onDataChange(DataSnapshot dataSnapshot) {
        Log.e("MainActivity", "user : " + dataSnapshot.getValue(User.class).getFirstName());
    }
    @Override
    public void onCancelled(DatabaseError databaseError) {}
});
```

- Whenever there is a change in specified node, onDataChange will be called on each active users device.

```java
databaseReference.setValue(new User("Android", "Gig"));
databaseReference.addValueEventListener(new ValueEventListener() {
    @Override
    public void onDataChange(DataSnapshot dataSnapshot) {
        User user = dataSnapshot.getValue(User.class);
        Log.e("MainActivity", "user : " + user.getFirstName() + " " + user.getLastName());
    }
    @Override
    public void onCancelled(DatabaseError databaseError) {}
});
```
