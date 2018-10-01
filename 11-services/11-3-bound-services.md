## Bound Service.

A bound service is the server in a client-server interface. It allows components (such as activities) to bind to the service, send requests, receive responses, and perform interprocess communication (IPC).They can send data to the launching component (client).

## Implementation of Bound Service .

Binding is done using the Context.bindService() method whereas it is terminated with Context.unbindService(). The binding is also destroyed if the client component’s lifecycle ends.

When a client component binds to a Bound Service it retrieves a communication interface for sending requests and receiving responses within the process or even across processes. It returns an IBinder interface implementation from its `onBind()` method through the ServiceConnection interface supplied by the binding client component when invoking `bindService()`.

- Build a new project .
- Create a new class and name that as a `TestService`.

````java
public class TestService extends Service {

    private String TAG = "TestService";

    private IBinder myBinder = new MyBinder();

    public TestService() {
    }

    @Override
    public void onCreate() {
        Log.d(TAG, "onCreate called");
    }

    @Override
    public IBinder onBind(Intent intent) {
        Log.d(TAG, "onBind done");
        return myBinder;
    }

    @Override
    public boolean onUnbind(Intent intent) {
        return false;
    }

    public class MyBinder extends Binder {
        TestService getService() {
            return TestService.this;
        }
    }


    // Methods used by the binding client components

    public void methodOne() {
        // Some code...
    }

    public void methodTwo() {
        // Some code...
    }

}```

* The `onBind()` method is called when the client component binds to the Service for the first time through `bindService().`

*  It returns an IBinder implementation that the client can use to interact with the Service by calling methods in the Service class. This IBinder
implementation is basically a communication interface to send requests and receive responses either within a particular process or across processes.

* `onUnbind()` is called when all bindings are unbound.

* The IBinder is returned to the client through a ServiceConnection interface that the client has to supply while binding.

```java
Intent intent = new Intent(this, TestService.class);
bindService(intent, serviceConnection, Context.BIND_AUTO_CREATE);
````

- `Intent` defines the Service to bind to and shouldn’t contain any other extra data.

- The binding client has to provide a ServiceConnection

````java
TestService testService;
boolean isBound = false;

private ServiceConnection serviceConnection = new ServiceConnection() {
    @Override
    public void onServiceConnected(ComponentName name, IBinder service) {
        TestService.MyBinder binder = (TestService.MyBinder) service;
        testService = binder.getService();
        isBound = true;
    }

    @Override
    public void onServiceDisconnected(ComponentName name) {
        testService = null;
        isBound = false;
    }
};```

* This way the binding client gets notify when the binding is established or disconnected.

* the binding connection can be observed. The last argument `(flags)` passed to `bindService()` defines the operation options.

* Now that you’ve the testService object, you can call important methods like `methodOne()` and `methodTwo()`

* Note this execution happens on the thread of the calling client. Hence if it’s the UI thread, long running operations should be prevented and a new Thread should be spawned to handle them in background in `methodOne()` and `methodTwo()`.


* Multiple clients can bind to the service, the system calls your Service’s `onBind()` only once when the first client binds to retrieve the `IBinder.` The system then won’t call `onBind()` again when any additional clients bind. Instead it’ll deliver the same `IBinder.`
````
