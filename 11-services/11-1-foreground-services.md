# Foreground Service

Foreground Service in android is a background service which keeps running even after the parent application is closed. And useful when you need user interface while performing long running operations.

Examples :

- Downloading a file in background from a server.
- Playing songs in background in a music player app.
- Displaying the status of Connection to the server for a chat messaging app.

## Implementation of Foreground Service.

- Build a new Android project .

- Open up `activity_main.xml`.

- Now , we're going to add 2 buttons , which is startService and stopService .

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.codingblocks_projects.stoned_coder.ForegroundServices.MainActivity">

    <Button android:id="@+id/btnStartService"
        android:layout_width="0dp"
        android:layout_weight="1"
        android:layout_height="wrap_content"
        android:text="Start Service" />

    <Button android:id="@+id/btnStopService"
        android:layout_width="0dp"
        android:layout_weight="1"
        android:layout_height="wrap_content"
        android:text="Start Service" />

</LinearLayout>
```

- create a `customview.xml` in which we're going to create some custom views for our notification panel .

````xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android" android:id="@+id/notification" android:layout_width="match_parent" android:layout_height="wrap_content" android:gravity="center" android:orientation="horizontal">

    <LinearLayout android:layout_width="match_parent" android:layout_height="wrap_content" android:layout_alignParentLeft="true" android:layout_toLeftOf="@+id/llPlayControls" android:padding="10dp" android:orientation="vertical" >

        <TextView android:textColor="#787878" android:id="@+id/notification_text_title" android:layout_width="match_parent" android:layout_height="wrap_content" android:singleLine="true" android:text="Song Title" android:textStyle="bold" android:ellipsize="end" android:lines="1" />

        <TextView android:textColor="#787878" android:id="@+id/notification_text_artist" android:layout_width="match_parent" android:layout_height="wrap_content" android:text="artist" android:lines="1" android:ellipsize="end" android:singleLine="true" />

    </LinearLayout>


    <LinearLayout android:id="@+id/llPlayControls" android:layout_alignParentRight="true" android:layout_width="wrap_content" android:layout_height="wrap_content" android:layout_centerVertical="true" android:orientation="horizontal">

        <ImageView android:id="@+id/notification_button_prev" android:layout_width="48dp" android:layout_height="48dp" android:src="@android:drawable/ic_media_previous" />
        <ImageView android:id="@+id/notification_button_play" android:layout_width="48dp" android:layout_height="48dp" android:src="@android:drawable/ic_media_play" />

        <ImageView android:id="@+id/notification_button_skip" android:layout_width="48dp" android:layout_height="48dp" android:src="@android:drawable/ic_media_next" />
        <ImageView android:id="@+id/notification_button_close" android:layout_width="48dp" android:layout_height="48dp" android:src="@android:drawable/ic_menu_close_clear_cancel" />
    </LinearLayout>




</RelativeLayout>```

* Now in the ```MainActivity.java``` file inside ``onCreate`` , we'll be geting refrence fo the buttons and defining 2 intents , one for start and the other being stop .

 ```java
   startButton=(Button)findViewById(R.id.btnStartService);
 stopButton =(Button)findViewById(R.id.btnStopService);

    startButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent startIntent = new Intent(MainActivity.this, ForegroundService.class);
                startIntent.setAction(Constants.ACTION.STARTFOREGROUND_ACTION);
                startService(startIntent);
            }
        });

    stopButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent stopIntent = new Intent(MainActivity.this, ForegroundService.class);
                stopIntent.setAction(Constants.ACTION.STOPFOREGROUND_ACTION);
                startService(stopIntent);
            }
        });
````

We'll create a new class and name that as `Constants.java`

- This class will contain 2 interfaces one for `Actions` and other for `ServiceID`

```java
public class Constants {

   public interface ACTION {
       public static String MAIN_ACTION = "com.stonedcoder.alertdialog.action.main";
       public static String STARTFOREGROUND_ACTION = "com.stonedcoder.alertdialog.action.startforeground";
       public static String STOPFOREGROUND_ACTION = "com.stonedcoder.alertdialog.action.stopforeground";
   }

   public interface NOTIFICATION_ID {
       public static int FOREGROUND_SERVICE = 101;
   }
}
```

- Now we'll create another class and name that `ForegroundService` which holds the logic for custom notification views and service .

```java
class ForegroundService extends Service {
    private static final String LOG_TAG = "ForegroundService";

    @Override
    public void onCreate() {
        super.onCreate();
    }

    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        if (intent.getAction().equals(Constants.ACTION.STARTFOREGROUND_ACTION)) {
            Toast.makeText(this,"Start Service",Toast.LENGTH_SHORT).show();
            Log.i(LOG_TAG, "Received Start Foreground Intent ");


            Intent notificationIntent = new Intent(this, MainActivity.class);
            notificationIntent.setAction(Constants.ACTION.MAIN_ACTION);
            notificationIntent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK
                    | Intent.FLAG_ACTIVITY_CLEAR_TASK);
            PendingIntent pendingIntent = PendingIntent.getActivity(this, 0,
                    notificationIntent, 0);

