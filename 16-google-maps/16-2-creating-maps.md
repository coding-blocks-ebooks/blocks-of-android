# Initializing the Map

**Declaring Map Types**
* In our `MapFragment` class, you need to define some global values at the top of the class for use in your application.

```java
private GoogleApiClient mGoogleApiClient;
private Location mCurrentLocation;
private final int[] MAP_TYPES = {
    GoogleMap.MAP_TYPE_SATELLITE,
    GoogleMap.MAP_TYPE_NORMAL,
    GoogleMap.MAP_TYPE_HYBRID,
    GoogleMap.MAP_TYPE_TERRAIN,
    GoogleMap.MAP_TYPE_NONE
};
private int curMapTypeIndex = 0;```
* `mGoogleApiClient` and `mCurrentLocation` are used for getting the user's location for initializing the map camera.

* `MAP_TYPES` and `curMapTypeIndex` are used in the sample code for switching between different map display types.

**Creating the API Client**

* You need to create your `GoogleApiClient` and initiate LocationServices in order to get your user's current location.

```java
@Override
public void onViewCreated(View view, Bundle savedInstanceState) {
    super.onViewCreated(view, savedInstanceState);
    setHasOptionsMenu(true);
    mGoogleApiClient = new GoogleApiClient.Builder(getActivity())
        .addConnectionCallbacks(this)
        .addOnConnectionFailedListener(this)
        .addApi(LocationServices.API)
        .build();
    initListeners();
}```

* The `initListeners` method binds the interfaces that you declared at the top of the class with the GoogleMap object associated with `SupportMapFragment`.

```java
private void initListeners() {
    getMap().setOnMarkerClickListener(this);
    getMap().setOnMapLongClickListener(this);
    getMap().setOnInfoWindowClickListener(this);
    getMap().setOnMapClickListener(this);
}```

* `GoogleApiClient` and listeners are created and bound from `onViewCreated` rather than the `onCreate`.

* This is because the GoogleMap object has not been initialized when `onCreate` is called, so we need to wait until the view is fully created before trying to call `getMap` in order to avoid a `NullPointerException`.

**Configuring the Map**

* We will set up the map camera after the user's location has been found through Play Services, we will use the Play Services lifecycle to drive initializing our map. You can connect the `GoogleApiClient` in onStart.

```java
@Override
public void onStart() {
    super.onStart();
    mGoogleApiClient.connect();
}
@Override
public void onStop() {
    super.onStop();
    if (mGoogleApiClient != null && mGoogleApiClient.isConnected()) {
        mGoogleApiClient.disconnect();
    }
}
@Override
public void onConnected(Bundle bundle) {
    mCurrentLocation = LocationServices
        .FusedLocationApi
        .getLastLocation(mGoogleApiClient);
    initCamera(mCurrentLocation);
}```

* `initCamera` method, you initialize the camera and some basic map properties. You start by creating a CameraPosition object through the `CameraPosition.Builder`, with a target set for the latitude and longitude of your user and a set zoom level.

```java
private void initCamera( Location location ) {
  CameraPosition position = CameraPosition.builder()
    .target( new LatLng( location.getLatitude(),location.getLongitude() ) )
    .zoom( 16f )
    .bearing( 0.0f )
    .tilt( 0.0f )
    .build();
  getMap().animateCamera(   CameraUpdateFactory.newCameraPosition( position ), null );
  getMap().setMapType( MAP_TYPES[curMapTypeIndex] );
  getMap().setTrafficEnabled( true );
  getMap().setMyLocationEnabled( true );
  getMap().getUiSettings().setZoomControlsEnabled( true );
}```

* `setMyLocationEnabled` adds a button to the top right corner of the `MapFragment` that automatically moves the camera to your user's location when pressed.

* `setZoomControlsEnabled` adds + and - buttons in the lower right corner, allowing the user to change the map zoom level without having to use gestures.


# Marking the map .

* The most used map features involves indicating locations with markers. Since a latitude and longitude are needed for adding a marker, you need to use the `OnMapClickListener` to allow the user to pick a spot on the map to place a Marker object.

```java
@Override
public void onMapClick(LatLng latLng) {
    MarkerOptions options = new MarkerOptions().position(latLng);
    options.title(getAddressFromLatLng(latLng));
    options.icon(BitmapDescriptorFactory.defaultMarker());
    getMap().addMarker(options);
}```

* This method creates a generic red marker where the user has tapped. Additional options, such as setting a marker as draggable, can be set through the `MarkerOptions` object .

* The `defaultMarker` method accepts a float value that defines the hue.

* `addMarker` returns a Marker object, which can be stored for manually removing specific markers if needed.


# Drawing on map .

* The `GoogleMap` object has a set of methods that make it easy to draw shapes and place images onto the map.

* To draw a simple circle, you only need to create a `CircleOptions` object, set a radius and center location, and define the stroke/fill colors and size.

* You have a `CircleOptions` object, you can call `addCircle` to draw the defined circle on top of the map. Just like when placing markers, objects that are drawn on the map return an object of the drawn item type so it can be referenced later .

```java
private void drawCircle( LatLng location ) {
  CircleOptions options = new CircleOptions();
  options.center( location );
  //Radius in meters
  options.radius( 10 );
  options.fillColor( getResources().getColor( R.color.fill_color ) );
  options.strokeColor( getResources().getColor( R.color.stroke_color ) );
  options.strokeWidth( 10 );
  getMap().addCircle(options);
}
```

* To draw a different closed-off shape, you can take multiple LatLng points and create a PolygonOptions object.

* PolygonOptions are created in a similar fashion to CircleOptions. Instead of using a center and radius method, you use add with a list of points. You can then call addPolygon to draw the shape.

```java
private void drawPolygon( LatLng startingLocation ) {
  LatLng point2 = new LatLng( startingLocation.latitude +   .001,startingLocation.longitude );
  LatLng point3 = new LatLng( startingLocation.latitude,startingLocation.longitude + .001 );
  PolygonOptions options = new PolygonOptions();
  options.add( startingLocation, point2, point3 );
  options.fillColor( getResources().getColor(R.color.fill_color ) );
  options.strokeColor( getResources().getColor( R.color.stroke_color ) );
  options.strokeWidth( 10 );
  getMap().addPolygon( options );
}```
