# LiveData

## The need for LiveData
It is a common scenario that the activity or fragment has to observe some data and whenever the data changes, the application has to update itself. Also, sharing of resources among activities or fragments is done very often. For example, consider location, it will be same for all the activities yet separate listeners have to be attached to each activity or as an alternate option they are passed via the intents. This whole procedure is very cumbersome and unnecessary. Therefore, what comes here to our rescue is LiveData.

## What is LiveData??
It is an observable data holder class from <i>lifecycle</i> library and the best part about this is that it is lifecycle aware, like for example if certain data is being observed then you don't really need to unsubscribe from it when lifecycle changes state beacuse the subscriptions are automatically handled by the LiveData. In other words, it makes the right things happen at the right time.
- While the activity is in start or resume state, the data is regulary updated.
- When the lifecycle is destroyed, the observer is removed.
- When the activity is restarted, the up-to-date data is provided to the observer.

The benefits of using LiveData are as follows:
- <b> Easy sharing of resources </b> 
Only one LiveData object (that is connected to system service) can be used by all the observers to watch out for the data related to that particular service.
- <b> Safe from memory leaks </b>
The observers of the liveData perform an automatic cleanup after their associated lifecycles are destroyed.
- <b> Automatic subscriptions </b>
As mentioned above, the observer need not subscribe and unsubscribe. LiveData is aware of relevant lifecycle status changes while observing, so it automatically manages stopping and resuming observation.
- <b> Automatic handling of configuration changes </b>
When the screen is rotated, the activity is created again but the rotated activity or fragment receives the updated data immediately as soon as lifecycl enters the start state.
- <b> Updated UI </b>
Whenever the state of lifecycle is changed, the observer is notified. Therefore the code can be made such that UI is updated whenever the state of data is changed, instead of updating it with the change of data.

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

- For observation of data, create an observer of the data in the onCreate() method of MainActivity and override the observer's onChanged() method. onChanged() is executed when either the value of data is changed or the state of observer changes.

```
class NameActivity : AppCompatActivity() {

    private lateinit var mModel: SampleViewModel

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // Other code to setup the activity...

        // Get the ViewModel.
        mModel = ViewModelProviders.of(this).get(SampleViewModel::class.java)


        // Create the observer which updates the UI.
        val scoreObserver = Observer<Integer> { newName ->
            // Update the UI, in this case, a TextView.
            mScoreTextView.text = currentScore
        }

        // Observe the LiveData, passing in this activity as the LifecycleOwner and the observer.
        mModel.currentScore.observe(this, scoreObserver)
    }
}
```

- As mentioned above, the setValue(T) and postValue(T) methods of MutableLiveData class are used to edit the value stored in the LiveData object.
After correctly setting up the observer relationship, the value of the LiveData object can be updated, as illustrated by the following example, which triggers all observers when the user taps a button:

```
mButton.setOnClickListener {
    val newScore = 15
    mModel.currentScore.setValue(newScore)
}
```
The setValue(T) method can be invoked for a variety of reasons, like button press as shown in the example or a network request or anything else. The point to be noted here is that in all the cases,the call to setValue() or postValue() triggers observers and updates the UI.

## Custom LiveData class

If you wish to observe and manipulate the changes in data, then in that case a new class will have to be craeted that extends the LiveData class.
In the custom class, onActive() and onInactive() functions will be used to handle the cases of availabilty as well as inavailabilty of the LifecycleOwner.
- onActive() — It is invoked when the observer's lifecycle is in Start or Resume state or in other words the method is invoked when an active observer is available.
- onInactive() — Similarly, this method is called when the LiveData does not have any active observers. Since no observers are listening, therefore changes in data aren't observed.
- setValue() — This method updates the value of LiveData and if the observer's lifecycle is in start or resume state then they are notified too. If the observers aren't active then as soon as the observer enters the active state, then it is notified immediately.

Following is a custom LiveData class which observes for changes in the file and notifies on any change in the file content. We use FileObserver API to observe for file changes.

```
class FileLiveData(private val context: Context) : LiveData<List<String>>() {
    private val fileObserver: FileObserver

    init {
        val path = File(context.filesDir, "users.txt").path
        fileObserver = object : FileObserver(path) {
            override fun onEvent(event: Int, path: String?) {
                fetchData()
            }
        }
        Data()
    }

    override fun onActive() {
        fileObserver.startWatching()
    }

    override fun onInactive() {
        fileObserver.stopWatching()
    }

    @SuppressLint("StaticFieldLeak")
    private fun fetchData() {
        object : AsyncTask<Void, Void, List<String>>() {
            override fun doInBackground(vararg voids: Void): List<String> {
                val file = File(context.filesDir, "data.json")
                return file.readLines()
            }

            override fun onPostExecute(data: List<String>) {
                setValue(data)
            }
        }.execute()
    }
}
```