            RemoteViews notificationView = new RemoteViews(this.getPackageName(),R.layout.customviews);

            // And now, building and attaching the Play button.
            Intent buttonPlayIntent = new Intent(this, NotificationPlayButtonHandler.class);
            buttonPlayIntent.putExtra("action", "togglePause");

            PendingIntent buttonPlayPendingIntent = pendingIntent.getBroadcast(this, 0, buttonPlayIntent, 0);
            notificationView.setOnClickPendingIntent(R.id.notification_button_play, buttonPlayPendingIntent);

            // And now, building and attaching the Skip button.
            Intent buttonSkipIntent = new Intent(this, NotificationSkipButtonHandler.class);
            buttonSkipIntent.putExtra("action", "skip");

            PendingIntent buttonSkipPendingIntent = pendingIntent.getBroadcast(this, 0, buttonSkipIntent, 0);
            notificationView.setOnClickPendingIntent(R.id.notification_button_skip, buttonSkipPendingIntent);

            // And now, building and attaching the Skip button.
            Intent buttonPrevIntent = new Intent(this, NotificationPrevButtonHandler.class);
            buttonPrevIntent.putExtra("action", "prev");

            PendingIntent buttonPrevPendingIntent = pendingIntent.getBroadcast(this, 0, buttonPrevIntent, 0);
            notificationView.setOnClickPendingIntent(R.id.notification_button_prev, buttonPrevPendingIntent);

            // And now, building and attaching the Close button.
            Intent buttonCloseIntent = new Intent(this, NotificationCloseButtonHandler.class);
            buttonCloseIntent.putExtra("action", "close");

            PendingIntent buttonClosePendingIntent = pendingIntent.getBroadcast(this, 0, buttonCloseIntent, 0);
            notificationView.setOnClickPendingIntent(R.id.notification_button_close, buttonClosePendingIntent);



            Bitmap icon = BitmapFactory.decodeResource(getResources(),
                    R.mipmap.ic_launcher);

            Notification notification = new NotificationCompat.Builder(this)
                    .setContentTitle("nkDroid Music Player")
                    .setTicker("nkDroid Music Player")
                    .setContentText("nkDroid Music")
                    .setSmallIcon(R.mipmap.ic_launcher)
                    .setLargeIcon(
                            Bitmap.createScaledBitmap(icon, 128, 128, false))
                    .setContent(notificationView)
                    .setOngoing(true).build();



            startForeground(Constants.NOTIFICATION_ID.FOREGROUND_SERVICE,
                    notification);
        }


        else if (intent.getAction().equals(Constants.ACTION.STOPFOREGROUND_ACTION)) {
            Toast.makeText(this,"Stop Service",Toast.LENGTH_SHORT).show();
            Log.i(LOG_TAG, "Received Stop Foreground Intent");
            stopForeground(true);
            stopSelf();
        }
        return START_STICKY;
    }

    @Override
    public void onDestroy() {
        super.onDestroy();
        Log.i(LOG_TAG, "In onDestroy");
    }

    @Override
    public IBinder onBind(Intent intent) {
        // Used only in case of bound services.
        return null;
    }


    /**
     * Called when user clicks the "play/pause" button on the on-going system Notification.
     */
    public static class NotificationPlayButtonHandler extends BroadcastReceiver {
        @Override
        public void onReceive(Context context, Intent intent) {
            Toast.makeText(context,"Play Clicked",Toast.LENGTH_SHORT).show();

        }
    }

    /**
     * Called when user clicks the "skip" button on the on-going system Notification.
     */
    public static class NotificationSkipButtonHandler extends BroadcastReceiver {
        @Override
        public void onReceive(Context context, Intent intent) {
            Toast.makeText(context,"Next Clicked",Toast.LENGTH_SHORT).show();
        }
    }

    /**
     * Called when user clicks the "previous" button on the on-going system Notification.
     */
    public static class NotificationPrevButtonHandler extends BroadcastReceiver {
        @Override
        public void onReceive(Context context, Intent intent) {
            Toast.makeText(context,"Previous Clicked",Toast.LENGTH_SHORT).show();
        }
    }

    /**
     * Called when user clicks the "close" button on the on-going system Notification.
     */
    public static class NotificationCloseButtonHandler extends BroadcastReceiver {
        @Override
        public void onReceive(Context context, Intent intent) {
            Toast.makeText(context,"Close Clicked",Toast.LENGTH_SHORT).show();

        }
    }

}
```

- Now open up `AndroidManifest.xml` and add recivers and service to the manifest .

`````xml
<receiver android:name=".ForegroundService$NotificationCloseButtonHandler" />
        <receiver android:name=".ForegroundService$NotificationPlayButtonHandler" />
        <receiver android:name=".ForegroundService$NotificationPrevButtonHandler" />
        <receiver android:name=".ForegroundService$NotificationSkipButtonHandler" />

        <service android:name=".ForegroundService" />````
`````
