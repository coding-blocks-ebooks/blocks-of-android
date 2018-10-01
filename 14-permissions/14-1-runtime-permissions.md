# Runtime Permissions

Android 6.0 Marshmallow (API 23) introduced a new runtime permission model. If your application targets Android 6.0, you must use the new permissions model.

- A permission request is made via a call to the `requestPermissions()` method of the `ActivityCompat` class.

- When this method is called, the permission request is handled asynchronously and a method named `onRequestPermissionsResult()` called when the task is completed.

- The `requestPermissions()` method takes as arguments a reference to the current activity, together with the identifier of the permission being requested and a request code.

- The request code can be any integer value and will be used to identify which request has triggered the call to the `onRequestPermissionsResult()` method.

- Modify the `MainActivity.java` file to declare a request code and request recording permission in the event that the permission check failed .

````java
package com.stonedcoder.coding - blocks.permissions;

import android.Manifest;
import android.content.pm.PackageManager;
import android.support.v4.content.ContextCompat;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.Log;
import android.support.v4.app.ActivityCompat;

public class PermissionDemoActivity extends AppCompatActivity {

    private static String TAG = "PermissionDemo";
    private static final int RECORD_REQUEST_CODE = 101;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_permission_demo);

        int permission = ContextCompat.checkSelfPermission(this,
            Manifest.permission.RECORD_AUDIO);

        if (permission != PackageManager.PERMISSION_GRANTED) {
            Log.i(TAG, "Permission to record denied");
            makeRequest();
        }
    }

    protected void makeRequest() {
        ActivityCompat.requestPermissions(this,
            new String[] {
                Manifest.permission.RECORD_AUDIO
            },
            RECORD_REQUEST_CODE);
    }
}```

* Now implement the onRequestPermissionsResult() method.

```java
@Override
public void onRequestPermissionsResult(int requestCode,
    String permissions[], int[] grantResults) {
    switch (requestCode) {
        case RECORD_REQUEST_CODE:
            {

                if (grantResults.length == 0 ||
                    grantResults[0] !=
                    PackageManager.PERMISSION_GRANTED) {

                    Log.i(TAG, "Permission has been denied by user");
                } else {
                    Log.i(TAG, "Permission has been granted by user");
                }
                return;
            }
    }
}
````

- Compile and run the app on an Android 6 device and note that a dialog seeking permission to record audio appears .

- Tap the Allow button and check that the “Permission has been granted by user” message appears in the Logcat panel.

- Once the user has granted the requested permission, the `checkSelfPermission()` method call will return a `PERMISSION_GRANTED` result on future app invocations until the user uninstalls and re-installs the app or changes the permissions for the app in Settings.
