# **Sensors**

Different devices have avariety of sensorsthat can be accessed via the Sensor framework.

Link:[http://developer.android.com/guide/topics/sensors/sensors_overview.html](http://developer.android.com/guide/topics/sensors/sensors_overview.html)

### Possible tasks related to sensors include:

1. List available sensors
2. Determine sensor capabilities \(range, resolution, etc.\)
3. Acquire raw sensor data
4. Register sensor event listeners

Common sensors that devices have available are for temperature, light, pressure, acceleration, motion, and orientation. Here's a list of guides:

1. ** Motion Sensors:**[http://developer.android.com/guide/topics/sensors/sensors_motion.html](http://developer.android.com/guide/topics/sensors/sensors_motion.html)
2. ** Position Sensors:**[http://developer.android.com/guide/topics/sensors/sensors_position.html](http://developer.android.com/guide/topics/sensors/sensors_position.html)
3. ** Environment Sensors:**[http://developer.android.com/guide/topics/sensors/sensors_environment.html](http://developer.android.com/guide/topics/sensors/sensors_environment.html)

### **Sensor Coordinate System**

![](img/sensor-coordinate.png)

A list of steps involved with getting data from sensors are :

#### **Initiate a SensorManager:**

`private SensorManager sensorManager;`

#### **Get a list of available sensors in onCreate:**

```
sensorManager= (SensorManager) getSystemService(Context.SENSOR_SERVICE);

// Get the default sensor of specified type
List<Sensor> deviceSensors =mSensorManager.getSensorList(Sensor.TYPE_GRAVITY);
    for( Sensor sensor : deviceSensors){
        Log.e("SEnsor Found", sensor.toString());
    }
```

#### **Get a specific sensor:**

```
Sensor lightSensor= sensorManager.getDefaultSensor(Sensor.TYPE_PROXIMITY);
```

Following are some properties available on a sensor are:

getResolution  
getPower  
getVendor  
getVersion

#### **Register for the right sensor in life cycle:**

```
@Override
protected voidonResume() {
    super.onResume();
    if(mLight!=null) {
        sensorManager.registerListener(lightSensorListener, lightSensor, SensorManager.SENSOR_DELAY_NORMAL);
    }
}

@Override
protected void onPause() {
    super.onPause();
    if(lightSensor!=null) {
        sensorManager.unregisterListener(mLightSensorListener);
    }
}
```

#### ** Handle Callback:**

This callback is responsible for getting us the values whenever the sensor updates.

```
private SensorEventListener lightSensorListener = newSensorEventListener() {
    @Override
    public voidonSensorChanged(SensorEvent event) {
        Log.d("MY_APP", event.toString());
        Log.e("event", event.values[0] +""); //the values array is a float array representing the value across different coordinate systems
    }

    @Override
    public void onAccuracyChanged(Sensor sensor, int accuracy) {
        Log.d("MY_APP", sensor.toString() +" - "+ accuracy);
    }
};
```

These two functions implemented are called when:

1. **A sensor's accuracy changes:**

In this case the system invokes the [onAccuracyChanged\(\)](http://developer.android.com/reference/android/hardware/SensorEventListener.html#onAccuracyChanged%28android.hardware.Sensor, int%29) method, providing you with a reference to the [Sensor](http://developer.android.com/reference/android/hardware/Sensor.html) object that changed and the new accuracy of the sensor.

Accuracy is represented by one of four status constants:

- [SENSOR_STATUS_ACCURACY_LOW](http://developer.android.com/reference/android/hardware/SensorManager.html#SENSOR_STATUS_ACCURACY_LOW)
- [SENSOR_STATUS_ACCURACY_MEDIUM](http://developer.android.com/reference/android/hardware/SensorManager.html#SENSOR_STATUS_ACCURACY_MEDIUM)
- [SENSOR_STATUS_ACCURACY_HIGH](http://developer.android.com/reference/android/hardware/SensorManager.html#SENSOR_STATUS_ACCURACY_HIGH)
- [SENSOR_STATUS_UNRELIABLE](http://developer.android.com/reference/android/hardware/SensorManager.html#SENSOR_STATUS_UNRELIABLE)

  1. **A sensor reports a new value: **

In this case the system invokes the [onSensorChanged\(\)](http://developer.android.com/reference/android/hardware/SensorEventListener.html#onSensorChanged%28android.hardware.SensorEvent%29) method, providing you with a [SensorEvent](http://developer.android.com/reference/android/hardware/SensorEvent.html) object.  
A [SensorEvent](http://developer.android.com/reference/android/hardware/SensorEvent.html) object contains information about the new sensor data, including:

- The accuracy of the data
- The sensor that generated the data
- The timestamp at which the data was generated
- The new data that the sensor recorded.

For every sensor, event contains an array of values, each of which has a different meaning, for example \(environmental sensors\).

_**Note** : To declare that a sensor is absolutely required by your application:_

```
<uses-feature
    android:name="android.hardware.sensor.accelerometer"
    android:required="true"/>
```

_If the device does not support the above hardware, your application won’t show in their play store \(incompatible\)_
