## So, how we use it to schedule our tasks?

### Get AlarmManager instance from the system services
``` kotlin
val alarmMgr = getSystemService(Context.ALARM_SERVICE) as AlarmManager
```

### Create the Intent and PendingIntent to pass in AlarmManager

``` kotlin
// Intent to start the Broadcast Receiver
val broadcastIntent = Intent(this, AlarmBroadcastReceiver::class.java)

// The Pending Intent to pass in AlarmManager
val pIntent = PendingIntent.getBroadcast(this,0,broadcastIntent,0)
```

On these two lines we create two variables:
1- Intent: this is responsible to prepare the android component what PendingIntent will start when the alarm is triggered. That component can be anyone (activity, service, broadcastReceiver, etc).
2- PendingIntent: this is the pending intent, which waits until the right time, to be called by AlarmManager.

### Start a BroadcastReceiver 
This broadcast receiver will show a notification in status bar, when the alarm is triggered. Here’s the broadcast called:

``` kotlin
class AlarmBroadcastReceiver : BroadcastReceiver() {

    override fun onReceive(context: Context?, intent: Intent?) {

        // Create the notification to be shown
        val mBuilder = NotificationCompat.Builder(context!!, "my_app")
                .setSmallIcon(R.mipmap.ic_launcher)
                .setContentTitle("Alarm Manager")
                .setContentText("It`s time to wake up")
                .setPriority(NotificationCompat.PRIORITY_DEFAULT)

        // Get the Notification manager service
        val am = context.getSystemService(Context.NOTIFICATION_SERVICE) as NotificationManager

        // Generate an Id for each notification
        val id = System.currentTimeMillis()/1000

        // Show a notification
        am.notify(id.toInt(), mBuilder.build())
    }
}
```

### Register the Receiver
Next, We need to register the BroadcastReceiver in our manifest file, so that it can be called even if the app is not running.

``` xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.pedromassango.alarmmanagersample">

    <application
        ... >
        <activity android:name=".MainActivity">
            ...
        </activity>
        
        <!-- Registering the BroadcastReceiver !-->
        <receiver android:name=".AlarmBroadcastReceiver"/>
        
    </application>
</manifest>
```

### Put all things together
Now we need to connect the PendingIntent with our AlarmManager to be called when the alarm is triggered, we do that in the method responsible for scheduling our alarm, go ahead and call this method where you want to set the alarm.

``` kotlin
// Set an alarm to trigger 5 second after this code is called
            alarmMgr.set(
                   AlarmManager.RTC_WAKEUP,
                    System.currentTimeMillis() + 5000,
                    pIntent
            ) // stop here
```

This method give three parameters:
1- type: this is the alarm time type, it can be(click on each one to know more):
RTC_WAKEUP, RTC, ELAPSED_REALTIME_WAKEUP or ELAPSED_REALTIME.
2- triggerAtMillis: time in milliseconds that the alarm should trigger. In this sample we set current time plus 5000 (5 second after this code is called).
3- operation: the pendingIntent called when the alarm is triggered.