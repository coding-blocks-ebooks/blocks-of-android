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


* Create a new project in Android Studi New Project and fill the project details.

* Open `strings.xml` and add the string values.

```xml
<resources>
<string name="app_name">M Permissions</string>
<string name="action_settings">Settings</string>
<string name="single_permission_text">(Click on FAB to check Single permission)</string>
</resources>```