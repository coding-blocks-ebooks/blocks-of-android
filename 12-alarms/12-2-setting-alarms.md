Setting the alarms is very simple.

We need to get access to the AlarmManager class and then using that we can schedule either a repeating or a one shot alarm for our app.

Some examples are given below : 

One time Alarm with **ELAPSED\_REALTIME**_**\_**_**WAKEUP** 

```java
private AlarmManager alarmMgr;
private PendingIntent alarmIntent;
...
alarmMgr = (AlarmManager)context.getSystemService(Context.ALARM_SERVICE);
Intent intent = new Intent(context, AlarmReceiver.class);
alarmIntent = PendingIntent.getBroadcast(context, 0, intent, 0);

alarmMgr.set(AlarmManager.ELAPSED_REALTIME_WAKEUP,
        SystemClock.elapsedRealtime() +
        60 * 1000, alarmIntent);
```

One time Alarm with **ELAPSED\_REALTIME**

```
private AlarmManager alarmMgr;
private PendingIntent alarmIntent;
...
alarmMgr = (AlarmManager)context.getSystemService(Context.ALARM_SERVICE);
Intent intent = new Intent(context, AlarmReceiver.class);
alarmIntent = PendingIntent.getBroadcast(context, 0, intent, 0);

alarmMgr.set(AlarmManager.ELAPSED_REALTIME,
        SystemClock.elapsedRealtime() + 60 * 1000, alarmIntent);
```

One time Alarm with **RTC\_WAKEUP** 

```java
// Set the alarm to start at approximately 2:00 p.m.
Calendar calendar = Calendar.getInstance();
calendar.setTimeInMillis(System.currentTimeMillis());
calendar.set(Calendar.HOUR_OF_DAY, 14);

alarmMgr.set(AlarmManager.RTC_WAKEUP,
        SystemClock.elapsedRealtime() + 60 * 1000, alarmIntent);
```

Repeating Alarm with **ELAPSED\_REALTIME**_**\_**_**WAKEUP**

```java
alarmMgr.setInexactRepeating(AlarmManager.ELAPSED_REALTIME_WAKEUP,
        SystemClock.elapsedRealtime() + AlarmManager.INTERVAL_HALF_HOUR,
        AlarmManager.INTERVAL_HALF_HOUR, alarmIntent);        
```

Repeating Alarm with **RTC\_WAKEUP**

```java
//Wake up the device to fire the alarm at approximately 2:00 p.m., and repeat once a day at the same time:
// Set the alarm to start at approximately 2:00 p.m.
Calendar calendar = Calendar.getInstance();
calendar.setTimeInMillis(System.currentTimeMillis());
calendar.set(Calendar.HOUR_OF_DAY, 14);

// With setInexactRepeating(), you have to use one of the AlarmManager interval
// constants--in this case, AlarmManager.INTERVAL_DAY.
alarmMgr.setInexactRepeating(AlarmManager.RTC_WAKEUP, calendar.getTimeInMillis(),
        AlarmManager.INTERVAL_DAY, alarmIntent);
```



