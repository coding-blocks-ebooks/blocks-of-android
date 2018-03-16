# Android Permissions

From Android Marshmallow , Google has changed the way of permissions that applications handle.

Earlier, we simply mention permissions in AndroidManifest.xml, but from Android 6.0 , we need to check every time for permission related task.

Applications need to ask the permission while it is running and also have to provide enough contexts on why the permissions are required. All the permissions, though we have to declare in manifest whenever application want to access the APIs that need the runtime permission, apps has to check whether that permission has been granted or to request the required permission using support library.

**Types of Permissions:**

Normal permission .
Dangerous permission .
# Permission Groups:

Different types of permissions are separated into groups based on which data or resource it requests access for. Once permission from a group has been granted then other permissions within that group do not need to be granted again. For example, permission group for SMS can send or receive the SMS. Those are two different permissions but the user only needs to allow one.

Android 6.0 Marshmallow has nine main groups of permissions:

Calendar: Read and/or write to the calendar.

Camera: Give the application the ability to access the camera.

Location: Access fine or coarse location.

Microphone: The ability to record audio.

Phone: Includes phone state, the ability to make calls, read and write to the call log and voicemail.

Sensor: The ability to use various sensors in the device, like a gyroscope.

SMS: Similar to how the phone is handled including sending and receiving texts. MMS and cell broadcasts.

Storage: Read and write to device’s external storage.
# Normal Permissions

It indicates that there's no great risk to the user's privacy or security in letting apps have those permissions.

```xml
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.REQUEST_INSTALL_PACKAGES"/>
<uses-permission android:name="android.permission.BLUETOOTH"/>```


* Create a new project in Android Studio

* Open up your `Main_Activity.java`  

```java
package com.stonedcoder.coding-blocks.permissions;

import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.Manifest;
import android.content.pm.PackageManager;
import android.support.v4.content.ContextCompat;
import android.util.Log;

public class MainActivity extends AppCompatActivity {

private static String TAG = "Permission ";

@Override
protected void onCreate(Bundle savedInstanceState) {
super.onCreate(savedInstanceState);
setContentView(R.layout.activity_permission_demo);

int permission = ContextCompat.checkSelfPermission(this,
Manifest.permission.RECORD_AUDIO);

if (permission != PackageManager.PERMISSION_GRANTED) {
Log.i(TAG, "Permission to record denied");
}
}
}
```

* An app before 6.0 attempts to make use of a feature that requires approval of a dangerous permission, and regardless of whether or not permission was previously granted, the code must check that the permission has been granted.

* This can be achieved via a call to the `checkSelfPermission()` method of the ContextCompat class, passing through as arguments a reference to the current activity and the permission being requested. The method will check whether the permission has been previously granted and return an integer value matching `PackageManager.PERMISSION_GRANTED` or `PackageManager.PERMISSION_DENIED` . 

* Run the app on a device running a version of Android 6.0 and check the log cat output within Android Studio.

* After the app has launched, the output should include the **“Permission to record denied”** message.

* Now open up `AndroidManifest.xml`

```xml
<uses-permission android:name="android.permission.RECORD_AUDIO" />```

* Compile and run the app once again and note that this time the permission denial message does not appear.

* However, and note that even though permission has been added to the manifest file, the check still reports that permission has been denied. This is because Android 6 requires that the app also request dangerous permissions at runtime.