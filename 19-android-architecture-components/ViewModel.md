# ViewModel
The ``` ViewModel ``` class is designed to store and manage UI-related data in a lifecycle conscious way. The ``` ViewModel``` class allows data to survive configuration changes such as screen rotations.
The Android framework manages the lifecycles of UI controllers, such as activities and fragments. The framework may decide to destroy or re-create a UI controller in response to certain user actions or device events that are completely out of your control.

If the system destroys or re-creates a UI controller, any transient UI-related data you store in them is lost. For example, your app may include a list of users in one of its activities. When the activity is re-created for a configuration change, the new activity has to re-fetch the list of users. For simple data, the activity can use the ``` onSaveInstanceState() ``` method and restore its data from the bundle in ``` onCreate()```, but this approach is only suitable for small amounts of data that can be serialized then deserialized, not for potentially large amounts of data like a list of users or bitmaps.

Another problem is that UI controllers frequently need to make asynchronous calls that may take some time to return. The UI controller needs to manage these calls and ensure the system cleans them up after it's destroyed to avoid potential memory leaks. This management requires a lot of maintenance, and in the case where the object is re-created for a configuration change, it's a waste of resources since the object may have to reissue calls it has already made.

UI controllers such as activities and fragments are primarily intended to display UI data, react to user actions, or handle operating system communication, such as permission requests. Requiring UI controllers to also be responsible for loading data from a database or network adds bloat to the class. Assigning excessive responsibility to UI controllers can result in a single class that tries to handle all of an app's work by itself, instead of delegating work to other classes. Assigning excessive responsibility to the UI controllers in this way also makes testing a lot harder.
It's easier and more efficient to separate out view data ownership from UI controller logic.
#### Implement a ViewModel
Architecture Components provides `` ViewModel`` helper class for the UI controller that is responsible for preparing data for the UI. ``ViewModel`` objects are automatically retained during configuration changes so that data they hold is immediately available to the next activity or fragment instance. For example, if you need to display a list of users in your app, make sure to assign responsibility to acquire and keep the list of users to a `` ViewModel ``, instead of an activity or fragment, as illustrated by the following sample code:
```kotlin
class MyViewModel : ViewModel() {
    private lateinit var users: MutableLiveData<List<User>>

    fun getUsers(): LiveData<List<User>> {
        if (!::users.isInitialized) {
            users = MutableLiveData()
            loadUsers()
        }
        return users
    }

    private fun loadUsers() {
        // Do an asynchronous operation to fetch users.
    }
}
```
You can then access the list from an activity as follows:
```kotlin
class MyActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        // Create a ViewModel the first time the system calls an activity's onCreate() method.
        // Re-created activities receive the same MyViewModel instance created by the first activity.

        val model = ViewModelProviders.of(this).get(MyViewModel::class.java)
        model.getUsers().observe(this, Observer<List<User>>{ users ->
            // update UI
        })
    }
}
```
If the activity is re-created, it receives the same `` MyViewModel`` instance that was created by the first activity. When the owner activity is finished, the framework calls the `` ViewModel`` objects's `` onCleared()`` method so that it can clean up resources.
``ViewModel`` objects are designed to outlive specific instantiations of views or ``LifecycleOwners``. This design also means you can write tests to cover a ``ViewModel`` more easily as it doesn't know about view and ``Lifecycle`` objects. ``ViewModel`` objects can contain ``LifecycleObservers``, such as ``LiveData`` objects. However ``ViewModel`` objects must never observe changes to lifecycle-aware observables, such as ``LiveData`` objects. If the ``ViewModel`` needs the ``Application`` context, for example to find a system service, it can extend the ``AndroidViewModel`` class and have a constructor that receives the ``Application`` in the constructor, since ``Application`` class extends ``Context``.
#### The lifecycle of a ViewModel
``ViewModel`` objects are scoped to the ``Lifecycle`` passed to the ``ViewModelProvider`` when getting the ``ViewModel``. The ``ViewModel`` remains in memory until the ``Lifecycle`` it's scoped to goes away permanently: in the case of an activity, when it finishes, while in the case of a fragment, when it's detached.

Figure 1 illustrates the various lifecycle states of an activity as it undergoes a rotation and then is finished. The illustration also shows the lifetime of the ``ViewModel`` next to the associated activity lifecycle. This particular diagram illustrates the states of an activity. The same basic states apply to the lifecycle of a fragment.
You usually request a ``ViewModel`` the first time the system calls an activity object's onCreate() method. The system may call ``onCreate()`` several times throughout the life of an activity, such as when a device screen is rotated. The ``ViewModel`` exists from when you first request a ``ViewModel`` until the activity is finished and destroyed.
#### Share data between fragments
It's very common that two or more fragments in an activity need to communicate with each other. Imagine a common case of master-detail fragments, where you have a fragment in which the user selects an item from a list and another fragment that displays the contents of the selected item. This case is never trivial as both fragments need to define some interface description, and the owner activity must bind the two together. In addition, both fragments must handle the scenario where the other fragment is not yet created or visible.

This common pain point can be addressed by using ``ViewModel`` objects. These fragments can share a ``ViewModel`` using their activity scope to handle this communication, as illustrated by the following sample code:
``` kotlin
class SharedViewModel : ViewModel() {
    val selected = MutableLiveData<Item>()

    fun select(item: Item) {
        selected.value = item
    }
}

class MasterFragment : Fragment() {

    private lateinit var itemSelector: Selector

    private lateinit var model: SharedViewModel

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        model = activity?.run {
            ViewModelProviders.of(this).get(SharedViewModel::class.java)
        } ?: throw Exception("Invalid Activity")
        itemSelector.setOnClickListener { item ->
            // Update the UI
        }
    }
}

class DetailFragment : Fragment() {

    private lateinit var model: SharedViewModel

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        model = activity?.run {
            ViewModelProviders.of(this).get(SharedViewModel::class.java)
        } ?: throw Exception("Invalid Activity")
        model.selected.observe(this, Observer<Item> { item ->
            // Update the UI
        })
    }
}
```
Notice that both fragments retrieve the activity that contains them. That way, when the fragments each get the `` ViewModelProvider``, they receive the same ``SharedViewModel`` instance, which is scoped to this activity.
This approach offers the following benefits:
* The activity does not need to do anything, or know anything about this communication.
* Fragments don't need to know about each other besides the SharedViewModel contract. If one of the fragments disappears, the other one keeps working as usual.
* Each fragment has its own lifecycle, and is not affected by the lifecycle of the other one. If one fragment replaces the other one, the UI continues to work without any problems.

#### Replacing Loaders with ViewModel
Loader classes like ``CursorLoader`` are frequently used to keep the data in an app's UI in sync with a database. You can use ``ViewModel``, with a few other classes, to replace the loader. Using a ``ViewModel`` separates your UI controller from the data-loading operation, which means you have fewer strong references between classes.

In one common approach to using loaders, an app might use a ``CursorLoader`` to observe the contents of a database. When a value in the database changes, the loader automatically triggers a reload of the data and updates the UI. ``ViewModel`` works with Room and LiveData to replace the loader. The ``ViewModel`` ensures that the data survives a device configuration change. Room informs your ``LiveData`` when the database changes, and the LiveData, in turn, updates your UI with the revised data.
