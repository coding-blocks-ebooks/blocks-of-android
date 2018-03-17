# Type of Alarms

There are two general clock types for alarms: "**elapsed real time**" and "**real time clock**" \(RTC\). 

Elapsed real time uses the "time since system boot" as a reference, and real time clock uses UTC \(wall clock\) time. This means that elapsed real time is suited to setting an alarm based on the passage of time \(for example, an alarm that fires every 30 seconds\) since it isn't affected by time zone/locale. The real time clock type is better suited for alarms that are dependent on current locale.

Both types have a "wakeup" version, which says to wake up the device's CPU if the screen is off. This ensures that the alarm will fire at the scheduled time. This is useful if our app has a time dependency—for example, if it has a limited window to perform a particular operation.   
If we don't use the wakeup version of the alarm type, then all the repeating alarms will fire when our device is next awake.

However, If we simply need the alarm to fire at a particular interval \(for example, every half hour\), using one of the elapsed real time types is a good idea as in general, this is the better choice.

If you need your alarm to fire at a particular time of day, then choose one of the clock-based real time clock types. 

Here is the list of types:

* [`ELAPSED_REALTIME`](https://developer.android.com/reference/android/app/AlarmManager.html#ELAPSED_REALTIME) —Fires the pending intent based on the amount of time since the device was booted, but doesn't wake up the device. The elapsed time includes any time during which the device was asleep.
* [`ELAPSED_REALTIME_WAKEUP`](https://developer.android.com/reference/android/app/AlarmManager.html#ELAPSED_REALTIME_WAKEUP) —Wakes up the device and fires the pending intent after the specified length of time has elapsed since device boot.
* [`RTC`](https://developer.android.com/reference/android/app/AlarmManager.html#RTC) —Fires the pending intent at the specified time but does not wake up the device.
* [`RTC_WAKEUP`](https://developer.android.com/reference/android/app/AlarmManager.html#RTC_WAKEUP) —Wakes up the device to fire the pending intent at the specified time.



