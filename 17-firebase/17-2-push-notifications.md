# Firebase Push Notifications

Push technology is a way of transferring messages from server to user in a forced or timed manner. Most often, these technologies are used for mass distribution of news or useful information to application users.

Firebase gives developers the ability not only to send notifications but also to transfer point-by-point messages, the recipient of which can be a single device. How to send a notification to Android app?

# Implementation of push notifications .

- Setup a new studio project and select `BlankActivity` .

- Go to the tools on the top pane and click on firebase .

- Copy the Needed JSON File into the project .

- In order to use Google services plug-ins, you’ll need to edit the build.gradle (app )file in Android Studio.

```java
buildscript {

    dependencies {

        classpath 'com.google.gms:google-services:3.0.0'

    }

}
```

- Add the following line to the end of the build.gradle file.

```java
apply plugin: 'com.google.gms.google-services
```

- Now we add the Firebase dependencies .

```java
dependencies {

  compile 'com.google.Firebase:Firebase-core:10.2.1' //The string required for Firebase integration
  compile 'com.google.Firebase:Firebase-messaging:10.2.1' //The string required to make Firebase Cloud Messaging available

}
```

\*

Then, update the services using com.google.android.gms: play-services

- Finally, add applicationId to defaultConfig .

```java
android {
    defaultConfig {
        applicationId "com.example.my.app" //your app’s id
    }
}
```

- Add the Service to the Application .

- To use FCM in the application, two services must be added to it , the notifications operation testing code, and code for the processing of received/sent messages and their integration into your design.

- To receive notifications, we must add a service that extends the FirebaseMessagingService capabilities.

```java
public class MyFirebaseMessagingService extends FirebaseMessagingService {

    private static final String TAG = "FCM Service";

    @Override

    public void onMessageReceived(RemoteMessage remoteMessage) {

        Log.d(TAG, "From: " + remoteMessage.getFrom());

        Log.d(TAG, "Notification Message Body: " + remoteMessage.getNotification().getBody());

    }
}
```

- Next, add the service to `AndroidManifest.xml`

```xml
<service android:name = ".MyFirebaseMessagingService" >
    <intent-filter >
        <action android:name = "com.google.Firebase.MESSAGING_EVENT" / >
    </intent-filter>
</service>
```

- Add the service that extends FirebaseInstanceIdService .

```java
public class FirebaseIDService extends FirebaseInstanceIdService {

    private static final String TAG = "FirebaseIDService";

    @Override

    public void onTokenRefresh() {

        String refreshedToken = FirebaseInstanceId.getInstance().getToken();

        Log.d(TAG, "Refreshed token: " + refreshedToken);
    }
    private void sendRegistrationToServer(String token) {}
}
```

- Provide the download service by adding the service to the `AndroidManifest.xml` file.

```xml
<service android:name = ".MyFirebaseMessagingService" >
    <intent-filter >
        <action android:name = "com.google.Firebase.MESSAGING_EVENT" / >
    </intent-filter>
</service>
```

- Now go ahead and check your first push ! .
