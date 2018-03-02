# Dynamic Fragments

Many a times you need a scenario when you want to display different user interface when screen changes its orientation from portrait to landscape.

We can customize the UI at the runtime using Fragments. Say for example you activity looks good and fills the entire screen when opened in mobile but when opened in tablet which has the bigger screen size than phones then your activity doesn’t fill the screen and may appear less attractive, in these cases you can display more content but manipulating with the Fragments.

# Implementation of Dynamic Fragments

* First thing you need `FragmentManager`, Now using FragmentManager instance get `FragmentTransaction` instance.

* Using `FragmentTransaction` instance you can perform fragment transactions which means you can add, remove and replace the fragments.

* Fragment changes when device changes it orientation. say we want to display Fragment1 when device is in portrait mode and Fragment2 when device is in the landscape mode.

* So now we just need to know the orientation of the device. That you can get by
```java
int displaymode = getResources().getConfiguration().orientation;
```

* if displaymode =1 then device is in portrait mode else it’s in landscape mode.


### Fragment1.xml

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:layout_width="match_parent"
android:layout_height="match_parent"
android:orientation="vertical" >

<TextView
android:id="@+id/textView1"
android:layout_width="fill_parent"
android:layout_height="fill_parent"
android:text="Iam fragment ONE"
android:gravity="center"
android:background="#5eff6a"
android:textAppearance="?android:attr/textAppearanceLarge" />

</LinearLayout>
```

### Fragment2.xml

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

### activity_main.xml

```xml

<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
xmlns:tools="http://schemas.android.com/tools"
android:id="@+id/LinearLayout1"
android:layout_width="match_parent"
android:layout_height="match_parent"
android:orientation="horizontal" >
</LinearLayout>
```

### Fragment1.java

```java
package com.Stonedcoder.coding-blocks.dynamicfragments.

import android.app.Fragment;
import android.os.Bundle;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;

public class Fragment1 extends Fragment {
public View onCreateView(LayoutInflater inflater, ViewGroup vg,
Bundle savedInstanceState) {
return inflater.inflate(R.layout.fragment1, vg, false);
}
}
```


### Fragment2.java
```java

package com.Stonedcoder.coding-blocks.dynamicfragments;
import android.app.Fragment;
import android.os.Bundle;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;

public class Fragment2 extends Fragment {
public View onCreateView(LayoutInflater inflater, ViewGroup vg,
Bundle savedInstanceState) {
return inflater.inflate(R.layout.fragment2, vg, false);
}
}
```

### MainActivity.java

```java
package com.Stonedcoder.coding-blocks.dynamicfragments;

import android.support.v7.app.ActionBarActivity;
import android.app.Fragment;
import android.app.FragmentManager;
import android.app.FragmentTransaction;
import android.os.Bundle;
import android.view.Display;
import android.view.Menu;
import android.view.MenuItem;
import android.view.WindowManager;

public class MainActivity extends ActionBarActivity {

@Override
protected void onCreate(Bundle savedInstanceState) {
super.onCreate(savedInstanceState);
setContentView(R.layout.main);

FragmentManager fm = getFragmentManager();
FragmentTransaction fragmentTransaction = fm.beginTransaction();

// get the display mode
int displaymode = getResources().getConfiguration().orientation;
if (displaymode == 1) { // it portrait mode
Fragment1 f1 = new Fragment1();
fragmentTransaction.replace(android.R.id.content, f1);
} else {// its landscape
Fragment2 f2 = new Fragment2();
fragmentTransaction.replace(android.R.id.content, f2);
}
fragmentTransaction.commit();

}
}
```