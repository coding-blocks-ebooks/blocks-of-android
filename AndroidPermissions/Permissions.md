# Android Permissions

From Android Marshmallow , Google has changed the way of permissions that applications handle.

Earlier, we simply mention permissions in AndroidManifest.xml, but from Android 6.0 , we need to check every time for permission related task.

Applications need to ask the permission while it is running and also have to provide enough contexts on why the permissions are required. All the permissions, though we have to declare in manifest whenever application want to access the APIs that need the runtime permission, apps has to check whether that permission has been granted or to request the required permission using support library.

**Types of Permissions:**

Normal permission .
Dangerous permission .
# Normal Permissions

It indicates that there's no great risk to the user's privacy or security in letting apps have those permissions.

```xml
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.REQUEST_INSTALL_PACKAGES"/>
<uses-permission android:name="android.permission.BLUETOOTH"/>```
