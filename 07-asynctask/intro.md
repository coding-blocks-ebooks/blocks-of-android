# Async Tasks

The concept of Async Tasks is similar to the concept of Threads in Java. Like in java, we can create multiple threads and other than that there is a main thread on which runs the majority of the program. Similarly, in android there is a UI thread(user interface thread) on which most of the operations like clicking, scrolling etc. are done and the tasks that take some time to finish are performed on some other thread so that the UI thread is not blocked.

Hence, Async task(asynchronus tasks) come in use when the operations like fetching data from server or downloading a file are to performed. These tasks are problematic not only because if performed on the main thread, it will block the thread and no other operation can be done but also, if the task takes more than 5 seconds to complete then that application will crash and will report **ApplicationNotResponding**.

Thus to prevent this, these tasks should be performed using async task which will perform the operation on a background thread and the result will be published on the main UI thread. However, async tasks should be used only to perform small tasks ( the ones which take only a few seconds to compute).

For implementation of the Asynchronus tasks, an inner class has to be created in the activity which inherits the **AsyncTask** class.

## Async Task Generic Types
The three types used by an asynchronous task are the following:
- **Params**, the type of the parameters sent to the task upon execution.
-	**Progress**, the type of the progress units published during the background computation.
-	**Result**, the type of the result of the background computation.

Not all types are always used by an asynchronous task. To mark a type as unused, simply use the type Void.
```
Private class MyTask extends AsyncTask<Void, Void, Void> {…}
```

Now there are four methods that are to defined, however only one of them (doInBackground) is compulsory. The four methods are as follows:
1.	**onPreExecute():**
This method runs on the UI thread and is used primarily for setting up the task like displaying the progress bar.

2.	**doInBackground(Params..):**
This method takes in an array of Parameters that is used to begin the task like the url of the image to be downloaded or the url from which the data is to be fetched.
It runs on the background thread.
It begins as soon as the execution of the `onPreExecute()` is over.

3.	**onProgressUpdate(Progress..):**
This method too runs on the UI thread and is used to diplay the progress of the task. For example, it is used to show what percent of the file has been downloaded in the form of a progress bar.
The parameter Progress are passed from the `doInBackground()` method. 

4.	**onPostExecute(Result..):**
This method also runs on the UI thread and starts after the execution of the `doInBackground()` method is completed.The result is passed on to this method as a parameter from the doInBackground method.
It is used to publish the result of the doInBackground method on the UI thread.

However, the point to be noted is that none of the four methods mentioned above, should be called directly from the main thread.

## Execution Flow
All you have to do is to call the ```execute()``` method on the object of the inner class from the main thread.
The execute method then calls the ```onPreExecute()``` on the UI thread **automatically**.
Next, the ```doInBackground method``` is called (again **automatically**) on the background thread.
From the doInBackground, you have to call the ```PublishProgress()``` method **manually** which further calls the ```OnProgressUpdate()``` on the UI thread.
Finally after the completion of the doInBackground method, the ```PostExecute``` method is called(automatically) and after its execution, the control is finally passed back to the UI thread.

## Cancelling the task
To cancel the task just invoke ```cancel(Boolean)```. Invoking this method will cause subsequent calls to ```isCancelled()``` to return true. After invoking this method, onCancelled(Object) will be invoked instead of ```onPostExecute(Object)``` after the execution of ```doInBackground(Object[])``` is over

## Code

Create a class for the task to be performed: 

```
internal class NSecWaitTask(private val tvToChange: TextView) : AsyncTask<Int, Float, String>() {

    override fun onPreExecute() {
        tvToChange.text = "Starting"
        super.onPreExecute()
    }

    override fun doInBackground(vararg params: Int?): String {
        for (i in 0 until params[0]!! * 2) {
            val startTime = System.currentTimeMillis()
            while (System.currentTimeMillis() < startTime + 500);
            publishProgress(i.toFloat() / 2)
        }
        return "Done"
    }

    override fun onProgressUpdate(vararg values: Float?) {
        tvToChange.text = values[0].toString()
        super.onProgressUpdate(*values)
    }

    override fun onPostExecute(s: String) {

        tvToChange.text = s
        super.onPostExecute(s)
    }
}
```

From the main thread, call the asynchronus task using the execute() method. The UI thread will not be blocked while the task gets completed on some other thread.
```
button!!.setOnClickListener {
            val myTask = NSecWaitTask(textView)
            myTask.execute(Integer.valueOf(editText!!.text.toString()))
        }

button2!!.setOnClickListener {
            textView.text = "Button2 still working..";
        }
```

   
