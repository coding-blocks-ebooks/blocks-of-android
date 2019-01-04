# Alarm Manager

Daily we used to set Alarms in our phone to wake up in morning or for some meetings, etc. So today we are going to learn about Alarms. Alarms (based on the AlarmManager class) give you a way to perform time-based operations outside the lifetime of your application. For example, you could use an alarm to initiate a long-running operation, such as starting a service once a day to download a weather forecast. You can schedule your application to run at a specific time in the future. It works whether your phone is running or not.

The Alarm Manager holds a CPU wake lock as long as the alarm receiver's onReceive() method is executing. This guarantees that the phone will not sleep until you have finished handling the broadcast. Once onReceive() returns, the Alarm Manager releases this wake lock. This means that the phone will in some cases sleep as soon as your onReceive() method completes. If your alarm receiver called Context.startService(), it is possible that the phone will sleep before the requested service is launched. To prevent this, your BroadcastReceiver and Service will need to implement a separate wake lock policy to ensure that the phone continues running until the service becomes available.

## AlarmManager is having different characteristics mentioned below:

1. AlarmManager runs outside the lifetime of your application. Once an alarm is scheduled, it will invoke even when your application is not running or in sleep mode.
2. An scheduled alarm will execute unless it is stopped explicitly by calling cancel() method, or until device reboots.
3. All scheduled alarms will be stopped when device reboots. This means, you need to re-schedule them explicitly when device boot completes.
4. AlarmManger fires an Intent at given intervals. This can be used along with broadcast receivers to start a service to perform network operations.
5. AlarmManager is different form java Timer and TimerTask.

Android supports two clock types for alarm service; Elapsed real time and real time clock (RTC). Elapsed real time uses the time since device last booted. Real time clock (RTC) uses UTC time for alarm service clock. RTC is most commonly used for setting alarm service in android. The following example, using RTC to schedule alarm.

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

### Cancelling an Alarm
You also can cancel an alarm if you want, to do that, basically you just need to pass the same parameters used to set the alarm and call cancel instead of set or other methods to schedule an alarm. For this sample we use this code to cancel the alarm.
``` kotlin
// Request the AlarmManager object
 val manager = getSystemService(Context.ALARM_SERVICE) as AlarmManager

// Create the PendingIntent that would have launched the BroadcastReceiver
val pending = PendingIntent.getBroadcast(
                this, 0, 
                Intent(this, AlarmBroadcastReceiver::class.java), 0
        )

// Cancel the alarm associated with that PendingIntent
manager.cancel(pending)
```



