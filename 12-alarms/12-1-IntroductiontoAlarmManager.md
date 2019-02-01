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
