#ListView & Array Adapters

## Understanding ListView .  

List View is used in order to create such a layout which has some content into it and the respective contents is

displayed as a list .

examples : Contacts , Gmail , Messages  .

## Creating a List . 

* Build a new Android project .

* Open up ```activity_main.xml``` & add ```ListView``` into it . 

* Set height and width to ```match_parent``` & give that view a respective Id . (So , that your list can be displayed in full screen) .

Now , You'll get a list rendered on your design pane of the android .

```
    <?xml version="1.0" encoding="utf-8"?>
    <RelativeLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.codingblocks_projects.stoned_coder.listview.MainActivity">
            
    <ListView
        android:id="@+id/listView"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

    </ListView>
    
    </RelativeLayout>```
    
    
Great , Now we've a list view let's add the contents that you want to display into the list .

## Adding Contents to List 

* Open up , ```MainActivity.java``` file and inside your ```onCreate()``` method create a ```String[]``` and name that as per your choice  , and populate that ```String``` with some values .

```String[] fruits = {"Peach" , "Mango" , "Apple" , "Kiwi" , "Orange"};```


So , The problem occurs is that we can't arbitrarily add this ```String[]``` to the respective list view .


In order to solve this issue there's an interface in android called **ArrayAdapters**.


What **ArrayAdapters** does that it binds your values into the list view .

```ListAdapter fruits = new ArrayAdapter<>(this,android.R.layout.simple_list_item_1 , fruits);```

Now the arguments that this adapter takes ;

* this : this is a keyword used for getting the context . 
* android.R.layout.android.R.layout.simple_list_item_1 : It's a simple list item stored in android by default 
* fruits : respective ```String[]``` , which we want to get values from . 


**Setting up the Adapter**

* First you've to get the reference of the respective ListView by calling ```findViewById()``` method.

* Then you can simply set the adapter by calling ```setadapter()``` method , which takes one argument which is the adapter .

```java
    ListView list = (ListView)findViewById(R.id.listView);
    list.setAdapter(FruitsAdapter)
```

Now , when you run your application the list will come up on the device screen like this .







 




    
  






