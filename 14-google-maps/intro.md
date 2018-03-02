# Google Maps

With Google Maps Android API v2, you can embed maps into an activity as a fragment with a simple XML snippet. The new Maps offer exciting features such as 3D maps; indoor, satellite, terrain, and hybrid maps; vector-based tiles for efficient caching and drawing; animated transitions; and much more.

# Implementation of google maps

you can create the initial Android project with the same package name that you used for creating the API key. Once your project is created, open the build.gradle file. You need to import the Play Services library for maps.

```java
implementation 'com.google.android.gms:play-services-maps:7.8.0'
implementation 'com.google.android.gms:play-services-location:7.8.0'```


* Open your `AndroidManifest.xml` file

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />```

* Within the application node, you need to add two pieces of metadata.

* Within the application node, you need to add two pieces of metadata.

* `@string/google_api_key` is a string reference to the key from the Google Developer Console.

```xml
<meta-data
android:name="com.google.android.gms.version"
android:value="@integer/google_play_services_version" />
<meta-data
android:name="com.google.android.geo.API_KEY"
android:value="@string/google_api_key"/>```
* Next, you need to create a new Java class, called `MapFragment`, which extends `SupportMapFragment`. `SupportMapFragment` is used here rather than `com.google.android.gms.maps.MapFragment` in order to add backwards compatibility before API 12.

* Once you have the base fragment created, you need to implement the six interface

```java
public class MapFragment extends SupportMapFragment implements GoogleApiClient.ConnectionCallbacks,
GoogleApiClient.OnConnectionFailedListener,
GoogleMap.OnInfoWindowClickListener,
GoogleMap.OnMapLongClickListener,
GoogleMap.OnMapClickListener,
GoogleMap.OnMarkerClickListener {````
* `ConnectionCallbacks` and `OnConnectionFailedListener` are designed to monitor the state of the GoogleApiClient, which is used in this application for getting the user's current location.

* `OnInfoWindowClickListener` is triggered when the user clicks on the info window that pops up over a marker on the map.

* `OnMapLongClickListener`and `OnMapClickListener` are triggered when the user either taps or holds down on a portion of the map.

* `OnMarkerClickListener` is called when the user clicks on a marker on the map, which also displays the info window for that marker.

* Now , with all this set up open `activity_main.xml`

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
android:layout_height="match_parent"
tools:context=".MainActivity">
<fragment
android:id="@+id/map"
android:name="com.Stonedcoder.googlemaps.MapFragment"
android:layout_width="match_parent"
android:layout_height="match_parent"/>
</RelativeLayout>```

* Now you should be able to run your application and view a map of Earth that is fully zoomed out and focused on latitude 0, longitude 0.