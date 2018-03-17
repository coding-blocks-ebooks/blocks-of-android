Depending on your app, we may want to include the ability to cancel the alarm. 

To cancel an alarm, we can call `cancel() ` on the Alarm Manager, passing in the `PendingIntent` which we no longer want to fire.   
For example:

```
// If the alarm has been set, cancel it.

if (alarmMgr!= null) {
    alarmMgr.cancel(alarmIntent);
}
```



