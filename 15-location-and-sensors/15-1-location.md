# Location

Some key considerations to keep in mind while dealing with locations are :

a. **Location Information**: There are a Multitude of location sources:

GPS, Cell-ID, and Wi-Fi can each provide a clue to users’ location. Determining which to use and trust is a matter of trade-offs in accuracy, speed, and battery-efficiency.

Another factor to consider is User movement, because the user location changes, you must account for movement by re-estimating user location every so often.

b. **Varying accuracy**: Location estimates coming from each location source are not consistent in their accuracy. A location obtained 10 seconds ago from one source might be more accurate than the newest location from another or same source.

We could choose one of the location providers \(network or GPS\) and request location updates or set up proximity alert. But there were two main issues with this approach:

a. In case we need to define precise location, we had to switch between network and GPS location providers \(as GPS doesn’t work indoors\).

b. Proximity alerts were used to notify a user about proximity to a location, and this took its toll on the battery life.

Hence, instead of using the default framework apis for location, we will be using **FusedLocationAPI** for the same.

Advantages of Using FusedLocationAPI:

a.** Simple APIs**: Lets us specify high-level needs like “high accuracy” or “low power”, instead of having to worry about location providers.

b. **Immediately available**: Gives our apps immediate access to the best, most recent location.

c. **Power-efficiency**: Minimize our app’s use of power. Based on all incoming location requests and available sensors, fused location provider chooses the most efficient way to meet those needs.

d. **Versatility**: Meets a wide range of needs, from foreground uses that need highly accurate location to background uses that need periodic location updates with negligible power impact.

_**Note**: Make sure Google Play services is properly installed and working in our device. Please don’t test this location api in emulator because this api is not working in the emulator_

###

### Getting the **Last Know**n Location:

- \*\* \*\*Import the google-play-services dependencies via gradle

`implementation'com.google.android.gms:play-services-location:8.4.0'`

- \*\* \*\*Add location Permissions to the Manifest file

```
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
```

- Connect to Google API client

This requires implementation of some functions by the activity handling location**:**

The **Google API Client** provides a common entry point to all the Google Play services and manages the network connection between the user's device and each Google service.

![](img/sensor-coordinate.png)

```
if (mGoogleApiClient == null) {
    mGoogleApiClient = new GoogleApiClient.Builder(this)
    .addConnectionCallbacks(this)
    .addOnConnectionFailedListener(this)
    .addApi(LocationServices.API)
    .build();
}
```

- Next up, we should also disconnect the mGoogleApiClient , whenever the activity stops, and then reconnect in OnStart\(\). So add required actions in life cycle methods.

```
protected void onStart() {
    super.onStart();
    mGoogleApiClient.connect();
}

protected void onStop() {
    mGoogleApiClient.disconnect();
    super.onStop();
}
```

- We will get Last Known Location in the OnConnected\(\) method as discussed in class.
  The getLastLocation\(\) method returns the best most recent location currently available \(as per your permissions\), it returns null if no location is available.

```
public void onConnected(@Nullable Bundle bundle) {
    lastLocation = LocationServices.FusedLocationApi.getLastLocation(mGoogleApiClient);
    if (lastLocation != null) {
        Log.i(TAG, "Latitude :" + lastLocation.getLatitude());Log.i(TAG, "Longitude :" + lastLocation.getLongitude());
    } else {
        Log.i(TAG, "Last Location Empty");
    }
    if (!requestedLocationUpdates) {
        startLocationUpdates();
    }
}
```

###

### **Setting Up Location Updates: **

In order to set up, we need to create a LocationRequest Object which takes some parameters.  
The parameters determine the level of accuracy for location requests:

#### i. Update interval

**setInterval\(long interval\)** - This method sets the rate in milliseconds at which your app prefers to receive location updates. Note that the location updates may be faster than this rate if another app is receiving updates at a faster rate, or slower than this rate, or there may be no updates at all \(if the device has no connectivity, for example\).

Link: developers.google.com/android/reference/com/google/android/gms/location/LocationRequest.html\#setInterval%28long%29

#### ii. Fastest update interval

**setFastestInterval\(long interval\)** - This method sets the fastest rate in milliseconds at which your app can handle location updates. You need to set this rate because other apps also affect the rate at which updates are sent. The Google Play services location APIs send out updates at the fastest rate that any app has requested with setInterval\(\). If this rate is faster than your app can handle, you may encounter problems with UI flicker or data overflow. To prevent this, call setFastestInterval\(\) to set an upper limit to the update rate.

Link: developers.google.com/android/reference/com/google/android/gms/location/LocationRequest.html\#setFastestInterval%28long%29

#### iii. Priority

**setPriority\(int priority\)** - This method sets the priority of the request, which gives the Google Play services location services a strong hint about which location sources to use.

Link: developers.google.com/android/reference/com/google/android/gms/location/LocationRequest.html\#setPriority%28int%29

In the setPriority method, the following values are supported:

i. **Priority Balanced Power Accuracy**: Use this setting to request location precision to within a city block, which is an accuracy of approximately 100 meters. This is considered a coarse level of accuracy, and is likely to consume less power. With this setting, the location services are likely to use WiFi and cell tower positioning.

Link:developers.google.com/android/reference/com/google/android/gms/location/LocationRequest.html\#PRIORITY_BALANCED_POWER_ACCURACY

ii. **Priority High Accuracy**: Use this setting to request the most precise location possible. With this setting, the location services are more likely to use GPS to determine the location.

Link:developers.google.com/android/reference/com/google/android/gms/location/LocationRequest.html\#PRIORITY_HIGH_ACCURACY

iii. **Priority Low Power**: Use this setting to request city-level precision, which is an accuracy of approximately 10 kms. This is considered a coarse level of accuracy, and is likely to consume less power.

Link:developers.google.com/android/reference/com/google/android/gms/location/LocationRequest.html\#PRIORITY_LOW_POWER

iv. **Priority No Power**: Use this setting if you need negligible impact on power consumption, but want to receive location updates when available. With this setting, your app does not trigger any location updates, but receives locations triggered by other apps. Link:developers.google.com/android/reference/com/google/android/gms/location/LocationRequest.html\#PRIORITY_NO_POWER

### Getting the location updates :

In the onConnected method,, check if you are already registered to listen to updates and if not, start listening to updates:

```
if(!requestingLocationUpdates){
    startLocationUpdates();
}
```

Set up the following function to start listening to location updates:

```
private void startLocationUpdates() {
    locationRequest = LocationRequest.create()
    .setPriority(LocationRequest.PRIORITY_BALANCED_POWER_ACCURACY)
    .setInterval(10000)
    .setFastestInterval(2000);
locationServices.FusedLocationApi.requestLocationUpdates(mGoogleApiClient,locationRequest,this);
}
```

This gives a function callback onLocationChanged wherein you can do whatever you want with the new location:

```
@Override
public void onLocationChanged(Location location) {
    Log.e("Locatiopn Update", location.getLongitude() + " " + location.getLatitude());
}
```

Whenever out activity loses focus, we won’t be requiring location updates, so we should stop location updates in onPause\(\) lifecycle method, and start receiving updates in corresponding lifecycle method i.e in onResume\(\).

```
@Override
protected void onPause(){
    super.onPause();
    stopLocationUpdates();
}

@Override
public void onResume() {
    super.onResume();
    if (googleApiClient.isConnected() && !requestingLocationUpdates) {
        startLocationUpdates();
    }
}

protected void stopLocationUpdates() {
    LocationServices.FusedLocationApi.removeLocationUpdates(googleApiClient, this);
}
```
