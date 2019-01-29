# LiveData

## The need for LiveData
It is a common scenario that the activity or fragment has to observe some data and whenever the data changes, the application has to update itself. Also, the sharing of resources among activities or fragments is tough for example, consider location, it will be same for all the activities yet separate listeners have to be attached to each activity or as an alternate option they are passed via the intents. However, this whole procedure is cumbersome and unnecessary. Here live data comes to our rescue.

## What is live data??
It is an observable data holder class from <i>lifecycle</i> library and the best part about this is that it is lifecycle aware, like for example if certain data is being observed then you don't really need to unsubscribe from it beacuse the subscriptions are automatically handled by the live data. In other words, it makes the right things to happen at the right time.
- When the activity is in start or resume state, the data is regulary updated.
- When the lifecycle is destroyed, the observer is removed.
- When the activity is restarted, the up-to-data is provided to the observer.

The benefits of using LiveData are as follows:
- <b> Easy sharing of resources </b> 
Only one LiveData object (that is connected to system service) can be used by all the observers to watch out for the data realted to that particular service.
- <b> Safe from memory leaks </b>
The observers of the liveData perform an automatic cleanup after their associated lifecycles are destroyed.
- <b> Automatic subscriptions </b>
As mentioned above, the observer need not subscribe and unsubscribe. LiveData is aware of relevant lifecycle status changes while observing, so LiveData automatically manages stopping and resuming observation.
- <b> Automatic handling of configuration changes </b>
When the screen is rotated, the activity is created again but the rotated activity or fragment, receives the updated data immediately.
- <b> Updated UI </b>
Whenever the state of lifecycle is changed, the observer is notified. Therefore the code be made such that UI is updated whenever the state of data is changed, instead of updating it with the change in data.

## Implementation
- Create an instance of LiveData object. Usually liveData object is stored in the object of ViewModel class and the getter method is used to extract the data

```
class SampleViewModel : ViewModel() {

    val currentScore: MutableLiveData<Integer> by lazy {
        MutableLiveData<Integer>()
    }

    // Rest of the ViewModel...
}
```
Note: MutableLiveData is used beacuse LiveData has no methods for updating the stored values. The MutableLiveData class has two extra public methods using which updation of data can be done: setValue(T) and postValue(T).

- 



