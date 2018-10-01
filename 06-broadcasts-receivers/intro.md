# BroadcastReceivers

BroadcastReceiver is one of the building blocks in Android. You can sendBroadcast with an action in an Intent and the Receiver can receive this action and do something about it. Here is an example of sending some custom intents and a receiver will receive them and act accordingly.

## Implementation of a BroadcastReceiver

- Build a new project create a class called `SimpleReciver`.

`````java
public class SimpleReceiver extends BroadcastReceiver {

    @Override
    public void onReceive(Context context, Intent intent) {
        if (intent.getAction() == "com.example.simplebroadcastreceiver.CUSTOM_INTENT1") {
            Toast.makeText(context, "Intent 1 Detected.", Toast.LENGTH_SHORT).show();
        } else if (intent.getAction() == "com.example.simplebroadcastreceiver.CUSTOM_INTENT2") {
            Toast.makeText(context, "Intent 2 Detected.", Toast.LENGTH_SHORT).show();
        }
    }

}```

* Register the above class in the manifest .

```xml
<receiver android:name=".SimpleReceiver">
    <intent-filter>
        <action android:name="com.example.simplebroadcastreceiver.CUSTOM_INTENT1"></action>
        <action android:name="com.example.simplebroadcastreceiver.CUSTOM_INTENT2"></action>
    </intent-filter>
</receiver> ````

* Create an Activity class to send the broadcast intents. When one of the button is clicked, it sends a broadcast with an action in an Intent, and that intent will be received in the `onReceive()` method in the `SimpleReceiver` class.

```java
public class MainActivity extends AppCompatActivity {

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }

    // broadcast a custom intent.
    public void broadcastIntent1(View view) {
        Intent intent = new Intent();
        intent.setAction("com.example.simplebroadcastreceiver.CUSTOM_INTENT1");
        sendBroadcast(intent);
    }

    // broadcast a custom intent.
    public void broadcastIntent2(View view) {
        Intent intent = new Intent();
        intent.setAction("com.example.simplebroadcastreceiver.CUSTOM_INTENT2");
        sendBroadcast(intent);
    }
}```

* Now , open up `activity_main.xml` for the layout .
* Create 2 buttons for `Intent1` and `Intent2` .

```xml

    <Button android:id="@+id/btn_broadcast_intent1"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="Broadcast Action 1"
        android:onClick="broadcastIntent1"/>

    <Button android:id="@+id/btn_broadcast_intent2"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="Broadcast Action 2"
        android:onClick="broadcastIntent2"/>
`````
