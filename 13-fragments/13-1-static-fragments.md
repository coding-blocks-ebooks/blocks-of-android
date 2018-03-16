# Static Fragments

* Create a blank project .

* Create `Fragment1.java` and `Fragment2.java` which extends the `Fragment` Class.

* Override `onCreate()` method in `Fragment1.java` and `Fragment2.java`, and inflate the `Fragment layouts ( R.id.Fragment1 and R.id.Fragment2)` using `LayoutInflater` Class object and by passing the `ViewGroup` argument which is the activity in which the fragment will be embedded.


# Implementation of Static fragments .

* Add a layout for `Fragment1.java` .

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <TextView
        android:id="@+id/textView1"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:background="#5eff6a"
        android:gravity="center"
        android:text="Iam fragment ONE"
        android:textAppearance="?android:attr/textAppearanceLarge" />

</LinearLayout>```

* Add a layout for `Fragment2.java` .

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical" >

    <TextView
        android:id="@+id/textView1"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:text="Iam fragment TWO"
        android:gravity="center"
        android:background="#ff9e5e"
        android:textAppearance="?android:attr/textAppearanceLarge" />

</LinearLayout>
```
* Now , we've to include both the fragments inside `activity_main.xml` .

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/LinearLayout1"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal" >

    <fragment
        android:id="@+id/fragment1"
        android:name="com.stonedcoder.codingblocks.staticfragments.Fragment1"
        android:layout_width="match_parent"
        android:layout_height="fill_parent"
        android:layout_weight="1" />
    <fragment
        android:id="@+id/fragment2"
        android:name="com.stonedcoder.codingblocks.staticfragments.Fragment2"
        android:layout_marginLeft="5sp"
        android:layout_width="match_parent"
        android:layout_height="fill_parent"
        android:layout_weight="1" />

</LinearLayout>
```
* Now open up `Fragment1.java`

```java
package com.stonedcoder.codingblocks.staticfragments;

import android.annotation.SuppressLint;
import android.app.Fragment;
import android.os.Bundle;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;

@SuppressLint("NewApi")
public class Fragment1 extends Fragment {
    public View onCreateView(LayoutInflater inflater, ViewGroup vg, Bundle savedInstanceState) {
        return inflater.inflate(R.layout.fragment1, vg, false);

    }
}
```

* Now open up `Fragment2.java `

```java
package com.stonedcoder.codingblocks.staticfragments;

import android.app.Fragment;
import android.os.Bundle;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;

public class Fragment2 extends Fragment {
    public View onCreateView(LayoutInflater inflater, ViewGroup vg, Bundle savedInstanceState) {
        return inflater.inflate(R.layout.fragment2, vg, false);

    }
}```
