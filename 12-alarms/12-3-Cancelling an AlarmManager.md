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



